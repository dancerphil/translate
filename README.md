# JavaScript 代码风格指南() {

*如何用最合理的方式写你的 JavaScript 代码*

> BY 张聪([dancerphil@github](https://github.com/dancerphil/trick/))
>
> 这是一篇在[原文](https://github.com/airbnb/javascript)基础上演绎的译文，与原文的表达会有出入
>
> 原文在不断的更新，本文基于 2021-03-24 的版本，last commit [ [0cf78ac] ](https://github.com/airbnb/javascript/commit/0cf78acab0fff90e9591dedd8783b62a72ffc118)
>
> 除非另行注明，页面上所有内容采用[MIT](#许可)协议共享

> **注意**：指南假设你使用了 [Babel](https://babeljs.io)，并且使用 [babel-preset-airbnb](https://npmjs.com/babel-preset-airbnb) 和 [airbnb-browser-shims](https://npmjs.com/airbnb-browser-shims) 或者其他等价的方案。

[![Downloads](https://img.shields.io/npm/dm/eslint-config-airbnb.svg)](https://www.npmjs.com/package/eslint-config-airbnb)
[![Downloads](https://img.shields.io/npm/dm/eslint-config-airbnb-base.svg)](https://www.npmjs.com/package/eslint-config-airbnb-base)
[![Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/airbnb/javascript?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

其他的代码指南(Style Guides)
 - [ES5 中文版 (Deprecated)](https://github.com/sivan/javascript-style-guide/blob/master/es5/README.md)
 - [React](https://github.com/airbnb/javascript/tree/master/react)
 - [CSS-in-JavaScript](https://github.com/airbnb/javascript/tree/master/css-in-javascript)
 - [CSS & Sass](https://github.com/airbnb/css)
 - [Ruby](https://github.com/airbnb/ruby)

## 目录

  1. [类型 Types](#类型)
  1. [引用 References](#引用)
  1. [对象 Objects](#对象)
  1. [数组 Arrays](#数组)
  1. [解构 Destructuring](#解构)
  1. [字符串 Strings](#字符串)
  1. [函数 Functions](#函数)
  1. [箭头函数 Arrow Functions](#箭头函数)
  1. [类，构造函数 Classes & Constructors](#类构造函数)
  1. [模块 Modules](#模块)
  1. [迭代器，生成器 Iterators and Generators](#迭代器生成器)
  1. [属性 Properties](#属性)
  1. [变量 Variables](#变量)
  1. [提升 Hoisting](#提升)
  1. [比较运算符，等号 Comparison Operators & Equality](#比较运算符等号)
  1. [块 Blocks](#块)
  1. [控制声明 Control Statements](#控制声明)
  1. [注释 Comments](#注释)
  1. [空格 Whitespace](#空格)
  1. [逗号 Commas](#逗号)
  1. [分号 Semicolons](#分号)
  1. [类型，强制类型转换 Type Casting & Coercion](#类型强制类型转换)
  1. [命名规则 Naming Conventions](#命名规则)
  1. [存取器 Accessors](#存取器)
  1. [事件 Events](#事件)
  1. [jQuery](#jquery)
  1. [ECMAScript 5 兼容](#ecmascript-5-兼容性)
  1. [ECMAScript 6+ (ES 2015+) 代码风格](#es6-代码风格)
  1. [标准库 Standard Library](#标准库)
  1. [测试 Testing](#测试)
  1. ~~性能 Performance~~ [前往原文](https://github.com/airbnb/javascript#resources)
  1. ~~学习资源 Resources~~
  1. ~~使用人群~~
  1. ~~其他翻译~~
  1. ~~指南的指南~~
  1. ~~和我们一起讨论JS~~
  1. ~~贡献者~~
  1. ~~许可~~
  1. ~~修订~~

## 类型

  - [1.1](#types--primitives) **基本类型**：访问到基本类型时，直接得到的值。

    - `string`
    - `number`
    - `boolean`
    - `null`
    - `undefined`
    - `symbol`
    - `bigint`

    ```javascript
    const foo = 1;
    let bar = foo;

    bar = 9;

    console.log(foo, bar); // => 1, 9
    ```
    
    - Symbol 或 BigInt 很难被可靠的 polyfill，使用时需要注意浏览器/环境是否原生支持，如果不能原生支持，就不要使用他们。

  - [1.2](#types--complex)  **复杂类型**：访问复杂类型时，通过引用得到值。

    - `object`
    - `array`
    - `function`

    ```javascript
    const foo = [1, 2];
    const bar = foo;

    bar[0] = 9;

    console.log(foo[0], bar[0]); // => 9, 9
    ```

**[↑ 回到最上方](#目录)**

## 引用

  - [2.1](#references--prefer-const) 所有引用都应该使用 `const`，避免使用 `var` 。 eslint: [`prefer-const`](https://eslint.org/docs/rules/prefer-const.html), [`no-const-assign`](https://eslint.org/docs/rules/no-const-assign.html)

    > 为什么？这就确保了你不会对引用(reference)重新赋值，否则会出现难以理解的 bug。

    ```javascript
    // 差评
    var a = 1;
    var b = 2;

    // 好评
    const a = 1;
    const b = 2;
    ```

  - [2.2](#references--disallow-var) 如果你一定要修改一个引用，使用 `let` 代替 `var`。 eslint: [`no-var`](https://eslint.org/docs/rules/no-var.html)

    > 为什么？ `let` 是块级作用域变量，而 `var` 是函数作用域变量。

    ```javascript
    // 差评
    var count = 1;
    if (true) {
      count += 1;
    }

    // 好评，使用 let
    let count = 1;
    if (true) {
      count += 1;
    }
    ```

  - [2.3](#references--block-scope) 注意 `let` 和 `const` 都是块级作用域。

    ```javascript
    // const 和 let 只在它们定义的代码块里才存在
    {
      let a = 1;
      const b = 1;
    }
    console.log(a); // 抛出 ReferenceError
    console.log(b); // 抛出 ReferenceError
    ```

**[↑ 回到最上方](#目录)**

## 对象

  - [3.1](#objects--no-new) 使用字面语法来创建对象。 eslint: [`no-new-object`](https://eslint.org/docs/rules/no-new-object.html)

    ```javascript
    // 差评
    const item = new Object();

    // 好评
    const item = {};
    ```

  <a name="对象可计算属性"></a>
  - [3.2](#es6-computed-properties) 创建有动态属性名的对象时，使用可被计算的属性名称。

    > 为什么？这使你你可以在一个地方定义所有的对象属性。

    ```javascript

    function getKey(k) {
      return `a key named ${k}`;
    }

    // 差评
    const obj = {
      id: 5,
      name: 'San Francisco',
    };
    obj[getKey('enabled')] = true;

    // 好评
    const obj = {
      id: 5,
      name: 'San Francisco',
      [getKey('enabled')]: true, // 可被计算属性名称
    };
    ```

  <a name="对象方法简写"></a>
  - [3.3](#es6-object-shorthand) 使用对象方法的简写写法。 eslint: [`object-shorthand`](https://eslint.org/docs/rules/object-shorthand.html)

    ```javascript
    // 差评
    const atom = {
      value: 1,

      addValue: function (value) {
        return atom.value + value;
      },
    };

    // 好评
    const atom = {
      value: 1,

      addValue(value) {
        return atom.value + value;
      },
    };
    ```

  <a name="对象属性简写"></a>
  - [3.4](#es6-object-concise) 使用对象属性值的简写写法。 eslint: [`object-shorthand`](https://eslint.org/docs/rules/object-shorthand.html)

    > 为什么？这样写简短有力。

    ```javascript
    const lukeSkywalker = 'Luke Skywalker';

    // 差评
    const obj = {
      lukeSkywalker: lukeSkywalker,
    };

    // 好评
    const obj = {
      lukeSkywalker,
    };
    ```

  - [3.5](#objects--grouped-shorthand) 把简写的属性放在一起，然后写在对象的开头处。

    > 为什么？这样能清楚地分辨哪些属性使用了简写。

    ```javascript
    const anakinSkywalker = 'Anakin Skywalker';
    const lukeSkywalker = 'Luke Skywalker';

    // 差评
    const obj = {
      episodeOne: 1,
      twoJediWalkIntoACantina: 2,
      lukeSkywalker,
      episodeThree: 3,
      mayTheFourth: 4,
      anakinSkywalker,
    };

    // 好评
    const obj = {
      lukeSkywalker,
      anakinSkywalker,
      episodeOne: 1,
      twoJediWalkIntoACantina: 2,
      episodeThree: 3,
      mayTheFourth: 4,
    };
    ```

  - [3.6](#objects--quoted-props) 只有拥有不合法标识符的属性名才使用引号。 eslint: [`quote-props`](https://eslint.org/docs/rules/quote-props.html)

    > 为什么？这样更易读，且可以改善 IDE 语法高亮，同时帮助很多 JS 引擎进行优化。

    ```javascript
    // 差评
    const bad = {
      'foo': 3,
      'bar': 4,
      'data-blah': 5,
    };
  
    // 好评
    const good = {
      foo: 3,
      bar: 4,
      'data-blah': 5,
    };
    ```

  - [3.7](#objects--prototype-builtins) 不要直接调用 `Object.prototype` 方法，比如 `hasOwnProperty`， `propertyIsEnumerable` 和 `isPrototypeOf`。 eslint: [`no-prototype-builtins`](https://eslint.org/docs/rules/no-prototype-builtins)

    > 为什么？这些方法可能会被问题对象的属性所覆盖（Shadowed） - 考虑 `{ hasOwnProperty: false }` - 或者干脆，这是一个空对象（`Object.create(null)`）。

    ```javascript
    // 差评
    console.log(object.hasOwnProperty(key));
    
    // 好评
    console.log(Object.prototype.hasOwnProperty.call(object, key));
    
    // 最佳
    const has = Object.prototype.hasOwnProperty; // 在模块域内缓存一个查找(lookup)
    /* or */
    const has = require('has'); // https://www.npmjs.com/package/has
    
    console.log(has.call(object, key));
    ```

  - [3.8](#objects--rest-spread) 浅拷贝时，使用 Spread 运算符 `...` 代替 [`Object.assign`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)。 忽略某些属性得到一个新对象时，使用 Rest 运算符 `...`。 eslint: [`prefer-object-spread`](https://eslint.org/docs/rules/prefer-object-spread)

    ```javascript
    // 非常差
    const original = { a: 1, b: 2 };
    const copy = Object.assign(original, { c: 3 }); // this mutates `original` ಠ_ಠ
    delete copy.a; // so does this
    
    // 差评
    const original = { a: 1, b: 2 };
    const copy = Object.assign({}, original, { c: 3 }); // copy => { a: 1, b: 2, c: 3 }
    
    // 好评
    const original = { a: 1, b: 2 };
    const copy = { ...original, c: 3 }; // copy => { a: 1, b: 2, c: 3 }
    
    const { a, ...noA } = copy; // noA => { b: 2, c: 3 }
    ```

**[↑ 回到最上方](#目录)**

## 数组

  - [4.1](#arrays--literals) 使用字面语法创建数组。 eslint: [`no-array-constructor`](https://eslint.org/docs/rules/no-array-constructor.html)

    ```javascript
    // 差评
    const items = new Array();

    // 好评
    const items = [];
    ```

  - [4.2](#arrays--push) 不要用下标方式直接在数组中加上一个项，使用 [Array#push](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/push) 来代替。

    ```javascript
    const someStack = [];

    // 差评
    someStack[someStack.length] = 'abracadabra';

    // 好评
    someStack.push('abracadabra');
    ```

  <a name="拓展运算符"></a>
  - [4.3](#es6-array-spreads) 使用数组的拓展运算符 `...` 来复制数组。

    ```javascript
    // 差评
    const len = items.length;
    const itemsCopy = [];
    let i;

    for (i = 0; i < len; i += 1) {
      itemsCopy[i] = items[i];
    }

    // 好评
    const itemsCopy = [...items];
    ```

  - [4.4](#arrays--from-iterable) 使用 Spread 运算符 `...` 把一个 **可迭代对象** 转换成数组，而非 [Array.from](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/from)

    ```javascript
    const foo = document.querySelectorAll('.foo');
    
    // 好评
    const nodes = Array.from(foo);
    
    // 最佳
    const nodes = [...foo];
    ```

  - [4.5](#arrays--from-array-like) 使用 [`Array.from`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/from) 把一个 **类数组对象** 转换成数组

    ```javascript
    const arrLike = { 0: 'foo', 1: 'bar', 2: 'baz', length: 3 };
    
    // 差评
    const arr = Array.prototype.slice.call(arrLike);
    
    // 好评
    const arr = Array.from(arrLike);
    ```

  - [4.6](#arrays--mapping) 使用 [`Array.from`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/from) 映射 **可迭代对象**，而非 Spread 操作符 `...`。因为这样少创建一个临时的 Array。

    ```javascript
    // 差评
    const baz = [...foo].map(bar);
    
    // 好评
    const baz = Array.from(foo, bar);
    ```

  - [4.7](#arrays--callback-return) 在数组函数的回调中，使用 return 声明。如果一个函数体只需要一句简单的声明，那么你可以省略 return。参见 [8.2](#8.2)。 eslint: [`array-callback-return`](https://eslint.org/docs/rules/array-callback-return)

    ```javascript
    // 好评
    [1, 2, 3].map((x) => {
      const y = x + 1;
      return x * y;
    });

    // 好评
    [1, 2, 3].map((x) => x + 1);

    // 差评
    [[0, 1], [2, 3], [4, 5]].reduce((acc, item, index) => {
      const flatten = acc.concat(item);
    });

    // 好评
    [[0, 1], [2, 3], [4, 5]].reduce((acc, item, index) => {
      const flatten = acc.concat(item);
      return flatten;
    });

    // 差评
    inbox.filter((msg) => {
      const { subject, author } = msg;
      if (subject === 'Mockingbird') {
        return author === 'Harper Lee';
      } else {
        return false;
      }
    });

    // 好评
    inbox.filter((msg) => {
      const { subject, author } = msg;
      if (subject === 'Mockingbird') {
        return author === 'Harper Lee';
      }

      return false;
    });
    ```

  - [4.8](#arrays--bracket-newline) 当一个 Array 有多行时，方括号需要换行。

    ```javascript
    // 差评
    const arr = [
      [0, 1], [2, 3], [4, 5],
    ];
    const objectInArray = [{
      id: 1,
    }, {
      id: 2,
    }];
    const numberInArray = [
      1, 2,
    ];
    
    // 好评
    const arr = [[0, 1], [2, 3], [4, 5]];
    const objectInArray = [
      {
        id: 1,
      },
      {
        id: 2,
      },
    ];
    const numberInArray = [
      1,
      2,
    ];
    ```

**[↑ 回到最上方](#目录)**

## 解构

  - [5.1](#destructuring--object) 使用对象解构（Destructuring）访问和使用多属性对象。 eslint: [`prefer-destructuring`](https://eslint.org/docs/rules/prefer-destructuring)

    > 为什么？解构使你不用频繁的创建临时引用。重复访问对象带来了更多的代码，容易出错。解构可以在一个块中提供一个找到定义点的位置，而不需要在整个块中寻找
    > 
    > 注 BY [dancerphil](https://github.com/dancerphil)：在异步代码中，解构和重复访问的行为是不同的

    ```javascript
    // 差评
    function getFullName(user) {
      const firstName = user.firstName;
      const lastName = user.lastName;

      return `${firstName} ${lastName}`;
    }

    // 好评
    function getFullName(user) {
      const { firstName, lastName } = user;
      return `${firstName} ${lastName}`;
    }

    // 最佳
    function getFullName({ firstName, lastName }) {
      return `${firstName} ${lastName}`;
    }
    ```

  - [5.2](#destructuring--array) 解构数组。 eslint: [`prefer-destructuring`](https://eslint.org/docs/rules/prefer-destructuring)

    ```javascript
    const arr = [1, 2, 3, 4];

    // 差评
    const first = arr[0];
    const second = arr[1];

    // 好评
    const [first, second] = arr;
    ```

  - [5.3](#destructuring--object-over-array) 要返回多个值时，使用对象解构，而不是数组解构。

    > 为什么？你可以轻松的增减返回值或更改它们的顺序，而不需要改变调用者的代码

    ```javascript
    // 差评
    function processInput(input) {
      // 一些奇怪的事情发生了(then a miracle occurs)
      return [left, right, top, bottom];
    }

    // 调用者需要思考返回值的顺序问题
    const [left, __, top] = processInput(input);

    // 好评
    function processInput(input) {
      // 一些奇怪的事情发生了
      return { left, right, top, bottom };
    }

    // 调用者只拿取它们需要的数据
    const { left, top } = processInput(input);
    ```

**[↑ 回到最上方](#目录)**

## 字符串

  - [6.1](#strings--quotes) 字符串使用单引号 `''` 。 eslint: [`quotes`](https://eslint.org/docs/rules/quotes.html)

    ```javascript
    // 差评
    const name = "Capt. Janeway";
    
    // 差评 - 模版字符串应当包含变量或者换行
    const name = `Capt. Janeway`;

    // 好评
    const name = 'Capt. Janeway';
    ```

  - [6.2](#strings--line-length) 字符串超过 100 个字节时，**不要** 使用字符串连接号换行。

    ```javascript
    // 差评
    const errorMessage = 'This is a super long error that was thrown because \
    of Batman. When you stop to think about how Batman had anything to do \
    with this, you would get nowhere \
    fast.';

    // 差评
    const errorMessage = 'This is a super long error that was thrown because ' +
      'of Batman. When you stop to think about how Batman had anything to do ' +
      'with this, you would get nowhere fast.';

    // 好评
    const errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.';

    ```

  <a name="模版字符串"></a>
  - [6.3](#es6-template-literals) 用程序生成字符串时，使用模板字符串代替字符串连接。 eslint: [`prefer-template`](https://eslint.org/docs/rules/prefer-template.html) [`template-curly-spacing`](https://eslint.org/docs/rules/template-curly-spacing)

    > 为什么？模板字符串更为简洁，更具可读性，其恰当的换行和字符串插值非常好用。

    ```javascript
    // 差评
    function sayHi(name) {
      return 'How are you, ' + name + '?';
    }

    // 差评
    function sayHi(name) {
      return ['How are you, ', name, '?'].join();
    }

    // 差评
    function sayHi(name) {
      return `How are you, ${ name }?`;
    }

    // 好评
    function sayHi(name) {
      return `How are you, ${name}?`;
    }
    ```

  - [6.4](#strings--eval) 永远不要在字符串上使用 `eval()` ，这会造成很多漏洞。 eslint: [`no-eval`](https://eslint.org/docs/rules/no-eval)

  - [6.5](#strings--escaping) 不要在字符串里使用无谓的转义符号。 eslint: [`no-useless-escape`](https://eslint.org/docs/rules/no-useless-escape)

    > 为什么？反斜杠影响了可读性，所以只有必要的时候才转义。

    ```javascript
    // 差评
    const foo = '\'this\' \i\s \"quoted\"';

    // 好评
    const foo = '\'this\' is "quoted"';
    const foo = `my name is '${name}'`;
    ```

**[↑ 回到最上方](#目录)**

## 函数

  - [7.1](#functions--declarations) 使用函数表达式，不要使用函数声明。

    > 为什么？函数声明是可命名的，他们在调用栈中更容易被识别。另外，函数声明会把整个函数提升（hoisted），而函数表达式只会把函数的引用变量名提升。所以[箭头函数](#arrow-functions)可以完全取代函数表达式。。函数声明会把整个函数提升（Hoisted），这使得函数太容易在定义之前被引用，破坏了可读性。如果一个函数定义复杂到影响到理解文件其余的内容，此时这个文件应当被提取到它自己的模块中。无论是什么情况，不要忘了显式的命名你的函数表达式（有的时候 浏览器或 babel 会推断一个命名，但你仍然应该自己命名），这有助于优化错误堆栈。 ([相关讨论](https://github.com/airbnb/javascript/issues/794))

    ```javascript
    // 差评
    function foo() {
      // ...
    }
    
    // 差评
    const foo = function () {
      // ...
    };

    // 好评 - 给一个较长的区别于调用的函数词法名称
    const short = function longUniqueMoreDescriptiveLexicalFoo() {
      // ...
    };
    ```

  - [7.2](#functions--iife) 在立即调用的函数表达式的两侧用括号包裹。 eslint: [`wrap-iife`](https://eslint.org/docs/rules/wrap-iife.html)

    > 为什么？立即调用的函数表达式(immediately invoked function expression)是一个单独的单元，你需要强调这一点，所以把表达式和它的调用括号包裹起来。还需要提醒一下的是：在写模块的时候，你永远不会需要一个 IIFE

    ```javascript
    // 立即调用的函数表达式 (IIFE)
    (function () {
      console.log('Welcome to the Internet. Please follow me.');
    }());
    ```

  - [7.3](#functions--in-blocks) 不要在一个非函数代码块（`if`, `while` 等等）内定义一个函数。请把你想要的那个函数赋给一个变量。浏览器会允许你这么做，但是它们的解析表现不一致。 eslint: [`no-loop-func`](https://eslint.org/docs/rules/no-loop-func.html)

  - [7.4](#functions--note-on-blocks) **注意：** ECMA-262 把 `block` 定义为一组语句。而函数声明并不是语句。

    ```javascript
    // 差评
    if (currentUser) {
      function test() {
        console.log('Nope.');
      }
    }

    // 好评
    let test;
    if (currentUser) {
      test = () => {
        console.log('Yup.');
      };
    }
    ```

  - [7.5](#functions--arguments-shadow) 永远不要把参数命名为 `arguments` 。这将取代原来函数作用域内的 `arguments` 对象。

    ```javascript
    // 差评
    function foo(name, options, arguments) {
      // ...
    }

    // 好评
    function foo(name, options, args) {
      // ...
    }
    ```

  <a name="rest"></a>
  - [7.6](#es6-rest) 与此同时，不要使用 `arguments` 。可以选择 rest 语法 `...` 替代。 eslint: [`prefer-rest-params`](https://eslint.org/docs/rules/prefer-rest-params)

    > 为什么？ `...` 可以确切的指定你想要获取的变量，而且 `rest` 参数是一个真正的数组，而 `argument` 是一个类数组对象

    ```javascript
    // 差评
    function concatenateAll() {
      const args = Array.prototype.slice.call(arguments);
      return args.join('');
    }

    // 好评
    function concatenateAll(...args) {
      return args.join('');
    }
    ```

  <a name="默认参数"></a>
  - [7.7](#es6-default-parameters) 使用预设参数的语法，而不是改变函数参数。

    ```javascript
    // 非常差
    function handleThings(opts) {
      // 不该改变函数参数
      // 同时，如果 opts 被计算为 false ，它会成为一个对象
      // 或许你想这么做，但是这样会引起 bug
      opts = opts || {};
      // ...
    }

    // 差评
    function handleThings(opts) {
      if (opts === void 0) {
        opts = {};
      }
      // ...
    }

    // 好评
    function handleThings(opts = {}) {
      // ...
    }
    ```

  - [7.8](#functions--default-side-effects) 避免预设参数的语法的副作用。

    > 为什么？这样做完之后，代码变得没有逻辑。

    ```javascript
    var b = 1;
    // 差评
    function count(a = b++) {
      console.log(a);
    }
    count();  // 1
    count();  // 2
    count(3); // 3
    count();  // 3
    ```

  - [7.9](#functions--defaults-last) 总是把预设参数写在参数的最后。 eslint: [`default-param-last`](https://eslint.org/docs/rules/default-param-last)

    ```javascript
    // 差评
    function handleThings(opts = {}, name) {
      // ...
    }

    // 好评
    function handleThings(name, opts = {}) {
      // ...
    }
    ```

  - [7.10](#functions--constructor) 不要使用函数构造器来构造一个新函数。eslint: [`no-new-func`](https://eslint.org/docs/rules/no-new-func)

    > 为什么？通过这种方式来构造函数与 `eval()` 相类似。会造成很多漏洞

    ```javascript
    // 差评
    var add = new Function('a', 'b', 'return a + b');

    // 差评
    var subtract = Function('a', 'b', 'return a - b');
    ```

  - [7.11](#functions--signature-spacing) 在函数的 signature 后放置空格。eslint: [`space-before-function-paren`](https://eslint.org/docs/rules/space-before-function-paren) [`space-before-blocks`](https://eslint.org/docs/rules/space-before-blocks)

    > 为什么？一致性是必须考虑的。在你新增和删除名称时，你不需要改变空格。

    ```javascript
    // 差评
    const f = function(){};
    const g = function (){};
    const h = function() {};

    // 好评
    const x = function () {};
    const y = function a() {};
    ```

  - [7.12](#functions--mutate-params) 不要改变对象的参数。 eslint: [`no-param-reassign`](https://eslint.org/docs/rules/no-param-reassign.html)

    > 为什么？将传入对象的参数重新赋值，可能会引起一些不期望的副作用。

    ```javascript
    // 差评
    function f1(obj) {
      obj.key = 1;
    }

    // 好评
    function f2(obj) {
      const key = Object.prototype.hasOwnProperty.call(obj, 'key') ? obj.key : 1;
    }
    ```

  - [7.13](#functions--reassign-params) 永远不要对参数重新赋值。 eslint: [`no-param-reassign`](https://eslint.org/docs/rules/no-param-reassign.html)

    > 为什么？对参数重新赋值会引起意外的行为，尤其是访问 `arguments` 对象。在 V8 引擎里，会导致优化问题。

    ```javascript
    // 差评
    function f1(a) {
      a = 1;
      // ...
    }

    function f2(a) {
      if (!a) { a = 1; }
      // ...
    }

    // 好评
    function f3(a) {
      const b = a || 1;
      // ...
    }

    function f4(a = 1) {
      // ...
    }
    ```

  - [7.14](#functions--spread-vs-apply) 使用 Spread 操作符 `...` 调用可变函数。 eslint: [`prefer-spread`](https://eslint.org/docs/rules/prefer-spread)

    > 为什么？这样清晰，不需要提供一个上下文，且可以简单的组合 `new` 和 `apply`。

    ```javascript
    // 差评
    const x = [1, 2, 3, 4, 5];
    console.log.apply(console, x);
    
    // 好评
    const x = [1, 2, 3, 4, 5];
    console.log(...x);
    
    // 差评
    new (Function.prototype.bind.apply(Date, [null, 2016, 8, 5]));
    
    // 好评
    new Date(...[2016, 8, 5]);
    ```

  - [7.15](#functions--signature-invocation-indentation) 带有多个参数的函数的签名或者调用都应该有一致的缩进：每一行一个参数，末尾逗号。 eslint: [`function-paren-newline`](https://eslint.org/docs/rules/function-paren-newline)

    ```javascript
    // 差评
    function foo(bar,
                 baz,
                 quux) {
      // ...
    }
    
    // 好评
    function foo(
      bar,
      baz,
      quux,
    ) {
      // ...
    }
    
    // 差评
    console.log(foo,
      bar,
      baz);
    
    // 好评
    console.log(
      foo,
      bar,
      baz,
    );
    ```

**[↑ 回到最上方](#目录)**

## 箭头函数

  - [8.1](#arrows--use-them) 当你必须使用匿名函数（或传递一个内联函数）时，使用箭头函数符号。 eslint: [`prefer-arrow-callback`](https://eslint.org/docs/rules/prefer-arrow-callback.html), [`arrow-spacing`](https://eslint.org/docs/rules/arrow-spacing.html)

    > 为什么？箭头函数创造了一个新的 `this` 上下文，通常符合你的期望，而且箭头函数非常简洁。
    >
    > 什么时候不？如果你有一个相当复杂的函数，你或许想要把逻辑部分转移到一个命名函数表达式上。

    ```javascript
    // 差评
    [1, 2, 3].map(function (x) {
      const y = x + 1;
      return x * y;
    });

    // 好评
    [1, 2, 3].map((x) => {
      const y = x + 1;
      return x * y;
    });
    ```

  - [8.2](#arrows--implicit-return) 如果一个函数体只有很简单的一行声明并返回一个没有副作用的 [表达式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators#Expressions)，那就把花括号、圆括号和 `return` 都省略掉。如果不是，那就保留括号和 `return` 。 eslint: [`arrow-parens`](https://eslint.org/docs/rules/arrow-parens.html), [`arrow-body-style`](https://eslint.org/docs/rules/arrow-body-style.html)

    > 为什么？这种语法糖在函数的链式调用中可读性很高

    ```javascript
    // 差评
    [1, 2, 3].map((number) => {
      const nextNumber = number + 1;
      `A string containing the ${nextNumber}.`;
    });

    // 好评
    [1, 2, 3].map((number) => `A string containing the ${number}.`);

    // 好评
    [1, 2, 3].map((number) => {
      const nextNumber = number + 1;
      return `A string containing the ${nextNumber}.`;
    });

    // 好评
    [1, 2, 3].map((number, index) => ({
      [index]: number
    }));
    
    // 有副作用时不要省略 return
    function foo(callback) {
      const val = callback();
      if (val === true) {
        // Do something if callback returns true
      }
    }
    let bool = false;
    
    // 差评
    foo(() => bool = true);
    
    // 好评
    foo(() => {
      bool = true;
    });
    ```

  - [8.3](#arrows--paren-wrap) 如果你们表达式跨行了，请使用括号把它们包裹起来，增加可读性。

    > 为什么？这样做使得表达式的开始和结束的位置更加清楚。

    ```javascript
    // 差评
    ['get', 'post', 'put'].map((httpMethod) => Object.prototype.hasOwnProperty.call(
        httpMagicObjectWithAVeryLongName,
        httpMethod,
      )
    );

    // 好评
    ['get', 'post', 'put'].map((httpMethod) => (
      Object.prototype.hasOwnProperty.call(
        httpMagicObjectWithAVeryLongName,
        httpMethod,
      )
    ));
    ```

  - [8.4](#arrows--one-arg-parens) 总是使用括号包裹参数，清晰一致。 eslint: [`arrow-parens`](https://eslint.org/docs/rules/arrow-parens.html)

    > 为什么？增加或移除函数参数时最小化 diff。

    ```javascript
    // 差评
    [1, 2, 3].map(x => x * x);
    
    // 好评
    [1, 2, 3].map((x) => x * x);

    // 差评
    [1, 2, 3].map(number => (
      `A long string with the ${number}. It’s so long that we don’t want it to take up space on the .map line!`
    ));
    
    // 好评
    [1, 2, 3].map((number) => (
      `A long string with the ${number}. It’s so long that we don’t want it to take up space on the .map line!`
    ));
    
    // 差评
    [1, 2, 3].map(x => {
      const y = x + 1;
      return x * y;
    });

    // 好评
    [1, 2, 3].map((x) => {
      const y = x + 1;
      return x * y;
    });
    ```

  - [8.5](#arrows--confusing) 避免把箭头函数语法 (`=>`) 和比较运算符 (`<=`, `>=`) 相混淆。 eslint: [`no-confusing-arrow`](https://eslint.org/docs/rules/no-confusing-arrow)

    ```javascript
    // 差评
    const itemHeight = (item) => item.height <= 256 ? item.largeSize : item.smallSize;

    // 差评
    const itemHeight = (item) => item.height >= 256 ? item.largeSize : item.smallSize;

    // 好评
    const itemHeight = (item) => (item.height <= 256 ? item.largeSize : item.smallSize);

    // 好评
    const itemHeight = (item) => {
      const { height, largeSize, smallSize } = item;
      return height <= 256 ? largeSize : smallSize;
    };
    ```

  - [8.6](#whitespace--implicit-arrow-linebreak) 省略 return 时，保持返回值的位置一致。 eslint: [`implicit-arrow-linebreak`](https://eslint.org/docs/rules/implicit-arrow-linebreak)

    ```javascript
    // 差评
    (foo) =>
      bar;
    (foo) =>
      (bar);
    
    // 好评
    (foo) => bar;
    (foo) => (bar);
    (foo) => (
       bar
    )
    ```

**[↑ 回到最上方](#目录)**

## 类，构造函数

  - [9.1](#constructors--use-class) 总是使用 `class`。避免直接操作 `prototype`。

    > 为什么? class 语法更简洁易读

    ```javascript
    // 差评
    function Queue(contents = []) {
      this.queue = [...contents];
    }
    Queue.prototype.pop = function () {
      const value = this.queue[0];
      this.queue.splice(0, 1);
      return value;
    };


    // 好评
    class Queue {
      constructor(contents = []) {
        this.queue = [...contents];
      }
      pop() {
        const value = this.queue[0];
        this.queue.splice(0, 1);
        return value;
      }
    }
    ```

  - [9.2](#constructors--extends) 使用 `extends` 来继承。

    > 为什么？ `extends` 是一个内建的原型继承方法，它不会破坏 `instanceof`。

    ```javascript
    // 差评
    const inherits = require('inherits');
    function PeekableQueue(contents) {
      Queue.apply(this, contents);
    }
    inherits(PeekableQueue, Queue);
    PeekableQueue.prototype.peek = function () {
      return this.queue[0];
    }

    // 好评
    class PeekableQueue extends Queue {
      peek() {
        return this.queue[0];
      }
    }
    ```

  - [9.3](#constructors--chaining) 方法返回 `this` 可以帮助链式调用。

    ```javascript
    // 差评
    Jedi.prototype.jump = function () {
      this.jumping = true;
      return true;
    };

    Jedi.prototype.setHeight = function (height) {
      this.height = height;
    };

    const luke = new Jedi();
    luke.jump(); // => true
    luke.setHeight(20); // => undefined

    // 好评
    class Jedi {
      jump() {
        this.jumping = true;
        return this;
      }

      setHeight(height) {
        this.height = height;
        return this;
      }
    }

    const luke = new Jedi();

    luke.jump()
      .setHeight(20);
    ```


  - [9.4](#constructors--tostring) 你可以实现一个自定义的 `toString()` 方法，但要确保它能正常运行并且不会引起副作用。

    ```javascript
    class Jedi {
      constructor(options = {}) {
        this.name = options.name || 'no name';
      }

      getName() {
        return this.name;
      }

      toString() {
        return `Jedi - ${this.getName()}`;
      }
    }
    ```

  - [9.5](#constructors--no-useless) 如果没有指定，类会拥有默认的构造方法，你不需要写一个空的构造方法，或者只是继承父类的方法，这是多余的。 eslint: [`no-useless-constructor`](https://eslint.org/docs/rules/no-useless-constructor)

    ```javascript
    // 差评
    class Jedi {
      constructor() {}

      getName() {
        return this.name;
      }
    }

    // 差评
    class Rey extends Jedi {
      constructor(...args) {
        super(...args);
      }
    }

    // 好评
    class Rey extends Jedi {
      constructor(...args) {
        super(...args);
        this.name = 'Rey';
      }
    }
    ```

  - [9.6](#classes--no-duplicate-members) 避免重复的类成员。 eslint: [`no-dupe-class-members`](https://eslint.org/docs/rules/no-dupe-class-members)

    > 为什么？重复的类成员中，只有最后一个声明会被静静的选中，重复类成员几乎必然会引起 bug

    ```javascript
    // 差评
    class Foo {
      bar() { return 1; }
      bar() { return 2; }
    }

    // 好评
    class Foo {
      bar() { return 1; }
    }

    // 好评
    class Foo {
      bar() { return 2; }
    }
    ```

  - [9.7](#classes--methods-use-this) 类方法应该使用 `this` 或者就应该是一个静态方法 (Static)，除非其他的库或框架要求了非静态方法。实例方法在每个实例上的表现应该不同。 eslint: [`class-methods-use-this`](https://eslint.org/docs/rules/class-methods-use-this)

    ```javascript
    // 差评
    class Foo {
      bar() {
        console.log('bar');
      }
    }
    // 好评 - 使用了 this
    class Foo {
      bar() {
        console.log(this.bar);
      }
    }
    // 好评 - constructor 除外
    class Foo {
      constructor() {
        // ...
      }
    }
    // 好评 - 静态方法不使用 this
    class Foo {
      static bar() {
        console.log('bar');
      }
    }
    ```

**[↑ 回到最上方](#目录)**

## 模块

  - [10.1](#modules--use-them) 总是使用模块 (`import`/`export`) 而不是其他非标准模块系统。你可以编译为你喜欢的模块系统。

    > 为什么？模块是未来，让我们开始使用未来吧！

    ```javascript
    // 差评
    const AirbnbStyleGuide = require('./AirbnbStyleGuide');
    module.exports = AirbnbStyleGuide.es6;

    // 还可以
    import AirbnbStyleGuide from './AirbnbStyleGuide';
    export default AirbnbStyleGuide.es6;

    // 最佳
    import { es6 } from './AirbnbStyleGuide';
    export default es6;
    ```

  - [10.2](#modules--no-wildcard) 不要使用通配符 `*` 引入。

    > 为什么？这样能确保你只有一个默认的 `export`。

    ```javascript
    // 差评
    import * as AirbnbStyleGuide from './AirbnbStyleGuide';

    // 好评
    import AirbnbStyleGuide from './AirbnbStyleGuide';
    ```

  - [10.3](#modules--no-export-from-import) 不要从 `import` 中直接 `export`。

    > 为什么？虽然写在一行看起来很棒，但是它们毕竟一个是引入，一个是导出，分开写会让所有事都井井有条。

    ```javascript
    // 差评
    // filename es6.js
    export { es6 as default } from './AirbnbStyleGuide';

    // 好评
    // filename es6.js
    import { es6 } from './AirbnbStyleGuide';
    export default es6;
    ```

  - [10.4](#modules--no-duplicate-imports) 来自相同路径的 `import` ，就把他们写在同一个地方。 eslint: [`no-duplicate-imports`](https://eslint.org/docs/rules/no-duplicate-imports)

    > 为什么？写在不同行的话，程序的维护和更新变得非常困难。

    ```javascript
    // 差评
    import foo from 'foo';
    // … 一些其他的 imports … //
    import { named1, named2 } from 'foo';

    // 好评
    import foo, { named1, named2 } from 'foo';

    // 好评
    import foo, {
      named1,
      named2,
    } from 'foo';
    ```

  - [10.5](#modules--no-mutable-exports) 不要 `export` 可变的绑定。 eslint: [`import/no-mutable-exports`](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/no-mutable-exports.md)

    > 为什么？在特定的情况下也许会导出可变绑定，一般来说我们都要避免变化（Mutation）的发生，只使用常数引用。

    ```javascript
    // 差评
    let foo = 3;
    export { foo };

    // 好评
    const foo = 3;
    export { foo };
    ```

  - [10.6](#modules--prefer-default-export) 如果模块只有一个简单的 `export`，使用 `default` 模块而不是带名称模块。
eslint: [`import/prefer-default-export`](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/prefer-default-export.md)

    > 为什么？鼓励更多模块只 export 一样东西，有助于可读性和可维护性。

    ```javascript
    // 差评
    export function foo() {}

    // 好评
    export default function foo() {}
    ```

  - [10.7](#modules--imports-first) 把所有的 `import` 放在其他的声明之前。 eslint: [`import/imports-first`](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/imports-first.md)

    > 为什么？因为 `import` 是提升的（Hoisted），把它们放在最上方可以避免许多令人惊讶的代码行为。

    ```javascript
    // 差评
    import foo from 'foo';
    foo.init();
    import bar from 'bar';

    // 好评
    import foo from 'foo';
    import bar from 'bar';

    foo.init();
    ```

  - [10.8](#modules--multiline-imports-over-newlines) 多行的 import 应该与 Array 和 Object 的规则保持一致。 eslint: [`object-curly-newline`](https://eslint.org/docs/rules/object-curly-newline)

    > 为什么？这里的大括号与其他代码块的缩进保持一致，逗号也是。

    ```javascript
    // 差评
    import {longNameA, longNameB, longNameC, longNameD, longNameE} from 'path';
    
    // 好评
    import {
      longNameA,
      longNameB,
      longNameC,
      longNameD,
      longNameE,
    } from 'path';
    ```

  - [10.9](#modules--no-webpack-loader-syntax) 禁止在 import 语句中使用 webpack loader 语法。 eslint: [`import/no-webpack-loader-syntax`](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/no-webpack-loader-syntax.md)

    > 为什么？这个语法依赖具体的打包库。应当在`webpack.config.js` 中定义 loader。

    ```javascript
    // 差评
    import fooSass from 'css!sass!foo.scss';
    import barCss from 'style!css!bar.css';
    
    // 好评
    import fooSass from 'foo.scss';
    import barCss from 'bar.css';
    ```
  
  - [10.10](#modules--import-extensions) 不要包括文件后缀。 eslint: [`import/extensions`](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/extensions.md)

    > 为什么？这影响了重构，消费端写死了实现的细节。

    ```javascript
    // 差评
    import foo from './foo.js';
    import bar from './bar.jsx';
    import baz from './baz/index.jsx';
    
    // 好评
    import foo from './foo';
    import bar from './bar';
    import baz from './baz';
    ```

**[↑ 回到最上方](#目录)**

## 迭代器，生成器

  - [11.1](#iterators--nope) 不要使用迭代器(iterators)。选择 JS 高阶函数比如 `map()` 和 `reduce()` ，不要使用循环如 `for-in` 和 `for-of`。 eslint: [`no-iterator`](https://eslint.org/docs/rules/no-iterator.html) [`no-restricted-syntax`](https://eslint.org/docs/rules/no-restricted-syntax)

    > 为什么？这增加了代码的不变性，处理纯函数的返回值非常方便，超过了其可以造成的副作用。

    > 使用 `map()` / `every()` / `filter()` / `find()` / `findIndex()` / `reduce()` / `some()` 等方法来迭代数组，并且使用 `Object.keys()` / `Object.values()` / `Object.entries()` 来生成一些数组，以便你迭代对象。

    ```javascript
    const numbers = [1, 2, 3, 4, 5];

    // 差评
    let sum = 0;
    for (let num of numbers) {
      sum += num;
    }

    sum === 15;

    // 好评
    let sum = 0;
    numbers.forEach((num) => {
        sum += num
    });
    sum === 15;

    // 最佳 - 使用函数式
    const sum = numbers.reduce((total, num) => total + num, 0);
    sum === 15;
    ```

  - [11.2](#generators--nope) 暂时不要使用生成器（Generators）。

    > 为什么？他们不能很好的被编译到 ES5

  - [11.3](#generators--spacing) 如果拒绝听从 [劝告](#generators--nope) 使用生成器，请先确保他们的函数签名是正确的。 eslint: [`generator-star-spacing`](https://eslint.org/docs/rules/generator-star-spacing)

    > 为什么？ `function` 和 `*` 是同一个概念关键字的两个部分 -  `*` 不是 `function`的一个修饰符， `function*` 与 `function` 不同，它是一个独有的关键字。

    ```javascript
    // 差评
    function * foo() {
      // ...
    }

    // 差评
    const bar = function * () {
      // ...
    };

    // 差评
    const baz = function *() {
      // ...
    };

    // 差评
    const quux = function*() {
      // ...
    };

    // 差评
    function*foo() {
      // ...
    }

    // 差评
    function *foo() {
      // ...
    }

    // 非常差
    function
    *
    foo() {
      // ...
    }

    // 非常差
    const wat = function
    *
    () {
      // ...
    }

    // 好评
    function* foo() {
      // ...
    }

    // 好评
    const foo = function* () {
      // ...
    }
    ```

**[↑ 回到最上方](#目录)**

## 属性

  - [12.1](#properties--dot) 使用 `.` 符号来访问对象属性。 eslint: [`dot-notation`](https://eslint.org/docs/rules/dot-notation.html)

    ```javascript
    const luke = {
      jedi: true,
      age: 28,
    };

    // 差评
    const isJedi = luke['jedi'];

    // 好评
    const isJedi = luke.jedi;
    ```

  - [12.2](#properties--bracket) 如果要使用一个变量来访问属性，那么使用 `[]`。

    ```javascript
    const luke = {
      jedi: true,
      age: 28,
    };

    function getProp(prop) {
      return luke[prop];
    }

    const isJedi = getProp('jedi');
    ```

  <a name="阶乘运算符"></a>
  - [12.3](#es2016-properties--exponentiation-operator) 使用阶乘操作符 `**`。 eslint: [`no-restricted-properties`](https://eslint.org/docs/rules/no-restricted-properties).

    ```javascript
    // 差评
    const binary = Math.pow(2, 10);
    
    // 好评
    const binary = 2 ** 10;
    ```

**[↑ 回到最上方](#目录)**

## 变量

  - [13.1](#variables--const) 总是用 `const` 或 `let` 来声明变量。不这么做会导致全局变量。我们想要尽量避免污染全局命名空间。 eslint: [`no-undef`](https://eslint.org/docs/rules/no-undef) [`prefer-const`](https://eslint.org/docs/rules/prefer-const)

    ```javascript
    // 差评
    superPower = new SuperPower();

    // 好评
    const superPower = new SuperPower();
    ```

  - [13.2](#variables--one-const) 每个变量或赋值都对应一个 `const` 或 `let`。 eslint: [`one-var`](https://eslint.org/docs/rules/one-var.html)

    > 为什么？增加新的变量会更容易，而且你永远不用再担心把 `,` 和 `;` 换来换去的事情，你肯定觉得 git diff 里包含太多标点符号的变换是很蠢的事情。而且，调试时你可以步进，而不是一次性跳过了所有的声明。

    ```javascript
    // 差评
    const items = getItems(),
        goSportsTeam = true,
        dragonball = 'z';

    // 差评 - 与上面比较一下，再说说哪里写错了
    const items = getItems(),
        goSportsTeam = true;
        dragonball = 'z';

    // 好评
    const items = getItems();
    const goSportsTeam = true;
    const dragonball = 'z';
    ```

  - [13.3](#variables--const-let-group) 把你所有的 `const` 放在一起，再把所有的 `let` 放在一起。

    > 为什么？等到你想要把已赋值的变量赋给一个新变量的时候，这很有帮助。

    ```javascript
    // 差评
    let i, len, dragonball,
        items = getItems(),
        goSportsTeam = true;

    // 差评
    let i;
    const items = getItems();
    let dragonball;
    const goSportsTeam = true;
    let len;

    // 好评
    const goSportsTeam = true;
    const items = getItems();
    let dragonball;
    let i;
    let length;
    ```

  - [13.4](#variables--define-where-used) 在你需要的地方赋值变量，把他们放到合理的位置。

    > 为什么？ `let` 和 `const` 是块级作用域的，不是函数级作用域的。

    ```javascript
    // 差评 - 没有必要的函数调用
    function checkName(hasName) {
      const name = getName();

      if (hasName === 'test') {
        return false;
      }

      if (name === 'test') {
        this.setName('');
        return false;
      }

      return name;
    }

    // 好评
    function checkName(hasName) {
      if (hasName === 'test') {
        return false;
      }

      const name = getName();

      if (name === 'test') {
        this.setName('');
        return false;
      }

      return name;
    }
    ```

  - [13.5](#variables--no-chain-assignment) 不要链式赋值。 eslint: [`no-multi-assign`](https://eslint.org/docs/rules/no-multi-assign)

    > 为什么？链式赋值创建了全局变量。

    ```javascript
    // 差评
    (function example() {
      // JavaScript 将以下代码解释为
      // let a = ( b = ( c = 1 ) );
      // 使 let 关系字只作用于变量 a，而 b 和 c 成为了全局变量。
      let a = b = c = 1;
    }());
    console.log(a); // 抛出 ReferenceError
    console.log(b); // 1
    console.log(c); // 1
    
    // 好评
    (function example() {
      let a = 1;
      let b = a;
      let c = a;
    }());
    console.log(a); // 抛出 ReferenceError
    console.log(b); // 抛出 ReferenceError
    console.log(c); // 抛出 ReferenceError
    // `const` 也一样
    ```

  - [13.6](#variables--unary-increment-decrement) 禁止使用一元增减操作符(`++`, `--`)。 eslint [`no-plusplus`](https://eslint.org/docs/rules/no-plusplus)

    > 为什么？根据 eslint 文档所说，一元增减操作符会服从自动分号插入(ASI)，从而导致问题。同时，`num += 1` 比 `num++` 或 `num ++` 更有表现力一些。同时，避免预增预减值带来的非预期行为。
    > 
    > 注 BY [dancerphil](https://github.com/dancerphil)：依据场景选择。

    ```javascript
    // 差评
    const array = [1, 2, 3];
    let num = 1;
    num++;
    --num;
    let sum = 0;
    let truthyCount = 0;
    for (let i = 0; i < array.length; i++) {
      let value = array[i];
      sum += value;
      if (value) {
        truthyCount++;
      }
    }
    
    // 好评
    const array = [1, 2, 3];
    let num = 1;
    num += 1;
    num -= 1;
    const sum = array.reduce((a, b) => a + b, 0);
    const truthyCount = array.filter(Boolean).length;
    ```

  - [13.7](#variables--linebreak) 避免在 `=` 前后换行，如果赋值违反了 [`max-len`](https://eslint.org/docs/rules/max-len.html)，用括号包裹（注：也可忽略 `max-len`）。 eslint [`operator-linebreak`](https://eslint.org/docs/rules/operator-linebreak.html).

    > 为什么？这混淆了赋值的值。

    ```javascript
    // 差评
    const foo =
      superLongLongLongLongLongLongLongLongFunctionName();
    
    // 差评
    const foo
      = 'superLongLongLongLongLongLongLongLongString';
    
    // 好评
    const foo = (
      superLongLongLongLongLongLongLongLongFunctionName()
    );
    
    // 好评
    const foo = 'superLongLongLongLongLongLongLongLongString';
    ```

  - [13.8](#variables--no-unused-vars) 禁止未使用的变量。 eslint: [`no-unused-vars`](https://eslint.org/docs/rules/no-unused-vars)

    > 为什么？这种情况很可能是重构没有做完产生的。占用了代码的空间，同时容易让读者混淆。

    ```javascript
    // 差评
    
    var some_unused_var = 42;
    
    // 只写的变量不认为是使用了。
    var y = 10;
    y = 5;
    
    // 只是修改自身的读不认为是使用了。
    var z = 0;
    z = z + 1;
    
    // 未使用的函数参数
    function getX(x, y) {
        return x;
    }
    
    // 好评
    
    function getXPlusY(x, y) {
      return x + y;
    }
    
    var x = 1;
    var y = a + 2;
    
    alert(getXPlusY(x, y));
    
    // 'type' 被忽略了，虽然它没有被使用，因为这是在忽略某个对象中的某个属性。
    var { type, ...coords } = data;
    // 'coords' 现在是没有 'type' 属性的 'data' 对象。
    ```

**[↑ 回到最上方](#目录)**

## 提升

  - [14.1](#hoisting--about) `var` 声明会被提升至最近的函数作用域的顶部，但其赋值不会提升。 `let` 和 `const` 被赋予了一种新概念，称为暂时性死区[(Temporal Dead Zones)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let#Temporal_dead_zone)。这对于了解为什么 [typeof 不再安全](https://web.archive.org/web/20200121061528/http://es-discourse.com/t/why-typeof-is-no-longer-safe/15)相当重要

    ```javascript
    // 这样是运行不了的
    // （如果 notDefined 不是全局变量）
    function example() {
      console.log(notDefined); // => 抛出 ReferenceError
    }

    // 由于变量提升的原因，在引用变量后再声明变量是可以运行的
    // 但是变量的赋值 `true` 不会被提升
    function example() {
      console.log(declaredButNotAssigned); // => undefined
      var declaredButNotAssigned = true;
    }

    // 编译器会把函数声明提升到作用域的顶层，所以上述代码其实可以写成这样
    function example() {
      let declaredButNotAssigned;
      console.log(declaredButNotAssigned); // => undefined
      declaredButNotAssigned = true;
    }

    // 使用 const 和 let
    function example() {
      console.log(declaredButNotAssigned); // => 抛出 ReferenceError
      console.log(typeof declaredButNotAssigned); // => 抛出 ReferenceError
      const declaredButNotAssigned = true;
    }
    ```

  - [14.2](#hoisting--anon-expressions) 匿名函数表达式的变量名会被提升，但函数内容不会

    ```javascript
    function example() {
      console.log(anonymous); // => undefined

      anonymous(); // => TypeError anonymous is not a function

      var anonymous = function () {
        console.log('anonymous function expression');
      };
    }
    ```

  - [14.3](#hoisting--named-expresions) 命名函数表达式的变量名会被提升，但函数名和函数函数内容不会

    ```javascript
    function example() {
      console.log(named); // => undefined

      named(); // => TypeError named is not a function

      superPower(); // => ReferenceError superPower is not defined

      var named = function superPower() {
        console.log('Flying');
      };
    }

    // 当函数名和变量名相同时，结论也同样成立
    function example() {
      console.log(named); // => undefined

      named(); // => TypeError named is not a function

      var named = function named() {
        console.log('named');
      };
    }
    ```

  - [14.4](#hoisting--declarations) 函数声明的名称和函数体都会被提升，所以你知道该怎么写

    ```javascript
    function example() {
      superPower(); // => Flying

      function superPower() {
        console.log('Flying');
      }
    }
    ```

  - 想了解更多信息，参考 [Scoping & Hoisting](http://www.adequatelygood.com/2010/2/JavaScript-Scoping-and-Hoisting/) 来自 [Ben Cherry](http://www.adequatelygood.com/)

  > 注 BY [dancerphil](https://github.com/dancerphil)：没有特殊需要的时候，把定义放在使用之前就可以了。

**[↑ 回到最上方](#目录)**

## 比较运算符，等号

  - [15.1](#comparison--eqeqeq) 尽量使用 `===` 和 `!==` ，而不是 `==` 和 `!=` 。 eslint: [`eqeqeq`](https://eslint.org/docs/rules/eqeqeq.html)

  - [15.2](#comparison--if) 条件声明如 `if` 会把表达式的值转化为 `Boolean`，依据以下的规则：

    - **Objects** 被视为 **true**
    - **Undefined** 被视为 **false**
    - **Null** 被视为 **false**
    - **Booleans** 被视为 **boolean的值**
    - 如果 **Numbers** 是 **+0, -0, or NaN** ，将被视为 **false** ，否则被视为 **true**
    - 如果 **Strings** 是一个空字符串 `''` ，将被视为 **false** ，否则被视为 **true**

    ```javascript
    if ([0] && []) {
      // true
      // 数组（即使它是空的）是一个对象，而对象被视为 true
    }
    ```

  - [15.3](#comparison--shortcuts) 对于 `Boolean` 使用简写(Shortcuts)，而 `String` 和 `Number` 使用显式比较。

    ```javascript
    // 差评
    if (isValid === true) {
      // ...
    }
    
    // 好评
    if (isValid) {
      // ...
    }
    
    // 差评
    if (name) {
      // ...
    }
    
    // 好评
    if (name !== '') {
      // ...
    }
    
    // 差评
    if (collection.length) {
      // ...
    }
    
    // 好评
    if (collection.length > 0) {
      // ...
    }
    ```

  - [15.4](#comparison--moreinfo) 想了解更多信息，可以参考 Angus Croll 的 [Truth Equality and JavaScript](https://javascriptweblog.wordpress.com/2011/02/07/truth-equality-and-javascript/#more-2108)。

    ```javascript
    // 注 BY dancerphil：文章中的部分内容
    console.log([] == true); // false
    console.log(![] == true); // false
    console.log(!![] == true); // true
    if([]) {
      console.log('why?'); // why?
    }
    ```

  - [15.5](#comparison--switch-blocks) 如果在 `case` 和 `default` 中包含了宣告语法(lexical declarations)，比如 `let`, `const`, `function`, 和 `class`。那么用大括号来建立代码块。 eslint: [`no-case-declarations`](https://eslint.org/docs/rules/no-case-declarations.html)

    > 为什么？宣告语法在整个 `switch` 块都可见，但是只在我们定义的地方才得到了初始值，也就是当进入其所在的 `case` 时才触发。如果不加大括号，可能引起多个 `case` 尝试定义同一事物的情况。

    ```javascript
    // 差评
    switch (foo) {
      case 1:
        let x = 1;
        break;
      case 2:
        const y = 2;
        break;
      case 3:
        function f() {
          // ...
        }
        break;
      default:
        class C {}
    }

    // 好评
    switch (foo) {
      case 1: {
        let x = 1;
        break;
      }
      case 2: {
        const y = 2;
        break;
      }
      case 3: {
        function f() {
          // ...
        }
        break;
      }
      case 4:
        bar();
        break;
      default: {
        class C {}
      }
    }
    ```

  - [15.6](#comparison--nested-ternaries) 三元运算符通常应该是简单的单行表达式，而不是嵌套的。 eslint rules: [`no-nested-ternary`](https://eslint.org/docs/rules/no-nested-ternary.html).

    ```javascript
    // 差评
    const foo = maybe1 > maybe2
      ? "bar"
      : value1 > value2 ? "baz" : null;
    
    // 换成两个单独的三元运算符
    const maybeNull = value1 > value2 ? 'baz' : null;
    
    // 好一些
    const foo = maybe1 > maybe2
      ? 'bar'
      : maybeNull;
    
    // 最佳
    const foo = maybe1 > maybe2 ? 'bar' : maybeNull;
    ```

  - [15.7](#comparison--unneeded-ternary) 避免不必要的三元运算符。 eslint rules: [`no-unneeded-ternary`](https://eslint.org/docs/rules/no-unneeded-ternary.html).

    ```javascript
    // 差评
    const foo = a ? a : b;
    const bar = c ? true : false;
    const baz = c ? false : true;

    // 好评
    const foo = a || b;
    const bar = !!c;
    const baz = !c;
    ```

  - [15.8](#comparison--no-mixed-operators) 混合使用多种操作符时，用括号声明优先级，唯一的例外是 `+`， `-` 和 `**`，因为它们的优先级被大部分知道。推荐括起 `/` 和 `*`，根据场景不同它们的优先级是模棱两可的。 eslint: [`no-mixed-operators`](https://eslint.org/docs/rules/no-mixed-operators.html)

    > 为什么？增强可读性，并且更好的阐明开发者的意图。

    ```javascript
    // 差评
    const foo = a && b < 0 || c > 0 || d + 1 === 0;
    
    // 差评
    const bar = a ** b - 5 % d;
    
    // 差评
    // 有人可能会想成 (a || b) && c
    if (a || b && c) {
      return d;
    }
    
    // 差评
    const bar = a + b / c * d;
    
    // 好评
    const foo = (a && b < 0) || c > 0 || (d + 1 === 0);
    
    // 好评
    const bar = a ** b - (5 % d);
    
    // 好评
    if (a || (b && c)) {
      return d;
    }
    
    // 好评
    const bar = a + (b / c) * d;
    ```

**[↑ 回到最上方](#目录)**

## 块

  - [16.1](#blocks--braces) 用大括号包裹所有的多行代码块。 eslint: [`nonblock-statement-body-position`](https://eslint.org/docs/rules/nonblock-statement-body-position)

    ```javascript
    // 差评
    if (test)
      return false;

    // 好评
    if (test) return false;

    // 好评
    if (test) {
      return false;
    }

    // 差评
    function foo() { return false; }

    // 好评
    function bar() {
      return false;
    }
    ```

  - [16.2](#blocks--cuddled-elses) 如果你在使用带 `if` 和 `else` 的多行代码块，把 `else` 放在 `if` 代码块关闭括号的同一行。 eslint: [`brace-style`](https://eslint.org/docs/rules/brace-style.html)

    ```javascript
    // 差评
    if (test) {
      thing1();
      thing2();
    }
    else {
      thing3();
    }

    // 好评
    if (test) {
      thing1();
      thing2();
    } else {
      thing3();
    }
    ```

- [16.3](#blocks--no-else-return) 如果一个 `if` 块总是 `return` ，那么对应的 `else` 块是不必要的。且 `else if` 块可以写成多个 `if` 块（如果每个块都 return 的话）。 eslint: [`no-else-return`](https://eslint.org/docs/rules/no-else-return)

  ```javascript
  // 差评
  function foo() {
    if (x) {
      return x;
    } else {
      return y;
    }
  }
  
  // 差评
  function cats() {
    if (x) {
      return x;
    } else if (y) {
      return y;
    }
  }
  
  // 差评
  function dogs() {
    if (x) {
      return x;
    } else {
      if (y) {
        return y;
      }
    }
  }
  
  // 好评
  function foo() {
    if (x) {
      return x;
    }
    return y;
  }
  
  // 好评
  function cats() {
    if (x) {
      return x;
    }
    if (y) {
      return y;
    }
  }
  
  // 好评
  function dogs(x) {
    if (x) {
      if (z) {
        return y;
      }
    } else {
      return z;
    }
  }
  ```

**[↑ 回到最上方](#目录)**

## 控制声明

  - [17.1](#control-statements) 如果你的控制语句(`if`, `while`)过于长，你可以把他们分成多组，每组一行，以逻辑运算符开始每一行。

    > 为什么？与链式方法调用的模式保持一致，且有助于提升可读性。

    ```javascript
    // 差评
    if ((foo === 123 || bar === 'abc') && doesItLookGoodWhenItBecomesThatLong() && isThisReallyHappening()) {
      thing1();
    }
    
    // 差评
    if (foo === 123 &&
      bar === 'abc') {
      thing1();
    }
    
    // 差评
    if (foo === 123
      && bar === 'abc') {
      thing1();
    }
    
    // 差评
    if (
      foo === 123 &&
      bar === 'abc'
    ) {
      thing1();
    }
    
    // 好评
    if (
      foo === 123
      && bar === 'abc'
    ) {
      thing1();
    }
    
    // 好评
    if (
      (foo === 123 || bar === 'abc')
      && doesItLookGoodWhenItBecomesThatLong()
      && isThisReallyHappening()
    ) {
      thing1();
    }
    
    // 好评
    if (foo === 123 && bar === 'abc') {
      thing1();
    }
    ```

  - [17.2](#control-statements--value-selection) 不要使用选择运算符代替控制语句。

    ```javascript
    // 差评
    !isRunning && startRunning();
    
    // 好评
    if (!isRunning) {
      startRunning();
    }
    ```

**[↑ 回到最上方](#目录)**

## 注释

  - [18.1](#comments--multiline) 使用 `/** ... */` 作为多行注释。

    ```javascript
    // 差评
    // make() returns a new element
    // based on the passed in tag name
    function make(tag) {

      // ...

      return element;
    }

    // 好评
    /**
     * make() returns a new element
     * based on the passed-in tag name
     */
    function make(tag) {

      // ...

      return element;
    }
    ```

  - [18.2](#comments--singleline) 使用 `//` 作为单行注释。在需要注释的代码上方另起一行写注释。在注释前空一行，除非注释在块的开头。

    ```javascript
    // 差评
    const active = true;  // is current tab

    // 好评
    // is current tab
    const active = true;

    // 差评
    function getType() {
      console.log('fetching type...');
      // set the default type to 'no type'
      const type = this.type || 'no type';

      return type;
    }

    // 好评
    function getType() {
      console.log('fetching type...');

      // set the default type to 'no type'
      const type = this.type || 'no type';

      return type;
    }

    // 同样好评
    function getType() {
      // set the default type to 'no type'
      const type = this.type || 'no type';

      return type;
    }
    ```

  - [18.3](#comments--spaces) 所有注释前有一个空格。 eslint: [`spaced-comment`](https://eslint.org/docs/rules/spaced-comment)

    ```javascript
    // 差评
    //is current tab
    const active = true;
    
    // 好评
    // is current tab
    const active = true;
    
    // 差评
    /**
     *make() returns a new element
     *based on the passed-in tag name
     */
    function make(tag) {
      // ...
      return element;
    }
    
    // 好评
    /**
     * make() returns a new element
     * based on the passed-in tag name
     */
    function make(tag) {
      // ...
      return element;
    }
    ```

  - [18.4](#comments--actionitems) 在注释前加上 `FIXME` 或 `TODO` 可以帮助其他开发者快速的了解写这段注释的原因。也许你正在指出一个需要复查的问题，或者你正在给需要实现的功能提供一个解决方式。和一般的注解不用，它们是可被执行的，对应的动作为 `FIXME: -- 需要搞清楚这一点` 或者 `TODO: -- 需要实现`。

  - [18.5](#comments--fixme) 使用 `// FIXME:` 标注问题。

    ```javascript
    class Calculator extends Abacus {
      constructor() {
        super();

        // FIXME: 这里不应该存在全局变量
        total = 0;
      }
    }
    ```

  - [18.6](#comments--todo) 使用 `// TODO:` 标注问题的解决方式。

    ```javascript
    class Calculator extends Abacus {
      constructor() {
        super();

        // TODO: total 应该是个可被传入的可选变量
        this.total = 0;
      }
    }
    ```

**[↑ 回到最上方](#目录)**

## 空格

  - [19.1](#whitespace--spaces) 使用两个空格作为缩进。 eslint: [`indent`](https://eslint.org/docs/rules/indent.html)

  > 注 BY [dancerphil](https://github.com/dancerphil)：四空格缩进也可以，统一即可。

    ```javascript
    // 差评
    function foo() {
    ∙∙∙∙let name;
    }

    // 差评
    function bar() {
    ∙let name;
    }

    // 好评
    function baz() {
    ∙∙let name;
    }
    ```

  - [19.2](#whitespace--before-blocks) 在起始的大括号 `{` 的左侧放上一个空格。 eslint: [`space-before-blocks`](https://eslint.org/docs/rules/space-before-blocks.html)

    ```javascript
    // 差评
    function test(){
      console.log('test');
    }

    // 好评
    function test() {
      console.log('test');
    }

    // 差评
    dog.set('attr',{
      age: '1 year',
      breed: 'Bernese Mountain Dog',
    });

    // 好评
    dog.set('attr', {
      age: '1 year',
      breed: 'Bernese Mountain Dog',
    });
    ```

  - [19.3](#whitespace--around-keywords) 在控制语句 `if`, `while` 的括号前放一个空格。在函数调用及声明中，括号前不加空格。 eslint: [`keyword-spacing`](https://eslint.org/docs/rules/keyword-spacing.html)

    ```javascript
    // 差评
    if(isJedi) {
      fight ();
    }

    // 好评
    if (isJedi) {
      fight();
    }

    // 差评
    function fight () {
      console.log ('Swooosh!');
    }

    // 好评
    function fight() {
      console.log('Swooosh!');
    }
    ```

  - [19.4](#whitespace--infix-ops) 使用空格隔开运算符。 eslint: [`space-infix-ops`](https://eslint.org/docs/rules/space-infix-ops.html)

    >  注 BY [dancerphil](https://github.com/dancerphil)：不要用空格分开一元运算符，比如 `let dog = isHuman && !hasGirl()`，也不要在逗号 `,` 分号 `;` 的左侧加空格

    ```javascript
    // 差评
    const x=y+5;

    // 好评
    const x = y + 5;
    ```

  - [19.5](#whitespace--newline-at-end) 在文件末尾插入一个空行。 eslint: [`eol-last`](https://github.com/eslint/eslint/blob/master/docs/rules/eol-last.md)

    ```javascript
    // 差评
    import { es6 } from './AirbnbStyleGuide';
      // ...
    export default es6;
    ```

    ```javascript
    // 差评
    import { es6 } from './AirbnbStyleGuide';
      // ...
    export default es6;↵
    ↵
    ```

    ```javascript
    // 好评
    import { es6 } from './AirbnbStyleGuide';
      // ...
    export default es6;↵
    ```

  - [19.6](#whitespace--chains) 链式调用时（超过两个方法的链），使用符合调用逻辑的缩进，并且在方法的左侧放置点 `.`，不要放到后面，强调这是方法调用而不是新语句。 eslint: [`newline-per-chained-call`](https://eslint.org/docs/rules/newline-per-chained-call) [`no-whitespace-before-property`](https://eslint.org/docs/rules/no-whitespace-before-property)

    ```javascript
    // 差评
    $('#items').find('.selected').highlight().end().find('.open').updateCount();

    // 差评
    $('#items').
      find('.selected').
        highlight().
        end().
      find('.open').
        updateCount();

    // 好评
    $('#items')
      .find('.selected')
        .highlight()
        .end()
      .find('.open')
        .updateCount();

    // 差评
    const leds = stage.selectAll('.led').data(data).enter().append('svg:svg').classed('led', true)
        .attr('width', (radius + margin) * 2).append('svg:g')
        .attr('transform', `translate(${radius + margin},${radius + margin})`)
        .call(tron.led);

    // 好评
    const leds = stage.selectAll('.led')
        .data(data)
      .enter().append('svg:svg')
        .classed('led', true)
        .attr('width', (radius + margin) * 2)
      .append('svg:g')
        .attr('transform', `translate(${radius + margin},${radius + margin})`)
        .call(tron.led);

    // 好评
    const leds = stage.selectAll('.led').data(data);
    const svg = leds.enter().append('svg:svg');
    svg.classed('led', true).attr('width', (radius + margin) * 2);
    const g = svg.append('svg:g');
    g.attr('transform', `translate(${radius + margin},${radius + margin})`).call(tron.led);
    ```

  - [19.7](#whitespace--after-blocks) 在块结束后、新语句开始前，插入一个空行。

    ```javascript
    // 差评
    if (foo) {
      return bar;
    }
    return baz;

    // 好评
    if (foo) {
      return bar;
    }

    return baz;

    // 差评
    const obj = {
      foo() {
      },
      bar() {
      },
    };
    return obj;

    // 好评
    const obj = {
      foo() {
      },

      bar() {
      },
    };

    return obj;

    // 差评
    const arr = [
      function foo() {
      },
      function bar() {
      },
    ];
    return arr;

    // 好评
    const arr = [
      function foo() {
      },

      function bar() {
      },
    ];

    return arr;
    ```

  - [19.8](#whitespace--padded-blocks) 不要使用空白行来垫起你的代码块。 eslint: [`padded-blocks`](https://eslint.org/docs/rules/padded-blocks.html)

    ```javascript
    // 差评
    function bar() {

      console.log(foo);

    }

    // 差评
    if (baz) {

      console.log(qux);
    } else {
      console.log(foo);

    }
    
    // 差评
    class Foo {
      constructor(bar) {
        this.bar = bar;
      }
    }
    
    // 好评
    function bar() {
      console.log(foo);
    }

    // 好评
    if (baz) {
      console.log(qux);
    } else {
      console.log(foo);
    }
    ```

  - [19.9](#whitespace--no-multiple-blanks) 不要使用多个空行来垫起你的代码块。 eslint: [`no-multiple-empty-lines`](https://eslint.org/docs/rules/no-multiple-empty-lines)

    <!-- markdownlint-disable MD012 -->
    ```javascript
    // 差评
    class Person {
      constructor(fullName, email, birthday) {
        this.fullName = fullName;
    
    
        this.email = email;
    
    
        this.setAge(birthday);
      }
    
    
      setAge(birthday) {
        const today = new Date();
    
    
        const age = this.getAge(today, birthday);
    
    
        this.age = age;
      }
    
    
      getAge(today, birthday) {
        // ..
      }
    }
    
    // 好评
    class Person {
      constructor(fullName, email, birthday) {
        this.fullName = fullName;
        this.email = email;
        this.setAge(birthday);
      }
    
      setAge(birthday) {
        const today = new Date();
        const age = getAge(today, birthday);
        this.age = age;
      }
    
      getAge(today, birthday) {
        // ..
      }
    }
    ```

  - [19.10](#whitespace--in-parens) 不要在括号里加空格。 eslint: [`space-in-parens`](https://eslint.org/docs/rules/space-in-parens.html)

    ```javascript
    // 差评
    function bar( foo ) {
      return foo;
    }

    // 好评
    function bar(foo) {
      return foo;
    }

    // 差评
    if ( foo ) {
      console.log(foo);
    }

    // 好评
    if (foo) {
      console.log(foo);
    }
    ```

  - [19.11](#whitespace--in-brackets) 不要在方括号内加空格。 eslint: [`array-bracket-spacing`](https://eslint.org/docs/rules/array-bracket-spacing.html)

    ```javascript
    // 差评
    const foo = [ 1, 2, 3 ];
    console.log(foo[ 0 ]);

    // 好评
    const foo = [1, 2, 3];
    console.log(foo[0]);
    ```

  - [19.12](#whitespace--in-braces) 不要在花括号内加空格。 eslint: [`object-curly-spacing`](https://eslint.org/docs/rules/object-curly-spacing.html)

    ```javascript
    // 差评
    const foo = {clark: 'kent'};

    // 好评
    const foo = { clark: 'kent' };
    ```

  - [19.13](#whitespace--max-len) 避免你的某一行代码超过了 100 字符，当然空格也算在里面。**注意**：长字符串除外。 eslint: [`max-len`](https://eslint.org/docs/rules/max-len.html)

    > 为什么？这种做法保证了可读性和可维护性

    ```javascript
    // 差评
    const foo = jsonData && jsonData.foo && jsonData.foo.bar && jsonData.foo.bar.baz && jsonData.foo.bar.baz.quux && jsonData.foo.bar.baz.quux.xyzzy;

    // 差评
    $.ajax({ method: 'POST', url: 'https://airbnb.com/', data: { name: 'John' } }).done(() => console.log('Congratulations!')).fail(() => console.log('You have failed this city.'));

    // 好评
    const foo = jsonData
      && jsonData.foo
      && jsonData.foo.bar
      && jsonData.foo.bar.baz
      && jsonData.foo.bar.baz.quux
      && jsonData.foo.bar.baz.quux.xyzzy;

    // 好评
    $.ajax({
      method: 'POST',
      url: 'https://airbnb.com/',
      data: { name: 'John' },
    })
      .done(() => console.log('Congratulations!'))
      .fail(() => console.log('You have failed this city.'));
    ```

**[↑ 回到最上方](#目录)**

## 逗号

  - [20.1](#commas--leading-trailing) 不要使用行首逗号。 eslint: [`comma-style`](https://eslint.org/docs/rules/comma-style.html)

    ```javascript
    // 差评
    const story = [
        once
      , upon
      , aTime
    ];

    // 好评
    const story = [
      once,
      upon,
      aTime,
    ];

    // 差评
    const hero = {
        firstName: 'Ada'
      , lastName: 'Lovelace'
      , birthYear: 1815
      , superPower: 'computers'
    };

    // 好评
    const hero = {
      firstName: 'Ada',
      lastName: 'Lovelace',
      birthYear: 1815,
      superPower: 'computers',
    };
    ```

  - [20.2](#commas--dangling) 在结尾增加额外的逗号。 eslint: [`comma-dangle`](https://eslint.org/docs/rules/comma-dangle.html)

    > 为什么? 这会让 git diffs 更干净。另外，像 babel 这样的转译器会移除结尾多余的逗号，也就是说你不必担心老旧浏览器的 [行尾逗号问题](https://github.com/airbnb/javascript/blob/es5-deprecated/es5/README.md#commas)。

    ```diff
    // 差评 - 没有行尾逗号时， git diff 是这样的
    const hero = {
         firstName: 'Florence',
    -    lastName: 'Nightingale'
    +    lastName: 'Nightingale',
    +    inventorOf: ['coxcomb chart', 'modern nursing']
    };

    // 好评 - 有行尾逗号时， git diff 是这样的
    const hero = {
         firstName: 'Florence',
         lastName: 'Nightingale',
    +    inventorOf: ['coxcomb chart', 'modern nursing'],
    };
    ```

    ```javascript
    // 差评
    const hero = {
      firstName: 'Dana',
      lastName: 'Scully'
    };

    const heroes = [
      'Batman',
      'Superman'
    ];

    // 好评
    const hero = {
      firstName: 'Dana',
      lastName: 'Scully',
    };

    const heroes = [
      'Batman',
      'Superman',
    ];
    
    // 差评
    function createHero(
      firstName,
      lastName,
      inventorOf
    ) {
      // ...
    }
    
    // 好评
    function createHero(
      firstName,
      lastName,
      inventorOf,
    ) {
      // ...
    }
    
    // 好评 - 注意 rest 语法后不需要逗号
    function createHero(
      firstName,
      lastName,
      inventorOf,
      ...heroArgs
    ) {
      // ...
    }
    
    // 差评
    createHero(
      firstName,
      lastName,
      inventorOf
    );
    
    // 好评
    createHero(
      firstName,
      lastName,
      inventorOf,
    );
    
    // 好评 - 注意 rest 语法后不需要逗号
    createHero(
      firstName,
      lastName,
      inventorOf,
      ...heroArgs
    );
    ```

**[↑ 回到最上方](#目录)**

## 分号

  - [21.1](#20.1) **要** 。 eslint: [`semi`](https://eslint.org/docs/rules/semi.html)

    > 为什么？当 JavaScript 遇到没有分号的换行符是，它会使用一套规则  [Automatic Semicolon Insertion](https://tc39.github.io/ecma262/#sec-automatic-semicolon-insertion) 来决定是否视为语句语句的结束，并且自动添加一个分号。ASI 包含一些异常行为，并且如果 JavaScript 错误的解释了换行符，代码将中断。随着新功能的加入，这些规则也越来越复杂。显式的声明分号有助于防止你遇到问题。

    ```javascript
    // 差评 - 产生报错
    const luke = {}
    const leia = {}
    [luke, leia].forEach((jedi) => jedi.father = 'vader')
    
    // 差评 - 产生报错
    const reaction = "No! That’s impossible!"
    (async function meanwhileOnTheFalcon() {
      // handle `leia`, `lando`, `chewie`, `r2`, `c3p0`
      // ...
    }())
    
    // 差评 - 返回 `undefined` 而不是第二行的值。
    function foo() {
      return
        'search your feelings, you know it to be foo'
    }
    
    // 好评
    const luke = {};
    const leia = {};
    [luke, leia].forEach((jedi) => {
      jedi.father = 'vader';
    });
    
    // 好评
    const reaction = "No! That’s impossible!";
    (async function meanwhileOnTheFalcon() {
      // handle `leia`, `lando`, `chewie`, `r2`, `c3p0`
      // ...
    }());
    
    // 好评
    function foo() {
      return 'search your feelings, you know it to be foo';
    }
    ```
    
    [IIFE](#7.2) - 立即调用的函数表达式

    [在 stackoverflow 阅读更多](https://stackoverflow.com/questions/7365172/semicolon-before-self-invoking-function/7365214#7365214)

**[↑ 回到最上方](#目录)**

## 类型，强制类型转换

  - [22.1](#coercion--explicit) 执行类型转换的位置应当是语句的开头。

  - [22.2](#coercion--strings) 对字符串：

    ```javascript
    // => this.reviewScore = 9;
    
    // 差评
    const totalScore = new String(this.reviewScore); // totalScore 是一个对象而非字符串
    
    // 差评
    const totalScore = this.reviewScore + ''; // 调用 this.reviewScore.valueOf()

    // 差评
    const totalScore = this.reviewScore.toString(); // 不能保证返回字符串

    // 好评
    const totalScore = String(this.reviewScore);
    ```

  - [21.3](#coercion--numbers) 数字：使用 `Number` 来进行类型转换，在使用 `parseInt` 时，总是带上类型转换的基数。 eslint: [`radix`](https://eslint.org/docs/rules/radix) [`no-new-wrappers`](https://eslint.org/docs/rules/no-new-wrappers)

    > 为什么？`parseInt` 在没有指定进位基数的时候，不同的浏览器对于八进制和十六进制的行为不同，且与 ECMAScript 3 的规范不一致。

    ```javascript
    const inputValue = '4';

    // 差评
    const val = new Number(inputValue);

    // 差评
    const val = +inputValue;

    // 差评
    const val = inputValue >> 0;

    // 差评
    const val = parseInt(inputValue);

    // 好评
    const val = Number(inputValue);

    // 好评
    const val = parseInt(inputValue, 10);
    ```

  - [22.4](#coercion--comment-deviations) 如果因为某些原因， `parseInt` 成为了[性能瓶颈](https://jsperf.com/coercion-vs-casting/3)，如果你一定要使用 **位操作** (Bitshift)，那么留个注释解释一下原因吧。

    ```javascript
    // 好评
    /**
     * parseInt 使我的代码变慢了
     * 改成使用位操作后来提升性能
     */
    const val = inputValue >> 0;
    ```

  - [22.5](#coercion--bitwise) **注意** : 小心使用位操作运算符。数字会被当成 [64位变量](https://es5.github.io/#x4.3.19)，但是位操作运算符总是返回 32 位的整数([参考](https://es5.github.io/#x11.7))。位操作在处理大于 32 位的整数值时，并不会如你所料。[相关讨论](https://github.com/airbnb/javascript/issues/109)。最大的 32 位整数是 2,147,483,647 ：

    ```javascript
    2147483647 >> 0; // => 2147483647
    2147483648 >> 0; // => -2147483648
    2147483649 >> 0; // => -2147483647
    ```

  - [22.6](#coercion--booleans) 布尔值：eslint: [`no-new-wrappers`](https://eslint.org/docs/rules/no-new-wrappers)

    ```javascript
    const age = 0;

    // 差评
    const hasAge = new Boolean(age);

    // 好评
    const hasAge = Boolean(age);

    // 最佳
    const hasAge = !!age;
    ```

**[↑ 回到最上方](#目录)**

## 命名规则

  - [23.1](#naming--descriptive) 别用单字母命名。让你的命名具备描述性。 eslint: [`id-length`](https://eslint.org/docs/rules/id-length)

    ```javascript
    // 差评
    function q() {
      // ...
    }

    // 好评
    function query() {
      // ...
    }
    ```

  - [23.2](#naming--camelCase) 使用驼峰式命名对象、函数和实例。 eslint: [`camelcase`](https://eslint.org/docs/rules/camelcase.html)

    ```javascript
    // 差评
    const OBJEcttsssss = {};
    const this_is_my_object = {};
    function c() {}

    // 好评
    const thisIsMyObject = {};
    function thisIsMyFunction() {}
    ```

  - [23.3](#naming--PascalCase) 使用帕斯卡式命名构造函数或类。 eslint: [`new-cap`](https://eslint.org/docs/rules/new-cap.html)

    ```javascript
    // 差评
    function user(options) {
      this.name = options.name;
    }

    const bad = new user({
      name: 'nope',
    });

    // 好评
    class User {
      constructor(options) {
        this.name = options.name;
      }
    }

    const good = new User({
      name: 'yup',
    });
    ```

  - [23.4](#naming--leading-underscore) 不要以下划线作为开头或结尾。 eslint: [`no-underscore-dangle`](https://eslint.org/docs/rules/no-underscore-dangle.html)

    > 为什么？事实上，Javascript 没有隐私方面的概念。虽然在一般的约定中，在前面加下划线的意思是 private ，但事实上，这些属性完完全全的就是公开的，也因此，意味着它们也是你公共 API 的一部分。这个一般的约定误导一部分程序员，使他们错误的认为一个小小的变化不会引起什么结果，或者认为测试是可有可无的。简单的说，如果你真的需要一些私密的属性，它就不应该被人看到。

    ```javascript
    // 差评
    this.__firstName__ = 'Panda';
    this.firstName_ = 'Panda';
    this._firstName = 'Panda';

    // 好评
    this.firstName = 'Panda';
    
    // 好评
    // 参见 https://kangax.github.io/compat-table/es6/#test-WeakMap
    const firstNames = new WeakMap();
    firstNames.set(this, 'Panda');
    ```

  - [23.5](#naming--self-this) 别保存 `this` 的引用。使用箭头函数或 [Function#bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)。

    ```javascript
    // 差评
    function foo() {
      const self = this;
      return function () {
        console.log(self);
      };
    }

    // 差评
    function foo() {
      const that = this;
      return function () {
        console.log(that);
      };
    }

    // 好评
    function foo() {
      return () => {
        console.log(this);
      };
    }
    ```

  - [23.6](#naming--filename-matches-export) 你的文件名应当和默认的类名保持完全一致。

    ```javascript
    // file 1 contents
    class CheckBox {
      // ...
    }
    export default CheckBox;

    // file 2 contents
    export default function fortyTwo() { return 42; }

    // file 3 contents
    export default function insideDirectory() {}

    // 然后在其他的一些文件里
    // 差评
    import CheckBox from './checkBox'; // PascalCase import/export, camelCase filename
    import FortyTwo from './FortyTwo'; // PascalCase import/filename, camelCase export
    import InsideDirectory from './InsideDirectory'; // PascalCase import/filename, camelCase export

    // 差评
    import CheckBox from './check_box'; // PascalCase import/export, snake_case filename
    import forty_two from './forty_two'; // snake_case import/filename, camelCase export
    import inside_directory from './inside_directory'; // snake_case import, camelCase export
    import index from './inside_directory/index'; // requiring the index file explicitly
    import insideDirectory from './insideDirectory/index'; // requiring the index file explicitly

    // 好评
    import CheckBox from './CheckBox'; // PascalCase export/import/filename
    import fortyTwo from './fortyTwo'; // camelCase export/import/filename
    import insideDirectory from './insideDirectory'; // camelCase export/import/directory name/implicit "index"
    // ^ supports both insideDirectory.js and insideDirectory/index.js
    ```

  - [23.7](#naming--camelCase-default-export) 使用驼峰式命名导出默认函数。你的文件名应当和函数名保持完全一致。

    ```javascript
    function makeStyleGuide() {
      // ...
    }

    export default makeStyleGuide;
    ```

  - [23.8](#naming--PascalCase-singleton) 使用帕斯卡式命名导出构造函数、类、单例(singleton)、函数库、空对象。

    ```javascript
    const AirbnbStyleGuide = {
      es6: {
      },
    };

    export default AirbnbStyleGuide;
    ```

  - [23.9](#naming--Acronyms-and-Initialisms) 缩写词应该始终全大写，或始终全小写。

    > 为什么？可读性比向一个计算机算法妥协更重要。

    ```javascript
    // 差评
    import SmsContainer from './containers/SmsContainer';
    
    // 差评
    const HttpRequests = [
      // ...
    ];
    
    // 好评
    import SMSContainer from './containers/SMSContainer';
    
    // 好评
    const HTTPRequests = [
      // ...
    ];
    
    // 好评
    const httpRequests = [
      // ...
    ];
    
    // 最佳
    import TextMessageContainer from './containers/TextMessageContainer';
    
    // 最佳
    const requests = [
      // ...
    ];
    ```

  - [23.10](#naming--uppercase) 对于以下变量采用大写：1、被导出，2、是 `const`，3、开发者可以信任它（以及他的属性）不变。
    
    > 为什么？在开发者不确定一个值是否会改变的时候，UPPERCASE_VARIABLES 提供了一种信任。

      - 普通的 `const` 变量，没有必要，对于单个文件内的变量不需要大写。

      - 被导出的对象，只在最外层大写（如 `EXPORTED_OBJECT.key`），此对象的所有嵌套的属性都不会改变。

    ```javascript
    // 差评
    const PRIVATE_VARIABLE = 'should not be unnecessarily uppercased within a file';
    
    // 差评
    export const THING_TO_BE_CHANGED = 'should obviously not be uppercased';
    
    // 差评
    export let REASSIGNABLE_VARIABLE = 'do not use let with uppercase variables';
    
    // ---
    
    // 可以，但不保证其值不改变
    export const apiKey = 'SOMEKEY';
    
    // 大多数情况下更好
    export const API_KEY = 'SOMEKEY';
    
    // ---
    
    // 差评
    export const MAPPING = {
      KEY: 'value'
    };
    
    // 好评
    export const MAPPING = {
      key: 'value'
    };
    ```

**[↑ 回到最上方](#目录)**

## 存取器

  - [24.1](#accessors--not-required) 并非所有属性的存取器函数都是必须的。

  - [24.2](#accessors--no-getters-setters) 不要使用 JavaScript 的 getters/setters ，因为它们会导致你不期待的副作用，而且使测试、维护和推论。如果你一定要存取器函数，使用 `getVal()` 和 `setVal('hello')`。

    ```javascript
    // 差评
    class Dragon {
      get age() {
        // ...
      }

      set age(value) {
        // ...
      }
    }

    // 好评
    class Dragon {
      getAge() {
        // ...
      }

      setAge(value) {
        // ...
      }
    }
    ```

  - [24.3](#accessors--boolean-prefix) 如果属性或方法是一个 `boolean` ，使用 `isVal()` 或 `hasVal()`。

    ```javascript
    // 差评
    if (!dragon.age()) {
      return false;
    }

    // 好评
    if (!dragon.hasAge()) {
      return false;
    }
    ```

  - [24.4](#accessors--consistent) 可以创建 `get()` 和 `set()` 函数，但是注意保持一致。

    ```javascript
    class Jedi {
      constructor(options = {}) {
        const lightsaber = options.lightsaber || 'blue';
        this.set('lightsaber', lightsaber);
      }

      set(key, val) {
        this[key] = val;
      }

      get(key) {
        return this[key];
      }
    }
    ```

**[↑ 回到最上方](#目录)**

## 事件

  - [25.1](#events--hash) 当事件需要一些附加数据时（无论是 DOM 事件还是私有事件），传入一个对象（也被称为哈希）而不是原始值。这样可以让后面的开发者简单的增加更多数据到事件中，而无需找出并更新事件的每一个处理器。比如不要写：

    ```javascript
    // 差评
    $(this).trigger('listingUpdated', listing.id);
    
    // ...
    
    $(this).on('listingUpdated', (e, listingID) => {
      // 使用 listingID
    });
    ```

    而写：

    ```javascript
    // 好评
    $(this).trigger('listingUpdated', { listingID: listing.id });

    // ...

    $(this).on('listingUpdated', (e, data) => {
      // 使用 data.listingID
    });
    ```

  **[↑ 回到最上方](#目录)**


## jQuery

  - [26.1](#jquery--dollar-prefix) jQuery 对象的变量名统一加上前缀 `$`。

    ```javascript
    // 差评
    const sidebar = $('.sidebar');

    // 好评
    const $sidebar = $('.sidebar');

    // 好评
    const $sidebarBtn = $('.sidebar-btn');
    ```

  - [26.2](#jquery--cache) 缓存 jQuery 查询，避免无用的查询。

    ```javascript
    // 差评
    function setSidebar() {
      $('.sidebar').hide();

      // ...

      $('.sidebar').css({
        'background-color': 'pink',
      });
    }

    // 好评
    function setSidebar() {
      const $sidebar = $('.sidebar');
      $sidebar.hide();

      // ...

      $sidebar.css({
        'background-color': 'pink',
      });
    }
    ```

  - [26.3](#jquery--queries) 查询 DOM 时，使用层叠 `$('.sidebar ul')` 或 parent > child 的模式 `$('.sidebar > ul')`。 [jsPerf](http://jsperf.com/jquery-find-vs-context-sel/16)

  - [26.4](#jquery--find) 查询有作用域的 jQuery 对象时，使用 `find`。

    ```javascript
    // 差评
    $('ul', '.sidebar').hide();

    // 差评
    $('.sidebar').find('ul').hide();

    // 好评
    $('.sidebar ul').hide();

    // 好评
    $('.sidebar > ul').hide();

    // 好评
    $sidebar.find('ul').hide();
    ```

**[↑ 回到最上方](#目录)**

## ECMAScript 5 兼容性

  - [27.1](#es5-compat--kangax) 参考 [Kangax](https://twitter.com/kangax/) 的 ES5 [兼容性表格](https://kangax.github.io/es5-compat-table/)。

**[↑ 回到最上方](#目录)**

## ES6+ 代码风格

  - [28.1](#es6-styles) ES6 代码风格分布在各个篇目中，我们整理了一下，以下是链接到 ES6 的各个特性的列表：

1. [箭头函数 Arrow Functions](#箭头函数)
1. [类 Classes](#类构造函数)
1. [对象方法简写 Object Shorthand](#对象方法简写)
1. [对象属性简写 Object Concise](#对象属性简写)
1. [对象可计算属性 Object Computed Properties](#对象可计算属性)
1. [模版字符串 Template Strings](#模版字符串)
1. [解构 Destructuring](#解构)
1. [默认参数 Default Parameters](#默认参数)
1. [Rest](#rest)
1. [拓展运算符 Array Spreads](#拓展运算符)
1. [Let 和 Const](#引用)
1. [阶乘运算符 Exponentiation Operator](#阶乘运算符)
1. [迭代器和生成器 Iterators and Generators](#迭代器生成器)
1. [模块 Modules](#模块)

  - [28.2](#tc39-proposals) 在 [TC39 提案](https://github.com/tc39/proposals) 没有到达 stage 3 前不要使用他们。
    
    > 为什么？[他们没有最终确定](https://tc39.github.io/process-document/)，且可能被更改和撤销。

**[↑ 回到最上方](#目录)**

# 标准库
  
  [标准库](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects) 中存在一些功能已经损坏，但仍然保留的工具。

  - [29.1](#standard-library--isnan) 使用 `Number.isNaN` 代替 `isNaN`。 eslint: [`no-restricted-globals`](https://eslint.org/docs/rules/no-restricted-globals)

    > 为什么？`isNaN` 会强制转化非数字类型，然后对于被转化为 `NaN` 的任何值返回 `true`。
    > 
    > 如果这是期望的行为，显式声明它。

    ```javascript
    // 差评
    isNaN('1.2'); // false
    isNaN('1.2.3'); // true
    
    // 好评
    Number.isNaN('1.2.3'); // false
    Number.isNaN(Number('1.2.3')); // true
    ```

  - [29.2](#standard-library--isfinite) 使用 `Number.isFinite` 代替 `isFinite`。 eslint: [`no-restricted-globals`](https://eslint.org/docs/rules/no-restricted-globals)
    
    > 为什么？`isFinite` 会强制转化非数字类型，然后对于被转化为有限值的任何值返回 `true`。
    > 
    > 如果这是期望的行为，显式声明它。

    ```javascript
    // 差评
    isFinite('2e3'); // true
    
    // 好评
    Number.isFinite('2e3'); // false
    Number.isFinite(parseInt('2e3', 10)); // true
    ```

**[↑ 回到最上方](#目录)**

## 测试

  - [30.1](#testing--yup) **好**

    ```javascript
    function foo() {
      return true;
    }
    ```

  - [30.2](#testing--for-real) **认真一点：**
    - 不论你使用了什么测试框架，你都应该写测试！
    - 尽量写很多小的纯函数，这样你可以在很小的区域里探知异常发生的地点
    - 对 stubs 和 mocks 保持严谨 - 他们使你的测试变得更加脆弱
    - 在 Airbnb 我们主要使用 [`mocha`](https://www.npmjs.com/package/mocha) 和 [`jest`](https://www.npmjs.com/package/jest)，对一些小型或单独的模块会使用 [`tape`](https://www.npmjs.com/package/tape)
    - 达到 100% 测试覆盖率，虽然实现它并不总是符合实际，但这是一个非常好的目标
    - 每当你修复一个 bug ，就为这个 bug 写 **回归测试** 。一个修复的 bug 如果没有回归测试，一般而言都会复发

**[↑ 回到最上方](#目录)**

## 性能

[前往原文](https://github.com/airbnb/javascript#resources)

**[↑ 回到最上方](#目录)**

## 许可

(The MIT License)

Copyright (c) 2012 Airbnb

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

> **警告** ：转载时必须包含此许可证

**[↑ 回到最上方](#目录)**

## 修订

非常鼓励你 [`fork`](https://github.com/dancerphil/trick) 这份代码风格指南，稍加改动后作为你们团队的指南。

# };
