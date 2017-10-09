https://codeburst.io/react-router-v4-unofficial-migration-guide-5a370b8905a
https://medium.com/ottofellercom/to-migrate-or-not-to-migrate-react-router-v4-fdaabb09e538

# React Router v4 Unofficial Migration Guide

![](https://cdn-images-1.medium.com/max/2000/1*5XL_I8smdz2JylMeDOO3Xg.jpeg)

> React-router v4 offer simple, faster routing. But can you upgrade?

[React-Router v4](https://reacttraining.com/react-router/web/guides/quick-start) introduces a radical change over version 3: now, `<Route>` components are _real_ React components. That’s a huge achievement, and it simplifies routing in React applications a lot.

Unfortunately, react-router v4 changes the way to create and nest routes. An existing react-router v3 application __will not work__ out of the box with v4. Upgrading an existing app is possible, but not straightforward. I discovered this hard truth by upgrading [admin-on-rest](https://github.com/marmelab/admin-on-rest), the React admin framework for REST services, to react-router v4. Since there is no official migration guide (update: [there is an incomplete guide](https://github.com/ReactTraining/react-router/blob/master/packages/react-router/docs/guides/migrating.md), but it’s hidden), I decided to share my experience, the migration path I found, and practical advice.

__Note__: For a medium-size react application, you will need at least a day to migrate.

Here is a list of the things you’ll need to do to migrate:

- [Change `react-router` to `react-router-dom`](#1)

- [Use Specialized Router Components](#2)

- [Add `<Switch>` to List of Routes](#3)

- [Add `exact` to Routes](#4)

- [Replace Nested Routes By Routes In Child Components](#5)

- [Replace `<IndexRoute>` by a regular `<Route>`](#6)

- [Replace the `params` props by `match.params`](#7)

- [Parse The Query String Yourself](#8)

- [Replace `onEnter` with `componentWillMount`](#9)

- [Redo Redux Integration](#10)

As a bonus:

- [You Can Pass Props To Route Components](#11)

- [Custom Route Components Are Now A Piece Of Cake](#12)

<a name="1"></a>
## The react-router package is now react-router-dom

The main module name has changed. So uninstall the previous package and install the new one:

```
npm uninstall react-router --save
npm install react-router-dom --save
```

Use `git grep react-router` to find all the scripts depending on that package, and replace the `import` statements (or `require()`) as follows:

```
// from
import { Route, Redirect } from 'react-router';
// to
import { Route, Redirect } from 'react-router-dom';
```

<a name="2"></a>
## Use Specialized Router Components

`<Router>` used to require a `history` prop. With v4, specialized router components take this step out:

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

However, if you need to synchronize the history with a state management lib like Redux (more on that later), you have to keep using the `<Router>` component, and pass a `history` object coming, this time, from the `history` package:

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
## Add <Switch> to List of Routes

`<Route>` components are no longer exclusive. That means that even if one route matches the current url, nothing prevents a sibling route component from matching, too.
Let’s suppose you defined the following routes, v3-style:

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

With v4, a path like `/posts/12/show` will trigger the first three routes! You need to add a `<Switch>` component around the list of `<Routes>` to avoid multiple route matches in a set:

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
## Add the exact prop to Routes

But this is not enough: in that order, the route rendered for the `/posts/12/show` path will be the first one (because the url matches the `/posts` pattern). To get a v3-like behavior, you must add the `exact` prop to your routes:

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
## No More Nested Routes

React-router v3 used to support nested routes:

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

In the `Layout` component, `{children}` would be replaced by the child route component (`Foo` or `Bar`).

React-router v4 doesn’t support nested routes anymore. You must now put nested routes inside child components:

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

So upgrade your `package.json` to use `react-router-redux~5.0.0` (currently in alpha, but don’t worry), and update your code from:

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

But that’s not a small change. Migrating an existing app to v4 will take at least a couple hours, up to a couple days. For instance, the admin-on-rest migration took about 2 days ([not a small diff](https://github.com/marmelab/admin-on-rest/pull/513) for a library of about 8,000 loc). But once it’s done, the routing logic is much simpler, and the library allows for future improvements that weren’t possible with v2/v3.

I definitely recommend migrating to react-router v4. Kudos to [the react-training team](https://github.com/ReactTraining) for this great piece of software!
