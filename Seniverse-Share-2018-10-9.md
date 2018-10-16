# 函数式编程

1. 函数不是一个过程，而是一个合约

    `arr.sort(func)` 会因为 `func` 的不同而有不同的表现，此时，传入的函数是一个合约。

    ```javascript
    arr.sort()
    arr.sort((a, b) => a - b)
    arr.sort(() => 0.5 - Math.random())
    ```

    （这个例子有一个不符合函数式编程的地方）

    > 合约这个词有点像区块链中可以将一块代码作为区块保存并使其不可变的概念。同样在冯诺伊曼体系中，一个很重要的理念就是代码和数据共同储存。函数式编程在某种程度上是在更小的问题范围内实现这个思路。
    >
    > 更进一步举例，react 生命周期方法也是在恰当的时候被调用的一份合约。

1. 把函数和字符串，对象一样思考，函数具有第一性

    用函数表达概念，函数本身也是一个编程单位，和字符串之类没有区别。

    在线性代数中，矩阵可以表示一个状态，也可以表示一个状态的转移。当我们有 `result = scale(rotate(origin))`，我们就能构造 `transform = scale(rotate)`，并使 `result = transform(origin)`

1. 无状态

    引用透明（Referential transparency），指的是函数的运行不依赖于外部变量或状态，只依赖于输入的参数，任何时候只要参数相同，引用函数所得到的返回值总是相同的。

1. 无副作用

    副作用（side effect），指的是函数内部与外部互动（最典型的情况，就是修改全局变量的值），产生运算以外的其他结果。函数式编程强调没有副作用，意味着函数要保持独立，所有功能就是返回一个新的值，没有其他行为，尤其是不得修改外部变量的值。

1. 文件域，函数工厂和高阶函数

   react 的文件域是一个组件工厂，如果把 import 视为函数的参数 export 视为函数的返回，之间的部分其实可以用函数式编程来思考。

   函数的柯里化，和高阶函数（传入一个或多个函数，并返回一个函数）是函数第一性的应用

1. 相关的库

    [lodash](https://lodash.com/docs/4.17.10)

# 函数式的生命周期方法带来的好处

1. 在 react 中使用函数式编程

    组件的声明法，有函数式的声明法和类式的声明法。对于函数式声明法，直接遵循上述的思路。

    类式的声明法，区别之一在于生命周期函数。那么生命周期函数也应该遵循无状态和无副作用的原则。当然没有返回的生命周期函数，本来就是用于理清副作用（cDM, cDU 等）。

    但是事实上，有很多情况下我们会有状态，这是声明式的另一个区别，即可以拥有自己的 state。

1. 提升 state

    props 和 state 引起的组件行为是一致的，这是 react 核心团队[仔细考虑](./React-Issues-11527.md)后的结果，这使得提升 state 是简单的。

    当 state 是组件本身独有的，并且组件是为了重用而存在的，我们应当使用 state。

    除此之外，我们应该提升 state。把它放到 redux 然后 connect，以 props 的形式作用于组件（如果它确实需要作用于组件）。

1. 为什么提升 state，并使用函数式编程

    1. 没有 state 的组件是简单的。

    1. state 不是为了实现共享的需求而出现的。

    1. 如果多个组件公用一个 state，我们没有什么理由说 state 属于其中的某一个。

    1. benefit from 统一的 action 处理相关的代码。（前提是你的 redux 相关的实现足够好用）

    1. 在使得 redux 复杂的同时，使得 react 简单，使得 react 的渲染可以是完全纯函数式的 pipe。一个纯的 react 可以简单的表示为 `view = render(model)`。

1. 使用 PureComponent 优化渲染次数

    如果一个组件是纯的（只依赖 props 和 state，并且相同的 props 和 state 会产生相同的结果），Component 和 PureComponent 的渲染结果是一样的，后者的渲染次数小于等于前者。

    需要指出，PureComponent 比 Component 更重，因为多余的 shallowCompare 进行了额外的计算，所以需要分两部分说：

    如果当组件的父亲一旦渲染，引起组件渲染，没有需要剪枝时，使用 Component 更好。但是如果父亲进行了渲染，但组件本身和组件的儿子需要保持稳定（可能是渲染的花费很大），那么就应该剪枝，一般情况下，PureComponent 是一个较优的实践。

1. 展示组件和容器组件

    我们应该把组件分成展示组件和容器组件，如[这篇译文](./Presentational-And-Container-Components.md)所阐述的。

    展示组件的部分，默认是一个成熟的组件库，如何打造一个成熟的组件库不在这篇文章的讨论范围内。

    当然，除了这两类，组件可能还包含一些特殊类型。比如交互增强型（antd 的表单增强）和功能型（Router, transition），这些没有太多需要讨论的地方。

# 哪些 props 是不需要层层传递的

现在我们只考虑容器类组件，它们通常是 view 层的逻辑的部分，是视图的骨架，容器类组件的最佳实践是一个尚未有定论的问题，尝试讨论它。

首先，不要传递太多东西。就像一个函数的参数非常多的时候，这个函数变的更加耦合了，当组件有很多 props 的时候，组件变得更加耦合了。

1. 只要是不可变的部分都可以被抽出

    函数或组件内可以调用多个外部的函数，也可以依赖外部的值，这并不违反函数式编程的理念。因为它们是不可变的。引用透明的另一层意思，我认为在于非引用的不透明。

    编程是关于逻辑的细致的轮廓线的工作。我们可以用函数来表达一个数据，也可以用数据表达一个函数。这不影响代码块之间的耦合性。但是不恰当的传入函数不需要的东西，是增加耦合性的过程。

1. props 只包含需要引起重渲染的数据

    举例来说，如果 action 是不可变的，为什么要通过 props 传递呢？我们不需要通过改变一个 action 来使组件重渲染，那么 action 就不应该被层层传递。它只需要被最后一个容器组件 import，然后作为 callback 传给展示组件即可。

    同样的，组件应该只包含自己需要的部分的数据，而不是先读入大量的数据，然后取用其中的一小部分。

1. 相同的组件

    是否能共用一个容器组件的前提应该是这两处拥有必然相同的逻辑，如果组件可能会随着需求的变化而变得不同，它们就应该是不同的组件，只是其中相同的部分再抽出成为一个展示组件。

    经过这样的分割，容器组件也许只是简单的调用另一个展示组件，是一个简单的 wrap，但是好处是提供了可以为这个容器组件定制逻辑的可能性。而展示组件也因为被调用而拥有可复用能力。

# 如何移除 redux 全家桶

随着 js, react, redux 本身的发展，全家桶所 handle 的很多情况都逐渐有了更优更一致的写法。

1. dva

    先说 dva，把 action, saga 和 reducer 写在一个 module 下，成为一个巨大的对象，并没有得到什么好处。把它们单纯的文件分割，单一职责的原则都会实践得更好，更不说 module 模式本身有无法适应的情况。一些常用的 dva 插件，本身的逻辑也可以通过新的语言特性独立存在。

1. redux-saga

    redux-saga 在 async-await 还没有成为标准的时代，成为了异步的基本唯一选择，但是如果你已经开始普及使用 async-await，redux-saga 的代码就很不协调。离开 generator 和 yield 的同时，我们也离开了 action 同时发出 saga 和 reducer， saga 又发出新的 action 这样奇怪的事件流。

    当一个 handler 被触发，它可以发出多个 action，也可以任意的 await 发出先后的 action，从 action 到 reducer 的数据流动就更纯。

    其实即使没有 async-await，也可以写出相似的代码，产生的问题是回调金字塔，redux-saga 的 co 式写法，解决了回调金字塔的问题之后，也引入了新的复杂性，在 async-await 已经成为解决方案的今天，co 式写法就没有优势了。

    不过 redux-saga 还提供了很多 effect，如 takeLatest 之类，它们可以做成一个新的库，暂时还没有找到以这个思路出发的开源项目，如果现有的代码大量依赖这些 effect，可能使你难以放弃 redux-saga。

1. redux-promise

    await 就能解决

1. redux-thunk

    当接触 redux middleware 时，第一个例子就是 redux-thunk，在有 async-await 后，我发现它可能是没有必要的，考虑官方示例：

    ```javascript
    const increment = () => {
      return { type, payload };
    }

    const incrementAsync = () => dispatch => {
      setTimeout(() => {
        dispatch(increment());
      }, 1000);
    }

    // 等价于
    const incrementAsync = () => {
      setTimeout(() => {
        dispatch({ type, payload });
      }, 1000);
    }

    // 再等价于
    const incrementAsync = async () => {
      await delay(1000)
      dispatch({ type, payload })
    }
    ```

    你可能疑惑，dispatch 是怎么来的，注意到 redux 的 store.dispatch，可以写：

    ```javascript
    const store = getStore();
    const { dispatch, getState } = store;

    export { dispatch, getState };
    ```

# fetchXXX 有没有一个终极解决方案

（编辑中）

# react 这几年比较大的变化

1. mixin => hoc

    在 react@15 之前，代码中大量使用 mixin，而后 mixin 被认为是有害的，并被高阶函数的写法代替，[详见](https://reactjs.org/blog/2016/07/13/mixins-considered-harmful.html)

    这一次的变动，主要解决的问题是如何使组件共享代码。站在现在的角度，可能的使组件共享代码的方式可能有以下几种：

    1. 展示组件，我们可以用 `const Parent = ({ children }) => <div>{children}<div/>` 的方式抽出公共部分的代码，展示组件可以很复杂，但总是提供最少的 props，并承担单一职责。

    1. 继承或 mixin，比如继承 PureComponent 得到一个通用的 shouldComponentUpdate 方法，甚至实现多继承。

    1. 高阶组件，传入一个组件并传出另一个包装后的组件的函数，比如 react-redux 的 connect

    1. 工具函数，这种已经属于 react 之外的代码共用。

    Dan Abramov 认为符合函数式的方式是更好的，mixin 引起了诸多问题，容易使代码的相互依赖不容易被找到。

    注：天气插件 sunshine 使用了 react@0.14 以及 mixin。

1. Fiber

    介绍视频：[Intro to Fiber](https://www.youtube.com/watch?v=ZCuYPiUIONs)

    如果你需要更深的技术细节，查看[acdlite 2016/10/19 的文章](https://github.com/acdlite/react-fiber-architecture)。（Fiber 写了两年）

    react 核心团队认为，react 的未来在 async rendering，而为了支持这个未来，它们使用 Fiber 代替了原来的 vdom。

    考虑原来的 render 流程，是一整个 vdom 到一整个真实 dom 的对应。

    它的好处有两点。第一，你不再需要操作 dom，你只需要告诉 react ViewModel 是怎样的，react 自动的操作 dom。第二是组件化，每个组件在一次 flush 中，从 parent 得到控制，交给 children，随后从 children 得到控制，再交还 parent，它仅仅考虑自身变化的部分，调用自身的生命周期方法，而不需要考虑其他的组件。

    它的坏处，每一次 flush 不能中断，parent 触发 render 到 children 的 render，它一直会等待控制回来，至少它还需要触发 componentDidXXX 的钩子，这个流程是无法被打破的。整个刷新就是 vdom 树的一次递归遍历。

    react 本身也许可以足够快，但是生命周期方法，用户的代码，是不可预测的，并会造成阻塞。所以，需要使 Reconcile 过程可分解。

    在算法里，所有的递归都可以改循环，Fiber 做的就是这个事，准确地说，Fiber 是一系列任务的链表，在任何时候都可以跳出去，把控制交还给浏览器，来处理用户操作，如果对接下来的任务没有影响，则可以继续工作。

    但如果有新的更新，就会重新进行 Reconcile 过程，这是 componentWillXXX 系列可能会多次触发从而变得不安全的原因。

    由于 Fiber 不再是一棵树，而是链表，那么 Fragment 就是最简单且显然的结果，把组件连在上一个组件的 siblings 就行了。Fiber 也引出了一系列其他新特性以及未来的 async rendering。

1. react@16 的新接口

    1. 转移了 createClass, propTypes 等多个接口，以减小包体积。

        其中 createClass 是不再提倡使用了，而 propTypes 独立后，可以支持其他类 react 框架。同时在推的 flow，有一部分和 prop-types 重合了。查看[关于类型的讨论](https://www.zhihu.com/question/273619114/answer/511141524)

    1. render return types: Fragment, string, array

        如上文所述的 Fiber 顺便带来的新特性

    1. createPortal, componentDidCatch

        createPortal 提供了一个"传送"到已经存在的 dom 上的能力。

        在 react@15，组件渲染过程中的报错会引起一整棵错误的树，并且无法从错误中恢复。在 react@16 推出了新的 componentDidCatch 生命周期方法。

        使用 componentDidCatch 记录前端报错日志。后端提供一个接口，用一个 componentDidCatch 包裹整个 App，并连接接口即可。

        使用 componentDidCatch 实现 Suspense，[详见](https://codesandbox.io/s/18r32mov6j)

        我认为它们都是为了某类特定组件的抽象而提供功能，可以在特殊的封装组件中用到，平常开发中不应该用到。

    1. 废弃 componentWillMount, componentWillReceiveProps, componentWillUpdate

        废弃它们的原因是在新的 Fiber 架构中，Reconcile 是不确定被调用的次数的，所以三个生命周期方法都可能被多次调用引起不安全。

        而事实上很多人在使用这三个生命周期方法时，确实会引入副作用，违反了函数式编程的理念。由于 react 无法强制用户的不安全行为，所以把它们标记为 UNSAFE。

        官方提供了一系列 migrate path，大部分情况下，可以将逻辑移入 componentDidMount, static getDerivedStateFromProps, componentDidUpdate 来实现相同的功能。

    1. static getDerivedStateFromProps, getSnapshotBeforeUpdate

        （编辑中，给出 antd 的 migrate 例子）

# react 的未来

1. React Fire

    DOM 相关的重构，和本文关系不大，最大的影响代码的改动是 className => class。

1. 并行的 Fiber 的可能性

    相比于原来的架构，Fiber 提供了并行的可能性，一旦有浏览器可以提供并行特性，现有的每一个切片可以并行并且在单独线程运行，重新组合成 dom diff。

1. 从废弃的 Fiber 中找出可重用的部分

    现有的 Fiber，在遭遇高优先级任务时，现有的工作会被插队和放弃，重新 build 高优先级的 Reconcile 过程。有的时候，高优先级任务并不一定破坏了原有的工作，甚至这些工作可以合并（类似 git merge）。

    由于可能的 conflict，现在会直接放弃低优先级任务，但找出重用的部分是可能的。

1. Suspense & TimeSlicing

    ```
    import { unstable_Profiler, Placeholder } from 'react';
    ```

    查看[视频](https://reactjs.org/blog/2018/03/01/sneak-peek-beyond-react-16.html)

    Suspense 是一项更偏向业务的功能，其原理是当组件接收到一个 promise，随即报错，被 componentDidCatch 捕获 promise，显示 PlaceHolder（loading 状态），然后等待 promise resolve，重新渲染组件。

    TimeSlicing 指在 CPU 运算时不 block 高优先级事务（当然如果你自己写的函数会 block，react 也没有办法），不需要写任何 code 就可以享受。

    这两个功能是现在的 react 团队正在开发的功能。我觉得 demo 里 Suspense 的接口不能说非常好用，而且 Suspense 的功能和 async rendering 有一些冲突，个人理解是 react 希望在 async rendering 到来之前收集和培养一些用例，并且使 async rendering 的部分代码得到测试，以期望更多人可以 forward 到 async rendering 上。

    所以，如果使用 Suspense，也是先实现特定组件，不应该在平常开发中使用。

1. async lifecycle method

    ```
    import { unstable_AsyncMode } from 'react';
    ```

    AsyncMode 的最终样子还没有确定，可能有几种我依次介绍，其核心概念就是，组件可以是一个 promise，这个组件就被称为 Lazy 组件。

    看上去最好的写法应该是这样：

    ```
    const AsyncComponent = async () => {
      const text = await fetchText();
      return <div>{text}</div>;
    }

    // or
    class AsyncComponent extends Component {
      async render() {
        const text = await fetchText();
        return <div>{text}</div>;
      }
    }
    ```

    当然也可以 `fetchText().then(({ text }) => <div>{text}</div>)`，似乎可以重用相当的代码

    AsyncMode 一度在开发列表里，但随后被延迟了，react 团队需要先处理多个依赖才能重新推进 AsyncMode 的进程，估计要在 react@17 的后期才能实装。

    以上均是 Fiber 带来的新的可能性。

1. no state, no setState

    多位 react 团队成员都透露了认为现有的 setState 并不是一个好的设计的观点，但是 state 广泛存在于代码中，或许有一天 react 会写出一篇 You Don't need state 来阐述这个思想，并提供 migrate，但是在没有和未来的 feature 冲突的情况下，优先级不高。

    [mobx](https://github.com/mobxjs/mobx), [unstated](https://github.com/jamiebuilds/unstated) 和 [recompose](https://github.com/acdlite/recompose) 已经在某种程度上证明，没有 state 是完全可行的。比如 recompose 的一段示例代码：

    ```
    const enhance = withState('counter', 'setCounter', 0)
    const Counter = enhance(({ counter, setCounter }) =>
      <div>
        Count: {counter}
        <button onClick={() => setCounter(n => n + 1)}>Increment</button>
        <button onClick={() => setCounter(n => n - 1)}>Decrement</button>
      </div>
    )
    ```

    思考我们用一个 polyfill 实现 state 以及 setState 的功能，react 本身是可以没有 state 的。为了讨论，把它命名为 mini-redux。（以怎样的形式 polyfill 我觉得可以类似 react-lifecycles-compat）

    由于 react 在实现 state 和 props 时的内在一致性，这样做的唯一的变化是把从 state 读数据改成了从 props 读数据，而好处是把函数的引用透明提升到组件级别的引用透明。

    这样，一部分生命周期会被提升到组件 Reconcile 之外的 mini-redux，作为高阶组件的 implement 的一部分而存在。最显然的例子是通过 static getDerivedStateFromProps 实现的完全可控/完全不受控组件的 migrate path。由于 getDerivedStateFromProps 强行隔开了 this， 这一步会比较简单。

    ```
    （编辑中，以 antd 的 migrate 例子描述进一步的改动）
    ```

1. 整个 Reconcile 过程的函数式

    如果可以把 state 完全移除，由 Component 或 PureComponent 完全控制组件树的剪枝，会发现整个 Reconcile 过程只剩下一个生命周期函数，即 render。那么 render 可以成为一个 static 函数了。static render 接收 props，返回 reactElement，并且不依赖 this。

    这当然是非常理想化的，ref, context 的问题怎么解决，都是需要考虑的问题。而且作为一个第三方，只是使用 react 而不能修改 react 的源码（也不想自己造一个轮子），后续的优化就谈不上了。从历史进程来看，首先要做的也是挖掘 Fiber 带来的更多可能性，这样的优化暂时不可能成为开发的痛点。
