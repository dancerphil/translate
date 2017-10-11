# React Router v4 非官方迁移指南

> 这是一篇译文，BY 张振衣([dancerphil@github](https://github.com/dancerphil/trick/))，[原文](https://codeburst.io/react-router-v4-unofficial-迁移-guide-5a370b8905a)，2017-10-09
>
> 除非另行注明，页面上所有内容采用[MIT](#license)协议共享

![](https://cdn-images-1.medium.com/max/2000/1*5XL_I8smdz2JylMeDOO3Xg.jpeg)

> React-router v4 提供了更简单快速的的路由，但是你是不是有能力升级呢？

[React-Router v4](https://reacttraining.com/react-router/web/guides/quick-start) 在 v3 版本的基础上引入了一系列激进的改动：现在，`<Route>` components 是 _real_ React components。只是一个巨大的成果，让 React 应用的路由变得十分简单。

不幸的是，react-router v4 改变了创建和嵌套路由的方式，现有的 react-router v3 应用在 v4 的框架下是 __不能工作__ 的。你当然可以升级现有的 app，但是并不能做到 straightforward。我发现. I discovered this hard truth by upgrading [admin-on-rest](https://github.com/marmelab/admin-on-rest), the React admin framework for REST services, to react-router v4. 由于没有官方的迁移指南（更新： [有一个不完整的指南](https://github.com/ReactTraining/react-router/blob/master/packages/react-router/docs/guides/migrating.md)，但它被隐藏了），我决定分享我个人的经验，我的迁移路径和一些实际建议。

__Note__: 对于一个中等规模的 react 应用程序，你需要至少一天的时间来进行迁移。

下面的列表列出了你所需要完成的工作：

- [把 `react-router` 改为 `react-router-dom`](#1)

- [使用特殊化的 Router 组件](#2)

- [为 Routes 列表添加 `<Switch>`](#3)

- [在 Routes 上添加 `exact` 属性](#4)

- [不再使用嵌套路由Replace Nested Routes By Routes In Child Components](#5)

- [Replace `<IndexRoute>` by a regular `<Route>`](#6)

- [Replace the `params` props by `match.params`](#7)

- [Parse The Query String Yourself](#8)

- [Replace `onEnter` with `componentWillMount`](#9)

- [Redo Redux Integration](#10)

As a bonus:

- [You Can Pass Props To Route Components](#11)

- [Custom Route Components Are Now A Piece Of Cake](#12)

<a name="1"></a>
## react-router 包现在改名为 react-router-dom

主模块已经改名了，所以你需要卸载以前的包，并安装新包：

```
npm uninstall react-router --save
npm install react-router-dom --save
```

使用 `git grep react-router` 找到所有引用了旧包的代码，然后按照下面的格式替换 `import` 语句（或者`require()`）：

```
// from
import { Route, Redirect } from 'react-router';
// to
import { Route, Redirect } from 'react-router-dom';
```

<a name="2"></a>
## 使用特殊化的 Router 组件

`<Router>` 过去需要一个 `history` prop。而在 v4，你可以使用特殊化的 router 组件来代替，采取以下的步骤：

```
// v3
import { Router, hashHistory } from 'react-router';
const MyApp = () => (
    <Router history={hashHistory}>
        ...
    </Router>
);
// v4
import { HashRouter } from 'react-router-dom';
const MyApp = () => (
    <HashRouter>
        ...
    </HashRouter>
);
```

然而，如果你需要将你的 history 和一个状态管理库进行同步，比如 Redux（过后会有更多的讨论），那么你就必须要继续使用 `<Router>` 组件，并且传递一个 `history` 对象，但这一次，是从 `history` 库得到这个对象：

```
// v4
import { Router } from 'react-router-dom';
import createHashHistory from 'history/createHashHistory';
const history = createHashHistory();
const MyApp = () => (
    <Router history={history}>
        ...
    </Router>
);
```

<a name="3"></a>
## 为 Routes 列表添加 <Switch>

`<Route>` 组件不再具有排他性。这意味着即使有一个 route 符合当前的 url，也不会阻止它和其他的 route 组件进行匹配。

我们假设你定义了以下路由，v3 风格：

```
import { Router, Route } from 'react-router-dom';
const MyApp = () => (
    <Router history={history}>
        <Route path="/posts" component={PostList} />
        <Route path="/posts/:id" component={PostEdit} />
        <Route path="/posts/:id/show" component={PostShow} />
        <Route path="/posts/:id/delete" component={PostDelete} />
    </Router>
)
```

在 v4，类似 `/posts/12/show` 的路径会同时触发前三个 route ！你需要用一个 `<Switch>` 组件包裹住 `<Routes>` 列表，来防止一个集合中的多个路由匹配：

```
import { Router, Route, Switch } from 'react-router-dom';
const MyApp = () => (
    <Router history={history}>
        <Switch>
            <Route path="/posts" component={PostList} />
            <Route path="/posts/:id" component={PostEdit} />
            <Route path="/posts/:id/show" component={PostShow} />
            <Route path="/posts/:id/delete" component={PostDelete} />
        </Switch>
    </Router>
)
```

<a name="4"></a>
## 在 Routes 上添加 exact 属性

但这还不够：按照这个顺序，对于路径 `/posts/12/show` 来说，被渲染的 route 会是第一个（因为 url 匹配了 `/posts` 的形式）。为了获得与 v3 相类似的行为，你需要在你的 route 上添加 exact 属性：

```
import { Router, Route, Switch } from 'react-router-dom';
const MyApp = () => (
    <Router history={history}>
        <Switch>
            <Route exact path="/posts" component={PostList} />
            <Route exact path="/posts/:id" component={PostEdit} />
            <Route exact path="/posts/:id/show" component={PostShow} />
            <Route exact path="/posts/:id/delete" component={PostDelete} />
        </Switch>
    </Router>
)
```

<a name="5"></a>
## 不再使用嵌套路由

React-router v3 曾经支持嵌套路由：

```
// in src/MyApp.js
const MyApp = () => (
    <Router history={history}>
        <Route path="/main" component={Layout}>
            <Route path="/foo" component={Foo} />
            <Route path="/bar" component={Bar} />
        </Route>
    </Router>
)
// in src/Layout.js
const Layout = ({ children }) => (
    <div className="body">
        <h1 className="title">MyApp</h1>
        <div className="content">
            {children}
        </div>
    </div>
)
```

在 `Layout` 组件中，`{children}` 将被子路由组件（`Foo` 或 `Bar`）所取代。

React-router v4 不再支持嵌套路由。你现在必须将嵌套路由放在子组件中：

```
// in src/MyApp.js
const MyApp = () => (
    <Router history={history}>
        <Route path="/main" component={Layout} />
    </Router>
)
// in src/Layout.js
const Layout = () => (
    <div className="body">
        <h1 className="title">MyApp</h1>
        <div className="content">
            <Switch>
                <Route path="/main/foo" component={Foo} />
                <Route path="/main/bar" component={Bar} />
            </Switch>
        </div>
    </div>
);
```

This is the greatest new feature of react-router v4: you can put `<Route>` components everywhere!

Notice that the `path` must always be absolute, even in `<Route>` components descendants of other routes. If you don’t want to repeat the top-level path, use the `match` prop (injected by react-router on routed components) to compose the path, as follows:

```
// in src/Layout.js
const Layout = ({ match }) => (
    <div className="body">
        <h1 className="title">MyApp</h1>
        <div className="content">
            <Switch>
                <Route path={`${match.url}/foo`} component={Foo} />
                <Route path={`${match.url}/foo`} component={Bar} />
            </Switch>
        </div>
    </div>
);
```

Support for relative routes is [in the works](https://github.com/ReactTraining/react-router/pull/4539).

<a name="6"></a>
## No More <IndexRoute>

The `<IndexRoute>` component allowed to route to a certain component on a top-level path in v3:

```
// in src/MyApp.js
const MyApp = () => (
    <Router history={history}>
        <Route path="/" component={Layout}>
            <IndexRoute component={Dashboard} />
            <Route path="/foo" component={Foo} />
            <Route path="/bar" component={Bar} />
        </Route>
    </Router>
)
```

This component doesn’t exist anymore in v4. To replace it, use a combination of `<Switch>`, `exact`, and route ordering (placing the index route last):

```
// in src/MyApp.js
const MyApp = () => {
    <Router history={history}>
        <Route path="/" component={Layout} />
    </Router>
}
// in src/Layout.js
const Layout = () => (
    <div className="body">
        <h1 className="title">MyApp</h1>
        <div className="content">
            <Switch>
                <Route exact path="/foo" component={Foo} />
                <Route exact path="/bar" component={Bar} />
                <Route exact path="/" component={Dashboard} />
            </Switch>
        </div>
    </div>
);
```

<a name="7"></a>
## Use match.params Instead Of params

You probably used the `params` prop in routed components to grab path parameters in v3:

```
// in src/MyApp.js
const MyApp = () => (
    <Router history={history}>
        <Route path="/posts/:id" component={PostEdit} />
    </Router>
)
// in src/PostEdit.js
const PostEdit = ({ params }) => (
    <div>
        <h1>Post #{params.id}</h1>
        ...
    </div>
)
```

This prop isn’t injected anymore in v4. You will find the same data in `match.params`.

```
// v4
const PostEdit = ({ match }) => (
    <div>
        <h1>Post #{match.params.id}</h1>
        ...
    </div>
)
```

Oh, and `params` aren’t decode by default in v4. Always use `decodeURIComponent` to handle special characters correctly.

<a name="8"></a>
## Parse The Query String Yourself

React-router v3 used to parse the query string by default, storing the query parameters as an object accessible in `location.query`. For instance, you could grab the `sort` query parameter in the `/posts?sort=foo` path as follows:

```
// in src/PostList.js
const PostList = ({ location }) => (
    <div>
        <h1>List sorted by {location.query.sort}</h1>
    </div>
);
```

Well, `location.query` doesn’t exist anymore in v4, and the query string isn’t parsed at all. You’ll have to parse `location.search` by hand, using a third-party library (like `query-string`).

```
import { parse } from 'query-string';
const PostList = ({ location }) => {
    const query = parse(location.search);
    return (
        <div>
            <h1>List sorted by {query.sort}</h1>
        </div>
    );
};
```

__Tip__: If you use Redux, and if you parse the query string in `mapStateToProps()`, it’s a good idea to use a library like [reselect](https://github.com/reactjs/reselect) to memoize the parsed query string, and avoid reparsing it on every state change.

<a name="9"></a>
## Replace onEnter with componentWillMount

The `onEnter` prop was commonly used to verify user credentials before every route.

```
// v3
// in src/MyApp.js
import checkCredentials from '../checkCredentials';
function redirectToLoginIfNotAuthenticated = (nextState, replace, callback) =>
    checkCredentials()
        .then(callback)
        .catch(e => replace({ pathname: '/login' })
<Route onEnter={redirectToLoginIfNotAuthenticated} component={PostList} />
```

`onEnter` doesn’t exist anymore in v4. To do a check before rendering a route, place the code in `componentWillMount` and `componentWillReceiveProps`:

```
// v4
// in src/PostList.js
import { withRouter } from 'react-router-dom';
import checkCredentials from '../checkCredentials';
class PostList extends Component {
    componentWillMount() {
        this.checkAuthentication(this.props);
    }
    componentWillReceiveProps(nextProps) {
        if (nextProps.location !== this.props.location) {
            this.checkAuthentication(nextProps);
        }
    }
    checkAuthentication(params) {
        const { history } = params;
        checkCredentials()
            .catch(e => history.replace({ pathname: '/login' }));
    }
    render() {
        // ...
    }
}
export withRouter(PostList);
// in src/MyApp.js
<Route component={PostList} />
```

But this forces you to use class components. Besides, repeating the `componentWillMount` logic in all the components you want to place behind a login is cumbersome.

A good way to refactor the authentication logic is to move it to another component, or to a higher-order component. The following HOC, called `restricted`, makes the `checkAuthentication` check on mount:

```
// in src/restricted.js
import React, { Component } from 'react';
import { withRouter } from 'react-router-dom';
import checkCredentials from '../checkCredentials';
/**
 * Higher-order component (HOC) to wrap restricted pages
 */
export function BaseComponent => {
    class Restricted extends Component {
        componentWillMount() {
            this.checkAuthentication(this.props);
        }
        componentWillReceiveProps(nextProps) {
            if (nextProps.location !== this.props.location) {
                this.checkAuthentication(nextProps);
            }
        }
        checkAuthentication(params) {
            const { history } = params;
            checkCredentials()
                .catch(e => history.replace({ pathname: '/login' }));
        }
        render() {
            return <BaseComponent {...this.props} />;
        }
    }
    return withRouter(Restricted);
}
```

Use this HOC as follows:

```
const FooPage = () => { ... };
<Route path="/foo" component={restricted(FooPage)} />
```

<a name="10"></a>
## Redux Integration

If your app uses Redux, you probably used [reactjs/react-router-redux](https://github.com/reactjs/react-router-redux). This library is no longer maintained, and [the react-training organization has taken over the maintenance](https://github.com/ReactTraining/react-router/tree/master/packages/react-router-redux) of the Redux binding for react-router v4.

So升级 your `package.json` to use `react-router-redux~5.0.0` (currently in alpha, but don’t worry), and update your code from:

```
import { combineReducers, createStore, compose, applyMiddleware } from 'redux';
import { Provider } from 'react-redux';
import { Router, Route, hashHistory } from 'react-router';
import { syncHistoryWithStore, routerMiddleware, routerReducer } from 'react-router-redux';
const history = syncHistoryWithStore(hashHistory, store)
const MyApp = () => {
    const reducer = combineReducers({
        ... // your reducers here
        routing: routerReducer,
    });
    const store = createStore(reducer, undefined, compose(
        applyMiddleware(sagaMiddleware, routerMiddleware(hashHistory)),
        window.devToolsExtension ? window.devToolsExtension() : f => f,
    ));
    return (
        <Provider store={store}>
            <Router history={history}>
                <Route ... />
            </Router>
        </Provider>
    );
}
```

To:

```
import { combineReducers, createStore, compose, applyMiddleware } from 'redux';
import { Provider } from 'react-redux';
import { Route, Switch } from 'react-router-dom';
import createHistory from 'history/createHashHistory';
import { ConnectedRouter, routerReducer, routerMiddleware } from 'react-router-redux';
const MyApp = () => {
    const reducer = combineReducers({
        ... // your reducers here
        routing: routerReducer,
    });
    const history = createHistory();
    const store = createStore(reducer, undefined, compose(
        applyMiddleware(routerMiddleware(history)),
        window.devToolsExtension ? window.devToolsExtension() : f => f,
    ));
    return (
        <Provider store={store}>
            <ConnectedRouter history={history}>
                <Switch>
                    <Route ... />
                </Switch>
            </ConnectedRouter>
        </Provider>
    );
};
```

The main differences are:

- Use `<ConnectedRouter>` instead of `<Router>`

- Pass the same `history` object to both the Redux middleware and the React Router component (no more `syncHistoryWithStore`)

Just as with the previous version, the Redux binding will give you Route actions that you can listen to in your actions and sagas.

Overall, the Redux integration is simplified in v4, and you don’t have to wonder which binding library to use — it’s now [part of react-router](https://github.com/ReactTraining/react-router/tree/master/packages/react-router-redux).

<a name="11"></a>
## You Can Pass Props To Route Components

In v3, in order to pass dynamic props to a route component, you had to use the `withProp` higher order component:

```
import withProps from 'recompose/withProps';
import Dashboard from './Dashboard';
const MyApp = ({ title }) => {
    const DashboardWithTitle = withProps(Dashboard, { title });
    return (
        <Router history={history}>
            <Route path="/" component={DashboardWithTitle} />
        </Router>
    );
}
```

Now you can use the `render` props to pass the props you want:

```
import Dashboard from './Dashboard';
const MyApp = ({ title }) => {
    return (
        <Router history={history}>
            <Route path="/" render={props =>
                <Dashboard title={title} {...props} />
            } />
        </Router>
    );
}
```

<a name="12"></a>
## Custom Route Components Are Now A Piece Of Cake

Creating a custom Route component (e.g. a <CrudRoute> component that would embed several routes to handle Creation, Retrieval, Update, and Deletion of a resource) was almost impossible with react-router v3. I managed to [find a workaround](https://marmelab.com/blog/2016/09/20/custom-react-router-component.html), but the trick felt a bit hacky. This was because the v3 router implementation was a bit hacky, too.

In v4, `<Route>` components are simple React components. So creating custom route components requires no special trick. Here is how the `<CrudRoute>` looks like in admin-on-rest:

```
import React, { createElement } from 'react';
import { Route, Switch } from 'react-router-dom';
const CrudRoute = ({ resource, list, create, edit, show, remove }) => {
    // inject the resource prop
    const ResourcePage = component => routeProps => createElement(component, {
        resource, ...routeProps })}
    return (
        <Switch>
            <Route
                exact
                path={`/${resource}`}
                render={ResourcePage(list)}
            />
            <Route
                exact
                path={`/${resource}/create`}
                render={ResourcePage(create)}
            />
            <Route
                exact
                path={`/${resource}/:id`}
                render={ResourcePage(edit)}
            />
            <Route
                exact
                path={`/${resource}/:id/show`}
                render={ResourcePage(show)}
            />
            <Route
                exact
                path={`/${resource}/:id/delete`}
                render={ResourcePage(remove)}
            />
        </Switch>
    );
};
export default CrudRoute;
```

## Conclusion

React-router v4 dramatically reduced the size of the router API, and that’s a great idea. By doing less things, the library does it better, and is easier to understand.

But that’s not a small change. Migrating an existing app to v4 will take at least a couple hours, up to a couple days. For instance, the admin-on-rest 迁移 took about 2 days ([not a small diff](https://github.com/marmelab/admin-on-rest/pull/513) for a library of about 8,000 loc). But once it’s done, the routing logic is much simpler, and the library allows for future improvements that weren’t possible with v2/v3.

I definitely recommend migrating to react-router v4. Kudos to [the react-training team](https://github.com/ReactTraining) for this great piece of software!
