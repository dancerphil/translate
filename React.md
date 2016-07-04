# React/JSX 代码风格指南

*如何用最合理的方式写你的 React/JSX 代码*

> BY 张聪([dancerphil@github](https://github.com/dancerphil/trick/))
> 
> 这是一篇在[原文](https://github.com/airbnb/javascript/tree/master/react)基础上演绎的译文，与原文的表达会有出入。
> 
> 原文在不断的更新，本文基于 2016-07-03 的版本，last commit [ [36d1561] ](https://github.com/airbnb/javascript/commit/36d15615968e267d11f6d2be509e5625900a7e6c)
> 
> 除非另行注明，页面上所有内容采用 [MIT](https://opensource.org/licenses/MIT) 协议共享。

<a name="table-of-contents"></a>
## 目录

  1. [基本规则 Basic Rules](#basic-rules)
  1. [类， `createClass` 与无状态 Class vs `React.createClass` vs stateless](#class-vs-reactcreateclass-vs-stateless)
  1. [命名 Naming](#naming)
  1. [声明 Declaration](#declaration)
  1. [对齐 Alignment](#alignment)
  1. [引号 Quotes](#quotes)
  1. [空格 Spacing](#spacing)
  1. [属性 Props](#props)
  1. [引用 Refs](#refs)
  1. [括号 Parentheses](#parentheses)
  1. [标签 Tags](#tags)
  1. [方法 Methods](#methods)
  1. ~~Ordering~~
  1. [`isMounted`](#ismounted)

<a name="basic-rules"></a>
## 基本规则

  - 每个文件只包含一个 React 组件
    - 不过一个文件可以包含多个 [无状态(stateless) 组件或纯净(pure) 组件](https://facebook.github.io/react/docs/reusable-components.html#stateless-functions) 。 eslint: [`react/no-multi-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-multi-comp.md#ignorestateless).
  - 总是使用 JSX 语法
  - 不要使用 `React.createElement` ，除非你正在用一个非 JSX 文件初始化你的 app

<a name="class-vs-reactcreateclass-vs-stateless"></a>
## 类， `createClass` 与无状态

  - 如果你有内部的状态或引用(reference)，选择使用 `class extends React.Component` ，而不是 `React.createClass` ，除非你有一个非常好的理由，否则不要使用 mixin。 eslint: [`react/prefer-es6-class`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/prefer-es6-class.md) [`react/prefer-stateless-function`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/prefer-stateless-function.md)

    ```jsx
    // 差评
    const Listing = React.createClass({
      // ...
      render() {
        return <div>{this.state.hello}</div>;
      }
    });

    // 好评
    class Listing extends React.Component {
      // ...
      render() {
        return <div>{this.state.hello}</div>;
      }
    }
    ```

    如果你不需要内部状态和引用，选择使用一般的函数，而不是箭头函数，也不是 class：

    ```jsx
    // 差评
    class Listing extends React.Component {
      render() {
        return <div>{this.props.hello}</div>;
      }
    }

    // 差评 (relying on function name inference is discouraged)
    const Listing = ({ hello }) => (
      <div>{hello}</div>
    );

    // 好评
    function Listing({ hello }) {
      return <div>{hello}</div>;
    }
    ```

<a name="naming"></a>
## 命名

  - **后缀名**： 写 React 组件应当使用后缀名 `.jsx`
  - **文件名**： 使用帕斯卡命名法，比如 `ReservationCard.jsx`.
  - **引用命名**： 使用帕斯卡命名法命名组件，用驼峰命名法命名组件的实例。 eslint: [`react/jsx-pascal-case`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-pascal-case.md)

    ```jsx
    // 差评
    import reservationCard from './ReservationCard';

    // 好评
    import ReservationCard from './ReservationCard';

    // 差评
    const ReservationItem = <ReservationCard />;

    // 好评
    const reservationItem = <ReservationCard />;
    ```

  - **组件命名**： 文件名和组件名保持相同。比如， `ReservationCard.jsx` 应当有一个名为 `ReservationCard` 的引用。然而对于一个某个目录的根组件来说，使用 `index.jsx` 作为文件名，然后用目录名称作为组件名：
    ```jsx
    // 差评
    import Footer from './Footer/Footer';

    // 差评
    import Footer from './Footer/index';

    // 好评
    import Footer from './Footer';
    ```

<a name="declaration"></a>
## 声明 Declaration

  - 不要使用 `displayName` 来命名组件，而使用参考(reference)命名组件

    ```jsx
    // 差评
    export default React.createClass({
      displayName: 'ReservationCard',
      // stuff goes here
    });

    // 好评
    export default class ReservationCard extends React.Component {
    }
    ```

<a name="alignment"></a>
## 对齐 Alignment

  - JSX 语法遵循以下对齐风格。 eslint: [`react/jsx-closing-bracket-location`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-closing-bracket-location.md)

    ```jsx
    // 差评
    <Foo superLongParam="bar"
         anotherSuperLongParam="baz" />

    // 好评
    <Foo
      superLongParam="bar"
      anotherSuperLongParam="baz"
    />

    // 如果属性(props)适合写在同一行上，就把他们写在一行
    <Foo bar="bar" />

    // 通常子元素需要缩进
    <Foo
      superLongParam="bar"
      anotherSuperLongParam="baz"
    >
      <Quux />
    </Foo>
    ```

<a name="quotes"></a>
## 引号 Quotes

  - 书写 JSX 属性值时总是使用双引号(`"`)，但是对于其他所有的 JS 代码都使用单引号。 eslint: [`jsx-quotes`](http://eslint.org/docs/rules/jsx-quotes)

  > 为什么？ JSX 属性 [不能包括转译的引号](http://eslint.org/docs/rules/jsx-quotes) ，在双引号里类似 `"don't"` 的属性值更容易输入。
  > 通常的 HTML 属性使用双引号， JSX 属性遵循了相同的语法

    ```jsx
    // 差评
    <Foo bar='bar' />

    // 好评
    <Foo bar="bar" />

    // 差评
    <Foo style={{ left: "20px" }} />

    // 好评
    <Foo style={{ left: '20px' }} />
    ```

<a name="spacing"></a>
## 空格 Spacing

  - 在你的自闭合标签里包含一个空格

    ```jsx
    // 差评
    <Foo/>

    // very bad
    <Foo                 />

    // 差评
    <Foo
     />

    // 好评
    <Foo />
    ```

  - 不要在 JSX 大括号内垫上空格。 eslint: [`react/jsx-curly-spacing`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-curly-spacing.md)

    ```jsx
    // 差评
    <Foo bar={ baz } />

    // 好评
    <Foo bar={baz} />
    ```

<a name="props"></a>
## 属性 Props

  - 永远使用驼峰命名法命名属性名

    ```jsx
    // 差评
    <Foo
      UserName="hello"
      phone_number={12345678}
    />

    // 好评
    <Foo
      userName="hello"
      phoneNumber={12345678}
    />
    ```

  - 如果一个属性值是明确的 `true` ，把值省略不写。 eslint: [`react/jsx-boolean-value`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-boolean-value.md)

    ```jsx
    // 差评
    <Foo
      hidden={true}
    />

    // 好评
    <Foo
      hidden
    />
    ```

  - `<img>` 标签应当永远包括 `alt` 属性。除非图片是介绍性的(presentational), `alt` 可以为空字符串或者 `<img>` 必须包含 `role="presentation"` 。 eslint: [`jsx-a11y/img-has-alt`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/img-has-alt.md)

    ```jsx
    // 差评
    <img src="hello.jpg" />

    // 好评
    <img src="hello.jpg" alt="Me waving hello" />

    // 好评
    <img src="hello.jpg" alt="" />

    // 好评
    <img src="hello.jpg" role="presentation" />
    ```

  - 在 `<img>` `alt` 属性里不要使用类似 "图像 image"， "照片 photo"，或者 "图片 picture" 这样的词语。 eslint: [`jsx-a11y/img-redundant-alt`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/img-redundant-alt.md)

  > 为什么？屏幕(Screenreaders)已经把 `img` 标签标注为图片了, 所以没有必要再在 alt 里说明这些信息

    ```jsx
    // 差评
    <img src="hello.jpg" alt="Picture of me waving hello" />

    // 好评
    <img src="hello.jpg" alt="Me waving hello" />
    ```

  - 使用有效的，非抽象的 [ARIA roles](https://www.w3.org/TR/wai-aria/roles#role_definitions) 属性值。 eslint: [`jsx-a11y/aria-role`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/aria-role.md)

    ```jsx
    // 差评 - not an ARIA role
    <div role="datepicker" />

    // 差评 - abstract ARIA role
    <div role="range" />

    // 好评
    <div role="button" />
    ```

  - 不要在元素上添加使用 `accessKey` 属性。 eslint: [`jsx-a11y/no-access-key`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/no-access-key.md)

  > 为什么？如果使用屏幕和键盘的人所用的键盘命令与键盘快捷键不相符合，会引起访问不一致性。

  ```jsx
  // 差评
  <div accessKey="h" />

  // 好评
  <div />
  ```

  - 不要使用数组的 `index` 作为属性 `key` 的值，使用一个 unique ID。

  > [为什么？](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318)

  ```jsx
  // bad
  {todos.map((todo, index) =>
    <Todo
      {...todo}
      key={index}
    />
  )}

  // good
  {todos.map(todo => (
    <Todo
      {...todo}
      key={todo.id}
    />
  ))}
  ```

<a name="refs"></a>
## 引用 Refs

  - 总是使用引用回调(ref callbacks)。 eslint: [`react/no-string-refs`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-string-refs.md)

  > 译注：引用一个组件 React 支持以下的两种方式，但是第一种是过时的向前兼容

    ```jsx
    // 差评
    <Foo
      ref="myRef"
    />

    // 好评
    <Foo
      ref={(ref) => this.myRef = ref}
    />
    ```

<a name="parentheses"></a>
## 括号 Parentheses

  - 把 JSX 标签写在括号 `()` 里，如果他们超过一行。 eslint: [`react/wrap-multilines`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/wrap-multilines.md)

    ```jsx
    // 差评
    render() {
      return <MyComponent className="long body" foo="bar">
               <MyChild />
             </MyComponent>;
    }

    // 好评
    render() {
      return (
        <MyComponent className="long body" foo="bar">
          <MyChild />
        </MyComponent>
      );
    }

    // 好评, when single line
    render() {
      const body = <div>hello</div>;
      return <MyComponent>{body}</MyComponent>;
    }
    ```

<a name="tags"></a>
## 标签 Tags

  - 总是自闭合(self-close)那些没有子元素的标签。 eslint: [`react/self-closing-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/self-closing-comp.md)

    ```jsx
    // 差评
    <Foo className="stuff"></Foo>

    // 好评
    <Foo className="stuff" />
    ```

  - 如果你的组件有多行属性，新起一行关闭标签。 eslint: [`react/jsx-closing-bracket-location`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-closing-bracket-location.md)

    ```jsx
    // 差评
    <Foo
      bar="bar"
      baz="baz" />

    // 好评
    <Foo
      bar="bar"
      baz="baz"
    />
    ```

<a name="methods"></a>
## 方法 Methods

  - 使用箭头函数获取本地变量。

    ```jsx
    function ItemList(props) {
      return (
        <ul>
          {props.items.map((item, index) => (
            <Item
              key={item.key}
              onClick={() => doSomethingWith(item.name, index)}
            />
          ))}
        </ul>
      );
    }
    ```

  - 在构造函数中就把 render 方法中需要的事件手柄绑定。 eslint: [`react/jsx-no-bind`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-no-bind.md)

  > 为什么？每一次 render 过程中，bind 调用都会创建一个全新的函数

    ```jsx
    // 差评
    class extends React.Component {
      onClickDiv() {
        // do stuff
      }

      render() {
        return <div onClick={this.onClickDiv.bind(this)} />
      }
    }

    // 好评
    class extends React.Component {
      constructor(props) {
        super(props);

        this.onClickDiv = this.onClickDiv.bind(this);
      }

      onClickDiv() {
        // do stuff
      }

      render() {
        return <div onClick={this.onClickDiv} />
      }
    }
    ```

  - 不要给内部函数使用下划线前缀 `_` ，他们实际上并不是私有的

    ```jsx
    // 差评
    React.createClass({
      _onClickSubmit() {
        // do stuff
      },

      // other stuff
    });

    // 好评
    class extends React.Component {
      onClickSubmit() {
        // do stuff
      }

      // other stuff
    }
    ```

  - 保证在你的 `render` 方法中返回值。 eslint: [`require-render-return`](https://github.com/yannickcr/eslint-plugin-react/pull/502)

    ```jsx
    // 差评
    render() {
      (<div />);
    }

    // 好评
    render() {
      return (<div />);
    }
    ```

<a name="ismounted"></a>
## `isMounted`

  - 不要使用 `isMounted`。 eslint: [`react/no-is-mounted`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-is-mounted.md)

  > 为什么？ [`isMounted` 是反模式(anti-pattern)][anti-pattern]，ES6 classes 中将不再使用它，此方法将被官方逐步移除。

  [anti-pattern]: https://facebook.github.io/react/blog/2015/12/16/ismounted-antipattern.html

**[↑ 回到最上方](#目录)**
