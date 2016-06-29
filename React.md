# React/JSX 代码风格指南

*如何用最合理的方式写你的 React/JSX 代码*

> BY 张聪([dancerphil@github](https://github.com/dancerphil/trick/))
> 
> 这是一篇在[原文](https://github.com/airbnb/javascript/tree/master/react)基础上演绎的译文，与原文的表达会有出入。
> 
> 原文在不断的更新，本文基于 2016-04-28 的版本，last commit [ [a71bffa] ](https://github.com/airbnb/javascript/commit/a71bffa5e3515c83023595e2716fee3bc75da7e5)
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
  1. [Spacing](#spacing)
  1. [Props](#props)
  1. [Parentheses](#parentheses)
  1. [标签 Tags](#tags)
  1. [方法 Methods](#methods)
  1. [排序 Ordering](#ordering)
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

  - Always use double quotes (`"`) for JSX attributes, but single quotes for all other JS. eslint: [`jsx-quotes`](http://eslint.org/docs/rules/jsx-quotes)

  > Why? JSX attributes [can't contain escaped quotes](http://eslint.org/docs/rules/jsx-quotes), so double quotes make conjunctions like `"don't"` easier to type.
  > Regular HTML attributes also typically use double quotes instead of single, so JSX attributes mirror this convention.

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
## Spacing

  - Always include a single space in your self-closing tag.

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

  - Do not pad JSX curly braces with spaces. eslint: [`react/jsx-curly-spacing`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-curly-spacing.md)

    ```jsx
    // 差评
    <Foo bar={ baz } />

    // 好评
    <Foo bar={baz} />
    ```

<a name="props"></a>
## Props

  - Always use camelCase for prop names.

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

  - Omit the value of the prop when it is explicitly `true`. eslint: [`react/jsx-boolean-value`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-boolean-value.md)

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

  - Always include an `alt` prop on `<img>` tags. If the image is presentational, `alt` can be an empty string or the `<img>` must have `role="presentation"`. eslint: [`jsx-a11y/img-has-alt`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/img-has-alt.md)

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

  - Do not use words like "image", "photo", or "picture" in `<img>` `alt` props. eslint: [`jsx-a11y/img-redundant-alt`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/img-redundant-alt.md)

  > Why? Screenreaders already announce `img` elements as images, so there is no need to include this information in the alt text.

    ```jsx
    // 差评
    <img src="hello.jpg" alt="Picture of me waving hello" />

    // 好评
    <img src="hello.jpg" alt="Me waving hello" />
    ```

  - Use only valid, non-abstract [ARIA roles](https://www.w3.org/TR/wai-aria/roles#role_definitions). eslint: [`jsx-a11y/aria-role`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/aria-role.md)

    ```jsx
    // 差评 - not an ARIA role
    <div role="datepicker" />

    // 差评 - abstract ARIA role
    <div role="range" />

    // 好评
    <div role="button" />
    ```

  - Do not use `accessKey` on elements. eslint: [`jsx-a11y/no-access-key`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/no-access-key.md)

  > Why? Inconsistencies between keyboard shortcuts and keyboard commands used by people using screenreaders and keyboards complicate accessibility.

  ```jsx
  // 差评
  <div accessKey="h" />

  // 好评
  <div />
  ```

<a name="parentheses"></a>
## Parentheses

  - Wrap JSX tags in parentheses when they span more than one line. eslint: [`react/wrap-multilines`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/wrap-multilines.md)

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
## Tags

  - Always self-close tags that have no children. eslint: [`react/self-closing-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/self-closing-comp.md)

    ```jsx
    // 差评
    <Foo className="stuff"></Foo>

    // 好评
    <Foo className="stuff" />
    ```

  - If your component has multi-line properties, close its tag on a new line. eslint: [`react/jsx-closing-bracket-location`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-closing-bracket-location.md)

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
## Methods

  - Use arrow functions to close over local variables.

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

  - Bind event handlers for the render method in the constructor. eslint: [`react/jsx-no-bind`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-no-bind.md)

  > Why? A bind call in the render path creates a brand new function on every single render.

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

  - Do not use underscore prefix for internal methods of a React component.

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

  - Be sure to return a value in your `render` methods. eslint: [`require-render-return`](https://github.com/yannickcr/eslint-plugin-react/pull/502)

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

<a name="ordering"></a>
## Ordering

  - Ordering for `class extends React.Component`:

  1. optional `static` methods
  1. `constructor`
  1. `getChildContext`
  1. `componentWillMount`
  1. `componentDidMount`
  1. `componentWillReceiveProps`
  1. `shouldComponentUpdate`
  1. `componentWillUpdate`
  1. `componentDidUpdate`
  1. `componentWillUnmount`
  1. *clickHandlers or eventHandlers* like `onClickSubmit()` or `onChangeDescription()`
  1. *getter methods for `render`* like `getSelectReason()` or `getFooterContent()`
  1. *Optional render methods* like `renderNavigation()` or `renderProfilePicture()`
  1. `render`

  - How to define `propTypes`, `defaultProps`, `contextTypes`, etc...

    ```jsx
    import React, { PropTypes } from 'react';

    const propTypes = {
      id: PropTypes.number.isRequired,
      url: PropTypes.string.isRequired,
      text: PropTypes.string,
    };

    const defaultProps = {
      text: 'Hello World',
    };

    class Link extends React.Component {
      static methodsAreOk() {
        return true;
      }

      render() {
        return <a href={this.props.url} data-id={this.props.id}>{this.props.text}</a>
      }
    }

    Link.propTypes = propTypes;
    Link.defaultProps = defaultProps;

    export default Link;
    ```

  - Ordering for `React.createClass`: eslint: [`react/sort-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/sort-comp.md)

  1. `displayName`
  1. `propTypes`
  1. `contextTypes`
  1. `childContextTypes`
  1. `mixins`
  1. `statics`
  1. `defaultProps`
  1. `getDefaultProps`
  1. `getInitialState`
  1. `getChildContext`
  1. `componentWillMount`
  1. `componentDidMount`
  1. `componentWillReceiveProps`
  1. `shouldComponentUpdate`
  1. `componentWillUpdate`
  1. `componentDidUpdate`
  1. `componentWillUnmount`
  1. *clickHandlers or eventHandlers* like `onClickSubmit()` or `onChangeDescription()`
  1. *getter methods for `render`* like `getSelectReason()` or `getFooterContent()`
  1. *Optional render methods* like `renderNavigation()` or `renderProfilePicture()`
  1. `render`

<a name="ismounted"></a>
## `isMounted`

  - Do not use `isMounted`. eslint: [`react/no-is-mounted`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-is-mounted.md)

  > Why? [`isMounted` is an anti-pattern][anti-pattern], is not available when using ES6 classes, and is on its way to being officially deprecated.

  [anti-pattern]: https://facebook.github.io/react/blog/2015/12/16/ismounted-antipattern.html

**[↑ 回到最上方](#目录)**
