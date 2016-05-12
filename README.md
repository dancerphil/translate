# JavaScript 代码风格指南() {

*如何用最合理的方式写你的 JavaScript 代码*

> BY 张聪([dancerphil@github](https://github.com/dancerphil/trick/))
> 
> 这是一篇在[原文](https://github.com/airbnb/javascript)基础上演绎的译文，与原文的表达会有出入
> 
> 原文在不断的更新，本文基于 2016-05-12 的版本，last commit [ [b660d4c] ](https://github.com/airbnb/javascript/commit/b660d4cc3f0497b6ffd981857ffef68edec729ee)
> 
> 除非另行注明，页面上所有内容采用[MIT](#license)协议共享

[![Downloads](https://img.shields.io/npm/dm/eslint-config-airbnb.svg)](https://www.npmjs.com/package/eslint-config-airbnb)
[![Downloads](https://img.shields.io/npm/dm/eslint-config-airbnb-base.svg)](https://www.npmjs.com/package/eslint-config-airbnb-base)
[![Gitter](https://badges.gitter.im/Join Chat.svg)](https://gitter.im/airbnb/javascript?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

其他的代码指南(Style Guides)
 - [ES5 中文版](https://github.com/sivan/javascript-style-guide/blob/master/es5/README.md)
 - [React](https://github.com/airbnb/javascript/tree/master/react)
 - [CSS & Sass](https://github.com/airbnb/css)
 - [Ruby](https://github.com/airbnb/ruby)

<a name="table-of-contents"></a>
## 目录

  1. [类型 Types](#types)
  1. [引用 References](#references)
  1. [对象 Objects](#objects)
  1. [数组 Arrays](#arrays)
  1. [解构 Destructuring](#destructuring) [（注：MDN 解构赋值）](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)
  1. [字符串 Strings](#strings)
  1. [函数 Functions](#functions)
  1. [箭头函数 Arrow Functions](#arrow-functions)
  1. [类，构造函数 Classes & Constructors](#classes--constructors)
  1. [模块 Modules](#modules)
  1. [迭代器，生成器 Iterators and Generators](#iterators-and-generators)
  1. [属性 Properties](#properties)
  1. [变量 Variables](#variables)
  1. [提升 Hoisting](#hoisting)
  1. [比较运算符，等号 Comparison Operators & Equality](#comparison-operators--equality)
  1. [块 Blocks](#blocks)
  1. [注释 Comments](#comments)
  1. [空格 Whitespace](#whitespace)
  1. [逗号 Commas](#commas)
  1. [分号 Semicolons](#semicolons)
  1. [类型，强制类型转换 Type Casting & Coercion](#type-casting--coercion)
  1. [命名规则 Naming Conventions](#naming-conventions)
  1. [存取器 Accessors](#accessors) (getter,setter)
  1. [事件 Events](#events)
  1. [jQuery](#jquery)
  1. [ECMAScript 5 兼容](#ecmascript-5-compatibility)
  1. [ES6 代码风格](#ecmascript-6-styles)
  1. [测试 Testing](#testing)
  1. [性能 Performance](#performance)
  1. [学习资源 Resources](#resources)
  1. ~~使用人群~~
  1. ~~其他翻译~~
  1. ~~指南的指南~~
  1. [和我们一起讨论JS](#chat-with-us-about-javascript)
  1. [贡献者](#contributors)
  1. [许可 License](#license)

<a name="types"></a>
## 类型

  <a name="types--primitives"></a><a name="1.1"></a>
  - [1.1](#types--primitives) **基本类型**： 你直接访问到基本类型的值

    + `string`
    + `number`
    + `boolean`
    + `null`
    + `undefined`

    ```javascript
    const foo = 1;
    let bar = foo;

    bar = 9;

    console.log(foo, bar); // => 1, 9
    ```

  <a name="types--complex"></a><a name="1.2"></a>
  - [1.2](#types--complex)  **复杂类型**: 你访问到复杂类型的引用，通过引用得到值

    + `object`
    + `array`
    + `function`

    ```javascript
    const foo = [1, 2];
    const bar = foo;

    bar[0] = 9;

    console.log(foo[0], bar[0]); // => 9, 9
    ```

**[↑ 回到最上方](#目录)**

<a name="references"></a>
## 引用

  <a name="references--prefer-const"></a><a name="2.1"></a>
  - [2.1](#references--prefer-const) 你的所有引用都应该使用 `const` ； 避免使用 `var` 。 eslint: [`prefer-const`](http://eslint.org/docs/rules/prefer-const.html), [`no-const-assign`](http://eslint.org/docs/rules/no-const-assign.html)

    > 为什么？这就确保了你不会对引用(reference)重新赋值，否则会出现难以理解的 bug 

    ```javascript
    // 差评
    var a = 1;
    var b = 2;

    // 好评
    const a = 1;
    const b = 2;
    ```

  <a name="references--disallow-var"></a><a name="2.2"></a>
  - [2.2](#references--disallow-var) 如果你一定要修改一个引用，那也不要用 `var` ，你可以用 `let` 来代替。 eslint: [`no-var`](http://eslint.org/docs/rules/no-var.html) jscs: [`disallowVar`](http://jscs.info/rule/disallowVar)

    > 为什么？ `let` 是块级作用域变量，而 `var` 是函数作用域变量，那你还不快用 `let` ？

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

    > BY [dancerphil](https://github.com/dancerphil)：有一个经典的例子

    ```javascript
    // 差评，而且失败
    var foos = []
    for(var i = 0; i < 4; i++){
      foos[i] = function() {
        console.log(i)
      }
    }
    foos[1]() // 4

    // 好评
    var foos = []
    for(let i = 0; i < 4; i++){ // 使用 let
      foos[i] = function() {
        console.log(i)
      }
    }
    foos[1]() // 1
    ```

  <a name="references--block-scope"></a><a name="2.3"></a>
  - [2.3](#references--block-scope) 提醒你一下： `let` 和 `const` 都是块级作用域

    ```javascript
    // const 和 let 只在它们定义的'代码块'里才存在
    {
      let a = 1;
      const b = 1;
    }
    console.log(a); // 出现 ReferenceError
    console.log(b); // 出现 ReferenceError
    ```

**[↑ 回到最上方](#table-of-contents)**

<a name="objects"></a>
## 对象

  <a name="objects--no-new"></a><a name="3.1"></a>
  - [3.1](#objects--no-new) 使用能让人看懂的，字面的，自解释的语法来创建对象。 eslint: [`no-new-object`](http://eslint.org/docs/rules/no-new-object.html)

    ```javascript
    // 差评
    const item = new Object();

    // 好评
    const item = {};
    ```

  <a name="objects--reserved-words"></a><a name="3.2"></a>
  - [3.2](#objects--reserved-words) 如果你的代码在浏览器环境下执行，别使用 [保留字 reserved words](http://es5.github.io/#x7.6.1) 作为键值。来个 IE8 ，它就爆炸了。 [更多信息](https://github.com/airbnb/javascript/issues/61)。而在ES6模块中使用或者在服务器端使用时，什么事都没有。 jscs: [`disallowIdentifierNames`](http://jscs.info/rule/disallowIdentifierNames)

    ```javascript
    // 差评
    const superman = {
      default: { clark: 'kent' },
      private: true,
    };

    // 好评
    const superman = {
      defaults: { clark: 'kent' },
      hidden: true,
    };
    ```

  <a name="objects--reserved-words-2"></a><a name="3.3"></a>
  - [3.3](#objects--reserved-words-2) 用一些可读性强的同义词来代替你想要使用的保留字。 jscs: [`disallowIdentifierNames`](http://jscs.info/rule/disallowIdentifierNames)

    ```javascript
    // 差评
    const superman = {
      class: 'alien',
    };

    // 差评
    const superman = {
      klass: 'alien',
    };

    // 好评
    const superman = {
      type: 'alien',
    };
    ```

  <a name="es6-computed-properties"></a><a name="3.4"></a>
  - [3.4](#es6-computed-properties) 创建有动态属性名的对象时，使用特性：可被计算属性名称

    > 你说的什么意思？这样的话，你可以在一个地方定义所有的对象属性
    > 
    > 注 BY [dancerphil](https://github.com/dancerphil)： ``` `a key named ${k}` ``` 是 ES6 模板字符串，其中可以包含 `${expression}` 占位符，[（见：MDN 模板字符串）](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/template_strings)

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

  <a name="es6-object-shorthand"></a><a name="3.5"></a>
  - [3.5](#es6-object-shorthand) 使用对象方法的简写写法。 eslint: [`object-shorthand`](http://eslint.org/docs/rules/object-shorthand.html) jscs: [`requireEnhancedObjectLiterals`](http://jscs.info/rule/requireEnhancedObjectLiterals)

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

  <a name="es6-object-concise"></a><a name="3.6"></a>
  - [3.6](#es6-object-concise) 使用对象属性值的简写写法。 eslint: [`object-shorthand`](http://eslint.org/docs/rules/object-shorthand.html) jscs: [`requireEnhancedObjectLiterals`](http://jscs.info/rule/requireEnhancedObjectLiterals)

    > 为什么？这样写更短更有力！

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

  <a name="objects--grouped-shorthand"></a><a name="3.7"></a>
  - [3.7](#objects--grouped-shorthand) 把简写的属性放在一起，然后写在对象的开头处

    > 为什么？这样能清楚地分辨哪些属性使用了简写

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

  <a name="objects-quoted-props"></a><a name="3.8"></a>
  - [3.8](#objects-quoted-props) 只有拥有不合法标识符的属性名才使用引号。 eslint: [`quote-props`](http://eslint.org/docs/rules/quote-props.html) jscs: [`disallowQuotedKeysInObjects`](http://jscs.info/rule/disallowQuotedKeysInObjects)

  > 为什么？一般的，我们认为这样更易读，这种做法可以改善 IDE 语法高亮，同时帮助很多JS引擎进行优化
  > 
  > 注 BY [dancerphil](https://github.com/dancerphil)：这一点还是存在一些争议的，[eslint](http://eslint.org/docs/rules/quote-props.htm) 中对 **属性名引用风格** (Quoting Style for Property Names)有四种不同的规则，此文采用的是 `as-needed` 规则，只在需要引用的情况下才引用
  > 
  > 而需要引用的情况有两种：
  > 
  > 1. 你使用了 ES3 环境，比如坑爹的 IE8 以下，并且你使用了 keyword 比如 `if` ，是必须引号的，这个规则在 ES5 被移除了
  > 
  > 1. 你想要使用一个非标识符 non-identifier 作为属性名，比如一个带空格的字符串 `"one two"`
  > 
  > [_eslint源码在此_](https://github.com/eslint/eslint/blob/master/lib/rules/quote-props.js)
  > 

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

**[↑ 回到最上方](#table-of-contents)**

<a name="arrays"></a>
## 数组

  <a name="arrays--literals"></a><a name="4.1"></a>
  - [4.1](#arrays--literals) 用大家都看得懂的字面语法创建数组。 eslint: [`no-array-constructor`](http://eslint.org/docs/rules/no-array-constructor.html)

    ```javascript
    // 差评
    const items = new Array();

    // 好评
    const items = [];
    ```

  <a name="arrays--push"></a><a name="4.2"></a>
  - [4.2](#arrays--push) 不要用下标方式直接在数组中加上一个项，使用 [Array#push](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/push) 来代替

    ```javascript
    const someStack = [];

    // 差评
    someStack[someStack.length] = 'abracadabra';

    // 好评
    someStack.push('abracadabra');
    ```

  <a name="es6-array-spreads"></a><a name="4.3"></a>
  - [4.3](#es6-array-spreads) 使用数组的拓展运算符 `...` 来复制数组，谁用谁知道

    ```javascript
    // 差评
    const len = items.length;
    const itemsCopy = [];
    let i;

    for (i = 0; i < len; i++) {
      itemsCopy[i] = items[i];
    }

    // 好评
    const itemsCopy = [...items];
    ```

  <a name="arrays--from"></a><a name="4.4"></a>
  - [4.4](#arrays--from) 使用 [Array.from](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/from) 把一个 **类数组对象** 转换成数组[（见：12-javascript-quirks:8）](https://github.com/justjavac/12-javascript-quirks/blob/master/cn/8-array-like-objects.md)

    ```javascript
    const foo = document.querySelectorAll('.foo');
    const nodes = Array.from(foo);
    ```

  <a name="arrays--callback-return"></a><a name="4.5"></a>
  - [4.5](#arrays--callback-return) 在数组函数的回调中，使用 return 声明。如果一个函数体只需要一句简单的声明，那么你可以省略 return 。参见 [8.2](#8.2)。 eslint: [`array-callback-return`](http://eslint.org/docs/rules/array-callback-return)

    ```javascript
    // 好评
    [1, 2, 3].map((x) => {
      const y = x + 1;
      return x * y;
    });

    // 好评
    [1, 2, 3].map(x => x + 1);

    // 差评
    const flat = {};
    [[0, 1], [2, 3], [4, 5]].reduce((memo, item, index) => {
      const flatten = memo.concat(item);
      flat[index] = flatten;
    });

    // 好评
    const flat = {};
    [[0, 1], [2, 3], [4, 5]].reduce((memo, item, index) => {
      const flatten = memo.concat(item);
      flat[index] = flatten;
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

**[↑ 回到最上方](#table-of-contents)**

<a name="destructuring"></a>
## 解构

  <a name="destructuring--object"></a><a name="5.1"></a>
  - [5.1](#destructuring--object) 使用对象解构(object destructuring)访问和使用多属性对象。 jscs: [`requireObjectDestructuring`](http://jscs.info/rule/requireObjectDestructuring)

    > 为什么？解构把你从创建临时引用的地狱中拯救了出来，感谢

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

    // 好评如潮
    function getFullName({ firstName, lastName }) {
      return `${firstName} ${lastName}`;
    }
    ```

  <a name="destructuring--array"></a><a name="5.2"></a>
  - [5.2](#destructuring--array) 解构数组。 jscs: [`requireArrayDestructuring`](http://jscs.info/rule/requireArrayDestructuring)

    ```javascript
    const arr = [1, 2, 3, 4];

    // 差评
    const first = arr[0];
    const second = arr[1];

    // 好评
    const [first, second] = arr;
    ```

  <a name="destructuring--object-over-array"></a><a name="5.3"></a>
  - [5.3](#destructuring--object-over-array) 要返回多个值时，使用对象解构，而不是数组解构。 jscs: [`disallowArrayDestructuringReturn`](http://jscs.info/rule/disallowArrayDestructuringReturn)

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


**[↑ 回到最上方](#table-of-contents)**

<a name="strings"></a>
## 字符串

  <a name="strings--quotes"></a><a name="6.1"></a>
  - [6.1](#strings--quotes) 字符串使用单引号 `''` 。 eslint: [`quotes`](http://eslint.org/docs/rules/quotes.html) jscs: [`validateQuoteMarks`](http://jscs.info/rule/validateQuoteMarks)

    ```javascript
    // 差评
    const name = "Capt. Janeway";

    // 好评
    const name = 'Capt. Janeway';
    ```

  <a name="strings--line-length"></a><a name="6.2"></a>
  - [6.2](#strings--line-length) 字符串超过 100 个字节时，应当使用字符串连接号换行

  <a name="strings--concat-perf"></a><a name="6.3"></a>
  - [6.3](#strings--concat-perf) **注意**：过度使用字串连接符号可能会对性能造成影响。 [jsPerf](http://jsperf.com/ya-string-concat) 和 [相关讨论](https://github.com/airbnb/javascript/issues/40).

    ```javascript
    // 差评
    const errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.';

    // 差评
    const errorMessage = 'This is a super long error that was thrown because \
    of Batman. When you stop to think about how Batman had anything to do \
    with this, you would get nowhere \
    fast.';

    // 好评
    const errorMessage = 'This is a super long error that was thrown because ' +
      'of Batman. When you stop to think about how Batman had anything to do ' +
      'with this, you would get nowhere fast.';
    ```

  <a name="es6-template-literals"></a><a name="6.4"></a>
  - [6.4](#es6-template-literals) 用程序生成字符串时，使用模板字符串代替字符串连接。 eslint: [`prefer-template`](http://eslint.org/docs/rules/prefer-template.html) [`template-curly-spacing`](http://eslint.org/docs/rules/template-curly-spacing) jscs: [`requireTemplateStrings`](http://jscs.info/rule/requireTemplateStrings)

    > 为什么呢？模板字符串更为简洁，更具可读性，其恰当的换行和字符串插值非常好用

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

  <a name="strings--eval"></a><a name="6.5"></a>
  - [6.5](#strings--eval) 永远不要在字符串上使用 `eval()` ，这会造成很多漏洞

  <a name="strings--escaping"></a>
  - [6.6](#strings--escaping) 不要在字符串里使用无谓的转义符号。 eslint: [`no-useless-escape`](http://eslint.org/docs/rules/no-useless-escape)

    > 为什么？你看得清吗？所以只有必要的时候才转义

    ```javascript
    // 差评
    const foo = '\'this\' \i\s \"quoted\"';

    // 好评
    const foo = '\'this\' is "quoted"';
    const foo = `'this' is "quoted"`;
    ```

**[↑ 回到最上方](#table-of-contents)**


<a name="functions"></a>
## 函数

  <a name="functions--declarations"></a><a name="7.1"></a>
  - [7.1](#functions--declarations) 使用函数声明，不要使用函数表达式。 jscs: [`requireFunctionDeclarations`](http://jscs.info/rule/requireFunctionDeclarations)

    > 为什么？函数声明是可命名的，他们在调用栈中更容易被识别。另外，函数声明会把整个函数提升（hoisted），而函数表达式只会把函数的引用变量名提升。所以[箭头函数](#arrow-functions)可以完全取代函数表达式

    ```javascript
    // 差评
    const foo = function () {
    };

    // 好评
    function foo() {
    }
    ```

  <a name="functions--iife"></a><a name="7.2"></a>
  - [7.2](#functions--iife) 在立即调用的函数表达式的两侧用括号包裹。 eslint: [`wrap-iife`](http://eslint.org/docs/rules/wrap-iife.html) jscs: [`requireParenthesesAroundIIFE`](http://jscs.info/rule/requireParenthesesAroundIIFE)

    > 为什么？立即调用的函数表达式(immediately invoked function expression)是一个单独的单元，你需要强调这一点，所以把表达式和它的调用括号包裹起来。还需要提醒一下的是：在写模块的时候，你永远不会需要一个 IIFE

    ```javascript
    // 立即调用的函数表达式 (IIFE)
    (function () {
      console.log('Welcome to the Internet. Please follow me.');
    }());
    ```

  <a name="functions--in-blocks"></a><a name="7.3"></a>
  - [7.3](#functions--in-blocks) 永远不要再一个非函数代码块（`if`, `while` 等等）定义一个函数。请把你想要的那个函数赋给一个变量。浏览器会允许你这么做，但是它们的解析表现不一致。 eslint: [`no-loop-func`](http://eslint.org/docs/rules/no-loop-func.html)

  <a name="functions--note-on-blocks"></a><a name="7.4"></a>
  - [7.4](#functions--note-on-blocks) **注意：** ECMA-262 把 `block` 定义为一组语句。而函数声明并不是语句。 [阅读 ECMA-262 相关说明](http://www.ecma-international.org/publications/files/ECMA-ST/Ecma-262.pdf#page=97)

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

  <a name="functions--arguments-shadow"></a><a name="7.5"></a>
  - [7.5](#functions--arguments-shadow) 永远不要把参数命名为 `arguments` 。这将取代原来函数作用域内的 `arguments` 对象

    ```javascript
    // 差评
    function nope(name, options, arguments) {
      // ...一些代码...
    }

    // 好评
    function yup(name, options, args) {
      // ...一些代码...
    }
    ```

  <a name="es6-rest"></a><a name="7.6"></a>
  - [7.6](#es6-rest) 与此同时，不要使用 `arguments` 。可以选择 rest 语法 `...` 替代。 eslint: [`prefer-rest-params`](http://eslint.org/docs/rules/prefer-rest-params)

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

  <a name="es6-default-parameters"></a><a name="7.7"></a>
  - [7.7](#es6-default-parameters) 使用预设参数的语法，而不是改变函数参数

    ```javascript
    // 实在差评
    function handleThings(opts) {
      // 不！我们不该改变函数参数
      // 错上加错！如果 opts 被计算为 false ，它会成为一个对象
      // 或许你想这么做，但是这样会引起 bug
      opts = opts || {};
      // ...
    }

    // 依旧差评
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

  <a name="functions--default-side-effects"></a><a name="7.8"></a>
  - [7.8](#functions--default-side-effects) 避免预设参数的语法的副作用

    > 为什么？这样做完之后，代码变得没有逻辑

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

  <a name="functions--defaults-last"></a><a name="7.9"></a>
  - [7.9](#functions--defaults-last) 总是把预设参数写在参数的最后

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

  <a name="functions--constructor"></a><a name="7.10"></a>
  - [7.10](#functions--constructor) 永远不要使用函数构造器来构造一个新函数

    > 为什么？通过这种方式来构造函数与 `eval()` 相类似。会造成很多漏洞

    ```javascript
    // 差评
    var add = new Function('a', 'b', 'return a + b');

    // 依旧差评
    var subtract = Function('a', 'b', 'return a - b');
    ```

  <a name="functions--signature-spacing"></a><a name="7.11"></a>
  - [7.11](#functions--signature-spacing) 在函数的 signature 后放置空格

    > 为什么？一致性是必须考虑的。在你新增和删除名称时，你不需要改变空格

    ```javascript
    // 差评
    const f = function(){};
    const g = function (){};
    const h = function() {};

    // 好评
    const x = function () {};
    const y = function a() {};
    ```

  <a name="functions--mutate-params"></a><a name="7.12"></a>
  - [7.12](#functions--mutate-params) 不要改变对象的参数。 eslint: [`no-param-reassign`](http://eslint.org/docs/rules/no-param-reassign.html)

    > 为什么？将传入对象的参数重新赋值，可能会引起一些不期望的副作用

    ```javascript
    // 差评
    function f1(obj) {
      obj.key = 1;
    };

    // 好评
    function f2(obj) {
      const key = Object.prototype.hasOwnProperty.call(obj, 'key') ? obj.key : 1;
    };
    ```

  <a name="functions--reassign-params"></a><a name="7.13"></a>
  - [7.13](#functions--reassign-params) 永远不要对参数重新赋值。 eslint: [`no-param-reassign`](http://eslint.org/docs/rules/no-param-reassign.html)

    > 为什么？对参数重新赋值会引起意外的行为，尤其是访问 `arguments` 对象。在 V8 引擎里，会导致优化问题

    ```javascript
    // 差评
    function f1(a) {
      a = 1;
    }

    function f2(a) {
      if (!a) { a = 1; }
    }

    // 好评
    function f3(a) {
      const b = a || 1;
    }

    function f4(a = 1) {
    }
    ```

**[↑ 回到最上方](#table-of-contents)**

<a name="arrow-functions"></a>
## 箭头函数

  <a name="arrows--use-them"></a><a name="8.1"></a>
  - [8.1](#arrows--use-them) 当你必须使用函数表达式（或传递一个匿名函数）时，使用箭头函数符号。 eslint: [`prefer-arrow-callback`](http://eslint.org/docs/rules/prefer-arrow-callback.html), [`arrow-spacing`](http://eslint.org/docs/rules/arrow-spacing.html) jscs: [`requireArrowFunctions`](http://jscs.info/rule/requireArrowFunctions)

    > 为什么？箭头函数创造了一个新的 `this` 上下文，通常符合你的期望，而且箭头函数非常简洁。（参考 BY [yuche](https://github.com/yuche)： [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) 和 [toddmotto](http://toddmotto.com/es6-arrow-functions-syntaxes-and-lexical-scoping/)）
    > 
    > 如果不，又是为什么？如果你有一个相当复杂的函数，你或许想要把逻辑部分转移到一个函数声明上

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

  <a name="arrows--implicit-return"></a><a name="8.2"></a>
  - [8.2](#arrows--implicit-return) 如果一个函数体只有很简单的一行，那就把花括号、圆括号和 `return` 都省略掉。如果不是，那就保留括号和 `return` 。 eslint: [`arrow-parens`](http://eslint.org/docs/rules/arrow-parens.html), [`arrow-body-style`](http://eslint.org/docs/rules/arrow-body-style.html) jscs:  [`disallowParenthesesAroundArrowParam`](http://jscs.info/rule/disallowParenthesesAroundArrowParam), [`requireShorthandArrowFunctions`](http://jscs.info/rule/requireShorthandArrowFunctions)

    > 为什么？通过语法修饰，在函数的链式调用中可读性很高

    ```javascript
    // 差评
    [1, 2, 3].map(number => {
      const nextNumber = number + 1;
      `A string containing the ${nextNumber}.`;
    });

    // 好评
    [1, 2, 3].map(number => `A string containing the ${number}.`);

    // 好评
    [1, 2, 3].map((number) => {
      const nextNumber = number + 1;
      return `A string containing the ${nextNumber}.`;
    });

    // 好评
    [1, 2, 3].map((number, index) => ({
      index: number
    }));
    ```

  <a name="arrows--paren-wrap"></a><a name="8.3"></a>
  - [8.3](#arrows--paren-wrap) 如果你们表达式跨行了，请使用括号把它们包裹起来，增加可读性

    > 为什么？这样做是的表达式的开始和结束的位置更加清楚

    ```js
    // 差评
    [1, 2, 3].map(number => 'As time went by, the string containing the ' +
      `${number} became much longer. So we needed to break it over multiple ` +
      'lines.'
    );

    // 好评
    [1, 2, 3].map(number => (
      `As time went by, the string containing the ${number} became much ` +
      'longer. So we needed to break it over multiple lines.'
    ));
    ```

  <a name="arrows--one-arg-parens"></a><a name="8.4"></a>
  - [8.4](#arrows--one-arg-parens) 如果你的函数只有一个参数，而且不包含大括号，那么省略括号吧，否则，你应当使用括号包住参数。 eslint: [`arrow-parens`](http://eslint.org/docs/rules/arrow-parens.html) jscs:  [`disallowParenthesesAroundArrowParam`](http://jscs.info/rule/disallowParenthesesAroundArrowParam)

    > 为什么？减少视觉噪声

    ```js
    // 差评
    [1, 2, 3].map((x) => x * x);

    // 好评
    [1, 2, 3].map(x => x * x);

    // 好评
    [1, 2, 3].map(number => (
      `A long string with the ${number}. It’s so long that we’ve broken it ` +
      'over multiple lines!'
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

  <a name="arrows--confusing"></a><a name="8.5"></a>
  - [8.5](#arrows--confusing) 避免把箭头函数语法 (`=>`) 和比较运算符 (`<=`, `>=`) 相混淆。不是你自己看得清就可以的，而是你写的时候要让别人看清。 eslint: [`no-confusing-arrow`](http://eslint.org/docs/rules/no-confusing-arrow)

    ```js
    // 差评
    const itemHeight = item => item.height > 256 ? item.largeSize : item.smallSize;

    // 差评
    const itemHeight = (item) => item.height > 256 ? item.largeSize : item.smallSize;

    // 好评
    const itemHeight = (item) => { return item.height > 256 ? item.largeSize : item.smallSize; };
    ```

**[↑ 回到最上方](#table-of-contents)**


<a name="classes--constructors"></a>
## 类，构造函数

  <a name="constructors--use-class"></a><a name="9.1"></a>
  - [9.1](#constructors--use-class) 总是使用 `class`。避免直接操作 `prototype` ，会 `prototype` 不一定要用，对吧

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

  <a name="constructors--extends"></a><a name="9.2"></a>
  - [9.2](#constructors--extends) 使用 `extends` 来继承

    > 为什么？ `extends` 是一个内建的原型继承方法，它不会破坏 `instanceof` 

    ```javascript
    // 差评
    const inherits = require('inherits');
    function PeekableQueue(contents) {
      Queue.apply(this, contents);
    }
    inherits(PeekableQueue, Queue);
    PeekableQueue.prototype.peek = function () {
      return this._queue[0];
    }

    // 好评
    class PeekableQueue extends Queue {
      peek() {
        return this._queue[0];
      }
    }
    ```

  <a name="constructors--chaining"></a><a name="9.3"></a>
  - [9.3](#constructors--chaining) 方法返回 `this` 可以帮助链式调用，这真的棒

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


  <a name="constructors--tostring"></a><a name="9.4"></a>
  - [9.4](#constructors--tostring) 你想写一个自定义的 `toString()` 方法？没问题，但要确保它能正常运行并且不会引起副作用

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

  <a name="constructors--no-useless"></a><a name="9.5"></a>
  - [9.5](#constructors--no-useless) 如果没有指定，类会拥有默认的构造方法，你不需要写一个空的构造方法，或者只是继承父类的方法，多余。 eslint: [`no-useless-constructor`](http://eslint.org/docs/rules/no-useless-constructor)

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

  <a name="classes--no-duplicate-members"></a>
  - [9.6](#classes--no-duplicate-members) 避免重复的类成员。 eslint: [`no-dupe-class-members`](http://eslint.org/docs/rules/no-dupe-class-members)

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


**[↑ 回到最上方](#table-of-contents)**


<a name="modules"></a>
## 模块

  <a name="modules--use-them"></a><a name="10.1"></a>
  - [10.1](#modules--use-them) 总是使用模组 (`import`/`export`) 而不是其他非标准模块系统。你可以编译为你喜欢的模块系统。参考：[@颜海镜](http://yanhaijing.com/javascript/2015/03/28/js-module/) ，[@segmentgault](https://segmentfault.com/a/1190000000492678)

    > 为什么？模块是未来，让我们开始使用“未来”吧！

    ```javascript
    // 差评
    const AirbnbStyleGuide = require('./AirbnbStyleGuide');
    module.exports = AirbnbStyleGuide.es6;

    // 还可以
    import AirbnbStyleGuide from './AirbnbStyleGuide';
    export default AirbnbStyleGuide.es6;

    // 好评如潮
    import { es6 } from './AirbnbStyleGuide';
    export default es6;
    ```

  <a name="modules--no-wildcard"></a><a name="10.2"></a>
  - [10.2](#modules--no-wildcard) 不要使用通配符 `*` 引入

    > 为什么？这样能确保你只有一个默认的 `export` 

    ```javascript
    // 差评
    import * as AirbnbStyleGuide from './AirbnbStyleGuide';

    // 好评
    import AirbnbStyleGuide from './AirbnbStyleGuide';
    ```

  <a name="modules--no-export-from-import"></a><a name="10.3"></a>
  - [10.3](#modules--no-export-from-import) 不要从 `import` 中直接 `export` 

    > 为什么？虽然写在一行看起来很棒，但是它们毕竟一个是引入，一个是导出，分开写会让所有事都井井有条

    ```javascript
    // 差评
    // filename es6.js
    export { es6 as default } from './airbnbStyleGuide';

    // 好评
    // filename es6.js
    import { es6 } from './AirbnbStyleGuide';
    export default es6;
    ```

  <a name="modules--no-duplicate-imports"></a>
  - [10.4](#modules--no-duplicate-imports) 来自相同路径的 `import` ，就把他们写在同一个地方。 eslint: [`no-duplicate-imports`](http://eslint.org/docs/rules/no-duplicate-imports)

    > 为什么？写在不同行的话，程序的维护和更新变得非常困难

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

  <a name="modules--no-mutable-exports"></a>
  - [10.5](#modules--no-mutable-exports) 不要 `export` 可变的绑定。
 eslint: [`import/no-mutable-exports`](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/no-mutable-exports.md)
    > 为什么？在特定的情况下也许会导出可变绑定，一般来说我们都要避免异常(mutation)的发生，只使用常数引用。

    ```javascript
    // 差评
    let foo = 3;
    export { foo }

    // 好评
    const foo = 3;
    export { foo }
    ```

**[↑ 回到最上方](#table-of-contents)**

<a name="iterators-and-generators"></a>
## 迭代器，生成器

  <a name="iterators--nope"></a><a name="11.1"></a>
  - [11.1](#iterators--nope) 不要使用迭代器(iterators)。选择 JS 高阶函数比如 `map()` 和 `reduce()` ，不要使用循环，比如 `for-of` 。 eslint: [`no-iterator`](http://eslint.org/docs/rules/no-iterator.html)

    > 为什么？这增加了代码的不变性，处理纯函数的返回值非常方便，超过了其可以造成的副作用

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
    numbers.forEach(num => sum += num);
    sum === 15;

    // 好评如潮 (use the functional force)
    const sum = numbers.reduce((total, num) => total + num, 0);
    sum === 15;
    ```

  <a name="generators--nope"></a><a name="11.2"></a>
  - [11.2](#generators--nope) 暂时不要使用生成器(generators)

    > 为什么？他们不能很好的被编译到 ES5 
    > 
    > 注 BY [dancerphil](https://github.com/dancerphil)：不过截至目前，V8 和 Nodejs 已经很好的支持了生成器

**[↑ 回到最上方](#table-of-contents)**


<a name="properties"></a>
## 属性

  <a name="properties--dot"></a><a name="12.1"></a>
  - [12.1](#properties--dot) 使用 `.` 符号来访问对象属性。 eslint: [`dot-notation`](http://eslint.org/docs/rules/dot-notation.html) jscs: [`requireDotNotation`](http://jscs.info/rule/requireDotNotation)

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

  <a name="properties--bracket"></a><a name="12.2"></a>
  - [12.2](#properties--bracket) 如果要使用一个变量来访问属性，那么使用 `[]` 

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

**[↑ 回到最上方](#table-of-contents)**


<a name="variables"></a>
## 变量

  <a name="variables--const"></a><a name="13.1"></a>
  - [13.1](#variables--const) 你应当使用 `const` 来声明变量。不这么做会导致全局变量。我们想要尽量避免污染全局命名空间。地球队长(Captain Planet)已经警告过我们了。（解释 BY [dancerphil](https://github.com/dancerphil)：Captain Planet 保卫着 global namespace，好冷）

    ```javascript
    // 差评
    superPower = new SuperPower();

    // 好评
    const superPower = new SuperPower();
    ```

  <a name="variables--one-const"></a><a name="13.2"></a>
  - [13.2](#variables--one-const) 每个变量都对应一个 `const` 。 eslint: [`one-var`](http://eslint.org/docs/rules/one-var.html) jscs: [`disallowMultipleVarDecl`](http://jscs.info/rule/disallowMultipleVarDecl)

    > 为什么？增加新的变量会更容易，而且你永远不用再担心把 `,` 和 `;` 换来换去的事情，你肯定觉得 git diff 里包含太多标点符号的变换是很蠢的事情。而且，调试时你可以步进，而不是一次性跳过了所有的声明
    > 
    > BY [dancerphil](https://github.com/dancerphil)：不写分号星人有另一种可供参考的的写法

    ```javascript
    // 差评
    const items = getItems(),
        goSportsTeam = true,
        dragonball = 'z';

    // 差评（与上面比较一下，再说说哪里写错了）
    const items = getItems(),
        goSportsTeam = true;
        dragonball = 'z';

    // 好评
    const items = getItems();
    const goSportsTeam = true;
    const dragonball = 'z';
    
    // BY dancerphil
    const items = getItems()
      , goSportsTeam = true
      , dragonball = 'z'
    ```

  <a name="variables--const-let-group"></a><a name="13.3"></a>
  - [13.3](#variables--const-let-group) 把你所有的 `const` 放在一起，再把所有的 `let` 放在一起

    > 为什么？等到你想要把已赋值的变量赋给一个新变量的时候，这很有帮助

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

  <a name="variables--define-where-used"></a><a name="13.4"></a>
  - [13.4](#variables--define-where-used) 在你需要的地方赋值变量，把他们放到合理的位置

    > 为什么？ `let` 和 `const` 是块级作用域的，不是函数级作用域的

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

**[↑ 回到最上方](#table-of-contents)**


<a name="hoisting"></a>
## 提升

  <a name="hoisting--about"></a><a name="14.1"></a>
  - [14.1](#hoisting--about) `var` 声明会被提升至该作用域的顶部，但它们赋值不会提升。 `let` 和 `const` 被赋予了一种新概念，称为暂时性死区[(Temporal Dead Zones)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let#Temporal_dead_zone_and_errors_with_let)。这对于了解为什么 [typeof 不再安全](http://es-discourse.com/t/why-typeof-is-no-longer-safe/15)相当重要

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

  <a name="hoisting--anon-expressions"></a><a name="14.2"></a>
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

  <a name="hoisting--named-expresions"></a><a name="14.3"></a>
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
      }
    }
    ```

  <a name="hoisting--declarations"></a><a name="14.4"></a>
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
  > BY [dancerphil](https://github.com/dancerphil)：在我看来可以这样总结，没有特殊需要的时候，把定义放在使用之前就可以了

**[↑ 回到最上方](#table-of-contents)**


<a name="comparison-operators--equality"></a>
## 比较运算符，等号

  <a name="comparison--eqeqeq"></a><a name="15.1"></a>
  - [15.1](#comparison--eqeqeq) 尽量使用 `===` 和 `!==` ，而不是 `==` 和 `!=` 。 eslint: [`eqeqeq`](http://eslint.org/docs/rules/eqeqeq.html)

  <a name="comparison--if"></a><a name="15.2"></a>
  - [15.2](#comparison--if) Conditional statements such as the `if` statement evaluate their expression using coercion with the `ToBoolean` abstract method and always follow these simple rules:

    + **Objects** 被视为 **true**
    + **Undefined** 被视为 **false**
    + **Null** 被视为 **false**
    + **Booleans** 被视为 **boolean的值**
    + 如果 **Numbers** 是 **+0, -0, or NaN** ，将被视为 **false** ，否则被视为 **true**
    + 如果 **Strings** 是一个空字符串 `''` ，将被视为 **false** ，否则被视为 **true**

    ```javascript
    if ([0] && []) {
      // true
      // 数组（即使它是空的）是一个对象，而对象被视为 true
    }
    ```

  <a name="comparison--shortcuts"></a><a name="15.3"></a>
  - [15.3](#comparison--shortcuts) 使用简写(shortcuts)

    ```javascript
    // 差评
    if (name !== '') {
      // ...一些代码...
    }

    // 好评
    if (name) {
      // ...一些代码...
    }

    // 差评
    if (collection.length > 0) {
      // ...一些代码...
    }

    // 好评
    if (collection.length) {
      // ...一些代码...
    }
    ```

  <a name="comparison--moreinfo"></a><a name="15.4"></a>
  - [15.4](#comparison--moreinfo) 想了解更多信息，可以参考 Angus Croll 的 [Truth Equality and JavaScript](http://javascriptweblog.wordpress.com/2011/02/07/truth-equality-and-javascript/#more-2108)（你点进去后会发现你的思维并没有你想像的那么清晰）

    ```javascript
    // BY dancerphil
    console.log([] == true); // false
    console.log(![] == true); // false
    console.log(!![] == true); // true
    if([]) {
      console.log('why?'); // why?
    }
    ```
    
  <a name="comparison--switch-blocks"></a><a name="15.5"></a>
  - [15.5](#comparison--switch-blocks) 如果在 `case` 和 `default` 中包含了宣告语法(lexical declarations)，比如 `let`, `const`, `function`, 和 `class`。那么用大括号来建立代码块
  
  eslint rules: [`no-case-declarations`](http://eslint.org/docs/rules/no-case-declarations.html).

  > 为什么？宣告语法在整个 `switch` 块都可见，但是只在我们定义的地方才得到了初始值，也就是当进入其所在的 `case` 时才触发。如果不加大括号，可能引起多个 `case` 尝试定义同一事物的情况


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
        function f() {}
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
        function f() {}
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

  <a name="comparison--nested-ternaries"></a><a name="15.6"></a>
  - [15.6](#comparison--nested-ternaries) 三元运算符通常应该是简单的单行表达式，而不是嵌套的(nested)
   
  eslint rules: [`no-nested-ternary`](http://eslint.org/docs/rules/no-nested-ternary.html).

    ```javascript
    // 差评
    const foo = maybe1 > maybe2
      ? "bar"
      : value1 > value2 ? "baz" : null;

    // better
    const maybeNull = value1 > value2 ? 'baz' : null;

    const foo = maybe1 > maybe2
      ? 'bar'
      : maybeNull;

    // 好评如潮
    const maybeNull = value1 > value2 ? 'baz' : null;

    const foo = maybe1 > maybe2 ? 'bar' : maybeNull;
    ```

  <a name="comparison--unneeded-ternary"></a><a name="15.7"></a>
  - [15.7](#comparison--unneeded-ternary) 避免不必要的三元运算符

  eslint rules: [`no-unneeded-ternary`](http://eslint.org/docs/rules/no-unneeded-ternary.html).

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

**[↑ 回到最上方](#table-of-contents)**


<a name="blocks"></a>
## 块

  <a name="blocks--braces"></a><a name="16.1"></a>
  - [16.1](#blocks--braces) 用大括号包裹所有的多行代码块

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

  <a name="blocks--cuddled-elses"></a><a name="16.2"></a>
  - [16.2](#blocks--cuddled-elses) 如果你在使用带 `if` 和 `else` 的多行代码块，把 `else` 放在 `if` 代码块关闭括号的同一行。 eslint: [`brace-style`](http://eslint.org/docs/rules/brace-style.html) jscs:  [`disallowNewlineBeforeBlockStatements`](http://jscs.info/rule/disallowNewlineBeforeBlockStatements)

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


**[↑ 回到最上方](#table-of-contents)**


<a name="comments"></a>
## 注释

  <a name="comments--multiline"></a><a name="17.1"></a>
  - [17.1](#comments--multiline) 使用 `/** ... */` 作为多行注释。包含描述，并指定参数和返回值的类型和值

    ```javascript
    // 差评
    // make() returns a new element
    // based on the passed in tag name
    //
    // @param {String} tag
    // @return {Element} element
    function make(tag) {

      // ...一些代码...

      return element;
    }

    // 好评
    /**
     * make() returns a new element
     * based on the passed in tag name
     *
     * @param {String} tag
     * @return {Element} element
     */
    function make(tag) {

      // ...一些代码...

      return element;
    }
    ```

  <a name="comments--singleline"></a><a name="17.2"></a>
  - [17.2](#comments--singleline) 使用 `//` 作为单行注释。在需要注释的代码上方另起一行写注释。在注释前空一行，除非注释在块的开头

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
      const type = this._type || 'no type';

      return type;
    }

    // 好评
    function getType() {
      console.log('fetching type...');

      // set the default type to 'no type'
      const type = this._type || 'no type';

      return type;
    }

    // 同样好评
    function getType() {
      // set the default type to 'no type'
      const type = this._type || 'no type';

      return type;
    }
    ```

  <a name="comments--actionitems"></a><a name="17.3"></a>
  - [17.3](#comments--actionitems) 在注释前加上 `FIXME` 或 `TODO` 可以帮助其他开发者快速的了解写这段注释的原因。也许你正在指出一个需要复查的问题，或者你正在给需要实现的功能提供一个解决方式。和一般的注解不用，它们是可被执行的，对应的动作为 `FIXME: -- 需要搞清楚这一点` 或者 `TODO: -- 需要实现`

  <a name="comments--fixme"></a><a name="17.4"></a>
  - [17.4](#comments--fixme) 使用 `// FIXME:` 标注问题

    ```javascript
    class Calculator extends Abacus {
      constructor() {
        super();

        // FIXME: 这里不应该存在全局变量
        total = 0;
      }
    }
    ```

  <a name="comments--todo"></a><a name="17.5"></a>
  - [17.5](#comments--todo) 使用 `// TODO:` 标注问题的解决方式

    ```javascript
    class Calculator extends Abacus {
      constructor() {
        super();

        // TODO: total 应该是个可被传入的可选变量
        this.total = 0;
      }
    }
    ```

**[↑ 回到最上方](#table-of-contents)**


<a name="whitespace"></a>
## 空格

  <a name="whitespace--spaces"></a><a name="18.1"></a>
  - [18.1](#whitespace--spaces) ~~使用两个空格作为缩进~~。 eslint: [`indent`](http://eslint.org/docs/rules/indent.html) jscs: [`validateIndentation`](http://jscs.info/rule/validateIndentation)

  > BY [dancerphil](https://github.com/dancerphil)：不！！！使用 Tab 作为缩进，并在 IDE 设置 Tab 为 4 空格宽，如果你选择此文作为某个组织的代码风格文档，则你应当认同此点
  > 
  > 选择两个空格作为缩进的理由是：JS 是一个充满了回调函数的语言，如果使用过长的缩进，会使一部分深处的代码左侧悬空，对此，我建议使用一个大的显示屏，并调整 IDE 的字体大小
  > 
  > 你知道我很刻薄的，4 空格宽意味着缩进是一个视觉上的长方形，我总是在这些“美学”的问题上计较，可你不也是如此吗？
  > 
  > 作为译者，坚持与原文的不同的地方有两处，这里是第一处

    ```javascript
    // 差评
    function foo() {
    ∙∙∙∙const name;
    }

    // 差评
    function bar() {
    ∙const name;
    }

    // 好评
    function baz() {
    ∙∙const name;
    }
    ```

  <a name="whitespace--before-blocks"></a><a name="18.2"></a>
  - [18.2](#whitespace--before-blocks) 在起始的大括号 `{` 的左侧放上一个空格。 eslint: [`space-before-blocks`](http://eslint.org/docs/rules/space-before-blocks.html) jscs: [`requireSpaceBeforeBlockStatements`](http://jscs.info/rule/requireSpaceBeforeBlockStatements)

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

  <a name="whitespace--around-keywords"></a><a name="18.3"></a>
  - [18.3](#whitespace--around-keywords) 在控制语句 `if`, `while` 的括号前放一个空格。在函数调用及声明中，括号前不加空格。 eslint: [`keyword-spacing`](http://eslint.org/docs/rules/keyword-spacing.html) jscs: [`requireSpaceAfterKeywords`](http://jscs.info/rule/requireSpaceAfterKeywords)

  >  `if` 的行为与函数不同，我承认应该要加，虽然这带来了一些成本
  
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

  <a name="whitespace--infix-ops"></a><a name="18.4"></a>
  - [18.4](#whitespace--infix-ops) 使用空格隔开运算符。 eslint: [`space-infix-ops`](http://eslint.org/docs/rules/space-infix-ops.html) jscs: [`requireSpaceBeforeBinaryOperators`](http://jscs.info/rule/requireSpaceBeforeBinaryOperators), [`requireSpaceAfterBinaryOperators`](http://jscs.info/rule/requireSpaceAfterBinaryOperators)

  >  我认为这一条只针对二元及以上运算符，对于一元运算符，请不要用空格分开它们，比如 `let dog = isHuman && !hasGirl()`
  >  
  >  另：原文没有提到逗号和分号的空格风格，不过注意永远不要在逗号 `,` 分号 `;` 的左侧加空格

    ```javascript
    // 差评
    const x=y+5;

    // 好评
    const x = y + 5;
    ```

  <a name="whitespace--newline-at-end"></a><a name="18.5"></a>
  - [18.5](#whitespace--newline-at-end) 在文件末尾插入一个空行

    ```javascript
    // 差评
    (function (global) {
      // ...一些代码...
    })(this);
    ```

    ```javascript
    // 差评
    (function (global) {
      // ...一些代码...
    })(this);↵
    ↵
    ```

    ```javascript
    // 好评
    (function (global) {
      // ...一些代码...
    })(this);↵
    ```

  <a name="whitespace--chains"></a><a name="18.6"></a>
  - [18.6](#whitespace--chains) 链式调用时（超过两个方法的链），使用符合调用逻辑的缩进，并且在方法的左侧放置点 `.` ，别给我放到后面，你需要强调这是方法调用而不是新语句。 eslint: [`newline-per-chained-call`](http://eslint.org/docs/rules/newline-per-chained-call) [`no-whitespace-before-property`](http://eslint.org/docs/rules/no-whitespace-before-property)

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
        .attr('transform', 'translate(' + (radius + margin) + ',' + (radius + margin) + ')')
        .call(tron.led);

    // 好评
    const leds = stage.selectAll('.led')
        .data(data)
      .enter().append('svg:svg')
        .classed('led', true)
        .attr('width', (radius + margin) * 2)
      .append('svg:g')
        .attr('transform', 'translate(' + (radius + margin) + ',' + (radius + margin) + ')')
        .call(tron.led);

    // 好评
    const leds = stage.selectAll('.led').data(data);
    ```

  <a name="whitespace--after-blocks"></a><a name="18.7"></a>
  - [18.7](#whitespace--after-blocks) 在块结束后、新语句开始前，插入一个空行。 jscs: [`requirePaddingNewLinesAfterBlocks`](http://jscs.info/rule/requirePaddingNewLinesAfterBlocks)

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

  <a name="whitespace--padded-blocks"></a><a name="18.8"></a>
  - [18.8](#whitespace--padded-blocks) 不要使用空白行来“装饰”你的代码块。 eslint: [`padded-blocks`](http://eslint.org/docs/rules/padded-blocks.html) jscs:  [`disallowPaddingNewlinesInBlocks`](http://jscs.info/rule/disallowPaddingNewlinesInBlocks)

    ```javascript
    // 差评
    function bar() {

      console.log(foo);

    }

    // also bad
    if (baz) {

      console.log(qux);
    } else {
      console.log(foo);

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

  <a name="whitespace--in-parens"></a><a name="18.9"></a>
  - [18.9](#whitespace--in-parens) 不要在括号里加空格，当然，逗号后面有空格。 eslint: [`space-in-parens`](http://eslint.org/docs/rules/space-in-parens.html) jscs: [`disallowSpacesInsideParentheses`](http://jscs.info/rule/disallowSpacesInsideParentheses)

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

  <a name="whitespace--in-brackets"></a><a name="18.10"></a>
  - [18.10](#whitespace--in-brackets) 不要在方括号内加空格。 eslint: [`array-bracket-spacing`](http://eslint.org/docs/rules/array-bracket-spacing.html) jscs: [`disallowSpacesInsideArrayBrackets`](http://jscs.info/rule/disallowSpacesInsideArrayBrackets)

    ```javascript
    // 差评
    const foo = [ 1, 2, 3 ];
    console.log(foo[ 0 ]);

    // 好评
    const foo = [1, 2, 3];
    console.log(foo[0]);
    ```

  <a name="whitespace--in-braces"></a><a name="18.11"></a>
  - [18.11](#whitespace--in-braces) 不要在花括号内加空格。 eslint: [`object-curly-spacing`](http://eslint.org/docs/rules/object-curly-spacing.html) jscs: [`disallowSpacesInsideObjectBrackets`](http://jscs.info/rule/disallowSpacesInsideObjectBrackets)

    ```javascript
    // 差评
    const foo = {clark: 'kent'};

    // 好评
    const foo = { clark: 'kent' };
    ```

  <a name="whitespace--max-len"></a><a name="18.12"></a>
  - [18.12](#whitespace--max-len) 避免你的某一行代码超过了 100 字符，当然空格也算在里面。 eslint: [`max-len`](http://eslint.org/docs/rules/max-len.html) jscs: [`maximumLineLength`](http://jscs.info/rule/maximumLineLength)

    > 为毛？这种做法保证了可读性和可维护性

    ```javascript
    // 差评
    const foo = 'Whatever national crop flips the window. The cartoon reverts within the screw. Whatever wizard constrains a helpful ally. The counterpart ascends!';

    // 差评
    $.ajax({ method: 'POST', url: 'https://airbnb.com/', data: { name: 'John' } }).done(() => console.log('Congratulations!')).fail(() => console.log('You have failed this city.'));

    // 好评
    const foo = 'Whatever national crop flips the window. The cartoon reverts within the screw. ' +
      'Whatever wizard constrains a helpful ally. The counterpart ascends!';

    // 好评
    $.ajax({
      method: 'POST',
      url: 'https://airbnb.com/',
      data: { name: 'John' },
    })
      .done(() => console.log('Congratulations!'))
      .fail(() => console.log('You have failed this city.'));
    ```

**[↑ 回到最上方](#table-of-contents)**

<a name="commas"></a>
## 逗号

<a name="commas--leading-trailing"></a><a name="19.1"></a>
  - [19.1](#commas--leading-trailing) 行首逗号： **请对它说不** 。 eslint: [`comma-style`](http://eslint.org/docs/rules/comma-style.html) jscs: [`requireCommaBeforeLineBreak`](http://jscs.info/rule/requireCommaBeforeLineBreak)

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

  <a name="commas--dangling"></a><a name="19.2"></a>
  - [19.2](#commas--dangling) 结尾的多余逗号： **要！它并不多余** 。 eslint: [`comma-dangle`](http://eslint.org/docs/rules/comma-dangle.html) jscs: [`requireTrailingComma`](http://jscs.info/rule/requireTrailingComma)

    > 为什么? 这会让 git diffs 更干净。另外，像 babel 这样的转译器会移除结尾多余的逗号，也就是说你不必担心老旧浏览器的 [行尾逗号问题](https://github.com/sivan/javascript-style-guide/blob/master/es5/README.md#commas)。（老旧浏览器在这里指  IE6/7 和 IE9 的怪异模式）
    > 
    > 你问我非 ES6 环境？看着办吧

    ```javascript
    // 差评 - 没有行尾逗号时， git diff 是这样的
    const hero = {
         firstName: 'Florence',
    -    lastName: 'Nightingale'
    +    lastName: 'Nightingale',
    +    inventorOf: ['coxcomb graph', 'modern nursing']
    };

    // 好评 - 有行尾逗号时， git diff 是这样的
    const hero = {
         firstName: 'Florence',
         lastName: 'Nightingale',
    +    inventorOf: ['coxcomb chart', 'modern nursing'],
    };

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
    ```

**[↑ 回到最上方](#table-of-contents)**


<a name="semicolons"></a>
## 分号

  <a name="semicolons--required"></a><a name="20.1"></a>
  - [20.1](#20.1) **要** 。 eslint: [`semi`](http://eslint.org/docs/rules/semi.html) jscs: [`requireSemicolons`](http://jscs.info/rule/requireSemicolons)

    > BY [dancerphil](https://github.com/dancerphil)：首先，我是不写的，一是受 Python 的影响，二是我司规定。[（知乎的相关讨论）](https://www.zhihu.com/question/20298345)
    > 
    > 至于 eslint，配置一下就好
    > 
    > 作为译者，坚持与原文的不同的地方有两处，这里是第二处

    ```javascript
    // 差评
    (function () {
      const name = 'Skywalker'
      return name
    })()

    // 好评
    (function () {
      const name = 'Skywalker';
      return name;
    }());

    // 好评，但是过于传统（这种做法曾被用来预防当两个存在 IIFE 的文件合并时括号被误当作一个函数的参数的情况）
    ;(() => {
      const name = 'Skywalker';
      return name;
    }());
    ```
    [IIFE](#7.2) - 立即调用的函数表达式
    
    [在 stackoverflow 阅读更多](http://stackoverflow.com/questions/7365172/semicolon-before-self-invoking-function/7365214%237365214)

**[↑ 回到最上方](#table-of-contents)**


<a name="type-casting--coercion"></a>
## 类型，强制类型转换

  <a name="coercion--explicit"></a><a name="21.1"></a>
  - [21.1](#coercion--explicit) 执行类型转换的位置应当是语句的开头

  <a name="coercion--strings"></a><a name="21.2"></a>
  - [21.2](#coercion--strings)  对字符串：

    ```javascript
    // => this.reviewScore = 9;

    // 差评
    const totalScore = this.reviewScore + ''; // 调用 this.reviewScore.valueOf()

    // 差评
    const totalScore = this.reviewScore.toString(); // 不能保证返回字符串

    // 好评
    const totalScore = String(this.reviewScore);
    ```

  <a name="coercion--numbers"></a><a name="21.3"></a>
  - [21.3](#coercion--numbers) 数字：使用 `Number` 来进行类型转换，在使用 `parseInt` 时，总是带上类型转换的基数。 eslint: [`radix`](http://eslint.org/docs/rules/radix)

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

  <a name="coercion--comment-deviations"></a><a name="21.4"></a>
  - [21.4](#coercion--comment-deviations) 如果因为某些原因， `parseInt` 成为了[性能瓶颈](http://jsperf.com/coercion-vs-casting/3)，而你就是任性的要使用 **位操作** (Bitshift)，那么留个注释解释一下原因吧

    ```javascript
    // 好评
    /**
     * 我的程序那么慢，全是 parseInt 的锅
     * 改成使用位操作后，很快很爽很开心
     */
    const val = inputValue >> 0;
    ```

  <a name="coercion--bitwise"></a><a name="21.5"></a>
  - [21.5](#coercion--bitwise) **注意** : 小心使用位操作运算符。数字会被当成 [64位变量](http://es5.github.io/#x4.3.19)，但是位操作运算符总是返回 32 位的整数([参考](http://es5.github.io/#x11.7))。位操作在处理大于 32 位的整数值时，并不会如你所料。[相关讨论](https://github.com/airbnb/javascript/issues/109)。最大的 32 位整数是 2,147,483,647 ：

    ```javascript
    2147483647 >> 0 //=> 2147483647
    2147483648 >> 0 //=> -2147483648
    2147483649 >> 0 //=> -2147483647
    ```

  <a name="coercion--booleans"></a><a name="21.6"></a>
  - [21.6](#coercion--booleans) 布尔值：

    ```javascript
    const age = 0;

    // 差评
    const hasAge = new Boolean(age);

    // 好评
    const hasAge = Boolean(age);

    // 好评如潮
    const hasAge = !!age;
    ```

**[↑ 回到最上方](#table-of-contents)**


<a name="naming-conventions"></a>
## 命名规则

  <a name="naming--descriptive"></a><a name="22.1"></a>
  - [22.1](#naming--descriptive) 别用单字母命名。让你的命名具备描述性

    ```javascript
    // 差评
    function q() {
      // ...一些代码...
    }

    // 好评
    function query() {
      // ..一些代码..
    }
    ```

  <a name="naming--camelCase"></a><a name="22.2"></a>
  - [22.2](#naming--camelCase) 使用驼峰式命名对象、函数和实例。 eslint: [`camelcase`](http://eslint.org/docs/rules/camelcase.html) jscs: [`requireCamelCaseOrUpperCaseIdentifiers`](http://jscs.info/rule/requireCamelCaseOrUpperCaseIdentifiers)

    ```javascript
    // 差评
    const OBJEcttsssss = {};
    const this_is_my_object = {};
    function c() {}

    // 好评
    const thisIsMyObject = {};
    function thisIsMyFunction() {}
    ```

  <a name="naming--PascalCase"></a><a name="22.3"></a>
  - [22.3](#naming--PascalCase) 使用帕斯卡式命名构造函数或类。它又被称为大驼峰式命名 eslint: [`new-cap`](http://eslint.org/docs/rules/new-cap.html) jscs: [`requireCapitalizedConstructors`](http://jscs.info/rule/requireCapitalizedConstructors)

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

  <a name="naming--leading-underscore"></a><a name="22.4"></a>
  - [22.4](#naming--leading-underscore) 不要以下划线作为开头或结尾。 eslint: [`no-underscore-dangle`](http://eslint.org/docs/rules/no-underscore-dangle.html) jscs: [`disallowDanglingUnderscores`](http://jscs.info/rule/disallowDanglingUnderscores)

    > 为什么？事实上，Javascript 没有隐私方面的概念。虽然在一般的约定中，在前面加下划线的意思是 private ，但事实上，这些属性完完全全的就是公开的，也因此，意味着它们也是你公共 API 的一部分。这个一般的约定误导一部分程序员，使他们错误的认为一个小小的变化不会引起什么结果，或者认为测试是可有可无的
    > 
    > 太长不看：如果你真的需要一些“私密”的属性，它不应该被双手奉上

    ```javascript
    // 差评
    this.__firstName__ = 'Panda';
    this.firstName_ = 'Panda';
    this._firstName = 'Panda';

    // 好评
    this.firstName = 'Panda';
    ```

  <a name="naming--self-this"></a><a name="22.5"></a>
  - [22.5](#naming--self-this) 别保存 `this` 的引用。使用箭头函数或 Function#bind。 jscs: [`disallowNodeTypes`](http://jscs.info/rule/disallowNodeTypes)

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

  <a name="naming--filename-matches-export"></a><a name="22.6"></a>
  - [22.6](#naming--filename-matches-export) 你的文件名应当和默认的类名保持完全一致

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

  <a name="naming--camelCase-default-export"></a><a name="22.7"></a>
  - [22.7](#naming--camelCase-default-export) 使用驼峰式命名导出默认函数。你的文件名应当和函数名保持完全一致

    ```javascript
    function makeStyleGuide() {
    }

    export default makeStyleGuide;
    ```

  <a name="naming--PascalCase-singleton"></a><a name="22.8"></a>
  - [22.8](#naming--PascalCase-singleton) 使用帕斯卡式命名导出构造函数、类、单例(singleton)、函数库、空对象

    ```javascript
    const AirbnbStyleGuide = {
      es6: {
      }
    };

    export default AirbnbStyleGuide;
    ```


**[↑ 回到最上方](#table-of-contents)**


<a name="accessors"></a>
## 存取器

  <a name="accessors--not-required"></a><a name="23.1"></a>
  - [23.1](#accessors--not-required) 并非所有属性的存取器函数都是必须的

  <a name="accessors--no-getters-setters"></a><a name="23.2"></a>
  - [23.2](#accessors--no-getters-setters) 不要使用 JavaScript 的 getters/setters ，因为它们会导致你不期待的副作用，而且使测试、维护和推论。如果你一定要存取器函数，使用 getVal() 和 setVal('hello')

    ```javascript
    // 差评
    dragon.age();

    // 好评
    dragon.getAge();

    // 差评
    dragon.age(25);

    // 好评
    dragon.setAge(25);
    ```

  <a name="accessors--boolean-prefix"></a><a name="23.3"></a>
  - [23.3](#accessors--boolean-prefix) 如果属性或方法是一个 `boolean` ，使用 `isVal()` 或 `hasVal()`

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

  <a name="accessors--consistent"></a><a name="23.4"></a>
  - [23.4](#accessors--consistent) 你当然可以创建 get() 和 set() 函数，但是注意保持一致

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

**[↑ 回到最上方](#table-of-contents)**


<a name="events"></a>
## 事件

  <a name="events--hash"></a><a name="24.1"></a>
  - [24.1](#events--hash) 当事件需要一些附加数据时（无论是 DOM 事件还是私有事件），传入一个哈希而不是原始值。这样可以让后面的开发者简单的增加更多数据到事件中，而无需找出并更新事件的每一个处理器。比如不要写：

    ```javascript
    // 差评
    $(this).trigger('listingUpdated', listing.id);

    ...

    $(this).on('listingUpdated', (e, listingId) => {
      // 使用 listingId
    });
    ```

    而写：

    ```javascript
    // 好评
    $(this).trigger('listingUpdated', { listingId: listing.id });

    ...

    $(this).on('listingUpdated', (e, data) => {
      // 使用 data.listingId
    });
    ```

  **[↑ 回到最上方](#table-of-contents)**


<a name="jquery"></a>
## jQuery

  <a name="jquery--dollar-prefix"></a><a name="25.1"></a>
  - [25.1](#jquery--dollar-prefix) jQuery 对象的变量名统一加上前缀 `$` 。 jscs: [`requireDollarBeforejQueryAssignment`](http://jscs.info/rule/requireDollarBeforejQueryAssignment)

    ```javascript
    // 差评
    const sidebar = $('.sidebar');

    // 好评
    const $sidebar = $('.sidebar');

    // 好评
    const $sidebarBtn = $('.sidebar-btn');
    ```

  <a name="jquery--cache"></a><a name="25.2"></a>
  - [25.2](#jquery--cache) 缓存 jQuery 查询，避免无用的查询

    ```javascript
    // 差评
    function setSidebar() {
      $('.sidebar').hide();

      // ...一些代码...

      $('.sidebar').css({
        'background-color': 'pink'
      });
    }

    // 好评
    function setSidebar() {
      const $sidebar = $('.sidebar');
      $sidebar.hide();

      // ...一些代码...

      $sidebar.css({
        'background-color': 'pink'
      });
    }
    ```

  <a name="jquery--queries"></a><a name="25.3"></a>
  - [25.3](#jquery--queries) 查询 DOM 时，使用层叠 `$('.sidebar ul')` 或 parent > child 的模式 `$('.sidebar > ul')`. [jsPerf](http://jsperf.com/jquery-find-vs-context-sel/16)

  <a name="jquery--find"></a><a name="25.4"></a>
  - [25.4](#jquery--find) 查询有作用域的 jQuery 对象时，使用 `find` 

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

**[↑ 回到最上方](#table-of-contents)**


<a name="ecmascript-5-compatibility"></a>
## ECMAScript 5 兼容性

  <a name="es5-compat--kangax"></a><a name="26.1"></a>
  - [26.1](#es5-compat--kangax) 参考 [Kangax](https://twitter.com/kangax/) 的 ES5 [兼容性表格](http://kangax.github.io/es5-compat-table/)，里面有你要的一切

**[↑ 回到最上方](#table-of-contents)**

<a name="ecmascript-6-styles"></a>
## ES6 代码风格

  <a name="es6-styles"></a><a name="27.1"></a>
  - [27.1](#es6-styles) ES6 代码风格分布在各个篇目中，我们整理了一下，以下是链接到 ES6 的各个特性的列表

1. [Arrow Functions](#arrow-functions)
1. [Classes](#constructors)
1. [Object Shorthand](#es6-object-shorthand)
1. [Object Concise](#es6-object-concise)
1. [Object Computed Properties](#es6-computed-properties)
1. [Template Strings](#es6-template-literals)
1. [Destructuring](#destructuring)
1. [Default Parameters](#es6-default-parameters)
1. [Rest](#es6-rest)
1. [Array Spreads](#es6-array-spreads)
1. [Let and Const](#references)
1. [Iterators and Generators](#iterators-and-generators)
1. [Modules](#modules)

**[↑ 回到最上方](#table-of-contents)**

<a name="testing"></a>
## 测试

  <a name="testing--yup"></a><a name="28.1"></a>
  - [28.1](#testing--yup) **测试这个东西，真的可以有！**

    ```javascript
    function foo() {
      return true;
    }
    ```

  <a name="testing--for-real"></a><a name="28.2"></a>
  - [28.2](#testing--for-real) **好吧，认真一点：**
   - 不论你使用了什么测试框架，你都应该写测试！
   - 尽量写很多小的纯函数(small pure functions)，这样你可以在很小的区域里探知异常发生的地点
   - 对 stubs 和 mocks 保持严谨 - 他们使你的测试变得更加脆弱
   - 在 Airbnb 我们主要使用 [`mocha`](https://www.npmjs.com/package/mocha) ，对一些小型或单独的模块会使用 [`tape`](https://www.npmjs.com/package/tape)
   - 达到 100% 测试覆盖率，虽然实现它是不切实际的，但这是一个非常好的目标
   - 每当你修复一个 bug ，就为这个 bug 写 **回归测试** 。一个修复的 bug 如果没有回归测试，一般而言都会复发

**[↑ 回到最上方](#table-of-contents)**


<a name="performance"></a>
## 性能

  - [On Layout & Web Performance](http://www.kellegous.com/j/2013/01/26/layout-performance/)
  - [String vs Array Concat](http://jsperf.com/string-vs-array-concat/2)
  - [Try/Catch Cost In a Loop](http://jsperf.com/try-catch-in-loop-cost)
  - [Bang Function](http://jsperf.com/bang-function)
  - [jQuery Find vs Context, Selector](http://jsperf.com/jquery-find-vs-context-sel/13)
  - [innerHTML vs textContent for script text](http://jsperf.com/innerhtml-vs-textcontent-for-script-text)
  - [Long String Concatenation](http://jsperf.com/ya-string-concat)
  - [Are Javascript functions like `map()`, `reduce()`, and `filter()` optimized for traversing arrays?](https://www.quora.com/JavaScript-programming-language-Are-Javascript-functions-like-map-reduce-and-filter-already-optimized-for-traversing-array/answer/Quildreen-Motta)
  - 载入中...

**[↑ 回到最上方](#table-of-contents)**


<a name="resources"></a>
## 学习资源

**学习 ES6**

  - [Draft ECMA 2015 (ES6) Spec](https://people.mozilla.org/~jorendorff/es6-draft.html)
  - [ExploringJS](http://exploringjs.com/)
  - [ES6 Compatibility Table](https://kangax.github.io/compat-table/es6/)
  - [Comprehensive Overview of ES6 Features](http://es6-features.org/)

**阅读**

  - [Standard ECMA-262](http://www.ecma-international.org/ecma-262/6.0/index.html)

**工具**

  - Code Style Linters
    + [ESlint](http://eslint.org/) - [Airbnb Style .eslintrc](https://github.com/airbnb/javascript/blob/master/linters/.eslintrc)
    + [JSHint](http://jshint.com/) - [Airbnb Style .jshintrc](https://github.com/airbnb/javascript/blob/master/linters/.jshintrc)
    + [JSCS](https://github.com/jscs-dev/node-jscs) - [Airbnb Style Preset](https://github.com/jscs-dev/node-jscs/blob/master/presets/airbnb.json)

**其他风格指南**

  - [Google JavaScript Style Guide](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml)
  - [jQuery Core Style Guidelines](http://contribute.jquery.org/style-guide/js/)
  - [Principles of Writing Consistent, Idiomatic JavaScript](https://github.com/rwaldron/idiomatic.js)

**其他风格**

  - [Naming this in nested functions](https://gist.github.com/cjohansen/4135065) - Christian Johansen
  - [Conditional Callbacks](https://github.com/airbnb/javascript/issues/52) - Ross Allen
  - [Popular JavaScript Coding Conventions on Github](http://sideeffect.kr/popularconvention/#javascript) - JeongHoon Byun
  - [Multiple var statements in JavaScript, not superfluous](http://benalman.com/news/2012/05/multiple-var-statements-javascript/) - Ben Alman

**更多阅读**

  - [Understanding JavaScript Closures](http://javascriptweblog.wordpress.com/2010/10/25/understanding-javascript-closures/) - Angus Croll
  - [Basic JavaScript for the impatient programmer](http://www.2ality.com/2013/06/basic-javascript.html) - Dr. Axel Rauschmayer
  - [You Might Not Need jQuery](http://youmightnotneedjquery.com/) - Zack Bloom & Adam Schwartz
  - [ES6 Features](https://github.com/lukehoban/es6features) - Luke Hoban
  - [Frontend Guidelines](https://github.com/bendc/frontend-guidelines) - Benjamin De Cock

**相关书籍**

  - [JavaScript: The Good Parts](http://www.amazon.com/JavaScript-Good-Parts-Douglas-Crockford/dp/0596517742) - Douglas Crockford
  - [JavaScript Patterns](http://www.amazon.com/JavaScript-Patterns-Stoyan-Stefanov/dp/0596806752) - Stoyan Stefanov
  - [Pro JavaScript Design Patterns](http://www.amazon.com/JavaScript-Design-Patterns-Recipes-Problem-Solution/dp/159059908X)  - Ross Harmes and Dustin Diaz
  - [High Performance Web Sites: Essential Knowledge for Front-End Engineers](http://www.amazon.com/High-Performance-Web-Sites-Essential/dp/0596529309) - Steve Souders
  - [Maintainable JavaScript](http://www.amazon.com/Maintainable-JavaScript-Nicholas-C-Zakas/dp/1449327680) - Nicholas C. Zakas
  - [JavaScript Web Applications](http://www.amazon.com/JavaScript-Web-Applications-Alex-MacCaw/dp/144930351X) - Alex MacCaw
  - [Pro JavaScript Techniques](http://www.amazon.com/Pro-JavaScript-Techniques-John-Resig/dp/1590597273) - John Resig
  - [Smashing Node.js: JavaScript Everywhere](http://www.amazon.com/Smashing-Node-js-JavaScript-Everywhere-Magazine/dp/1119962595) - Guillermo Rauch
  - [Secrets of the JavaScript Ninja](http://www.amazon.com/Secrets-JavaScript-Ninja-John-Resig/dp/193398869X) - John Resig and Bear Bibeault
  - [Human JavaScript](http://humanjavascript.com/) - Henrik Joreteg
  - [Superhero.js](http://superherojs.com/) - Kim Joar Bekkelund, Mads Mobæk, & Olav Bjorkoy
  - [JSBooks](http://jsbooks.revolunet.com/) - Julien Bouquillon
  - [Third Party JavaScript](https://www.manning.com/books/third-party-javascript) - Ben Vinegar and Anton Kovalyov
  - [Effective JavaScript: 68 Specific Ways to Harness the Power of JavaScript](http://amzn.com/0321812182) - David Herman
  - [Eloquent JavaScript](http://eloquentjavascript.net/) - Marijn Haverbeke
  - [You Don't Know JS: ES6 & Beyond](http://shop.oreilly.com/product/0636920033769.do) - Kyle Simpson

**博客**（我很喜欢“部落格”，台湾会这么说。为了部落！格。）

  - [DailyJS](http://dailyjs.com/)
  - [JavaScript Weekly](http://javascriptweekly.com/)
  - [JavaScript, JavaScript...](http://javascriptweblog.wordpress.com/)
  - [Bocoup Weblog](https://bocoup.com/weblog)
  - [Adequately Good](http://www.adequatelygood.com/)
  - [NCZOnline](https://www.nczonline.net/)
  - [Perfection Kills](http://perfectionkills.com/)
  - [Ben Alman](http://benalman.com/)
  - [Dmitry Baranovskiy](http://dmitry.baranovskiy.com/)
  - [Dustin Diaz](http://dustindiaz.com/)
  - [nettuts](http://code.tutsplus.com/?s=javascript)

**播客** (Podcasts)

  - [JavaScript Jabber](https://devchat.tv/js-jabber/)


**[↑ 回到最上方](#table-of-contents)**

<a name="chat-with-us-about-javascript"></a>
## 和我们一起讨论JS

  - 在这里找我们： [gitter](https://gitter.im/airbnb/javascript).

<a name="contributors"></a>
## 贡献者

  - [查看为原文作出贡献的同学们](https://github.com/airbnb/javascript/graphs/contributors)
  - 感谢：[yuche](https://github.com/yuche/javascript)
  - [查看为译文作出贡献的同学们](https://github.com/dancerphil/trick/graphs/contributors)


<a name="license"></a>
## 许可

(The MIT License)

Copyright (c) 2014-2016 Airbnb

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

**[↑ 回到最上方](#table-of-contents)**

<a name="amendments"></a>
## 修订

非常鼓励你 [`fork`](https://github.com/dancerphil/trick) 这份代码风格指南，稍加改动后作为你们团队的指南。欢迎讨论

# };
