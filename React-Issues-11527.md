## 征求澄清：为什么 `setState` 是异步的？

> BY 张振衣([dancerphil@github](https://github.com/dancerphil/trick/))
>
> [原文](https://github.com/facebook/react/issues/11527)
>
> 本文适合有 react 使用经验，并且对 js 的异步机制有一定了解的同学。这里是一些可能有用的参考：[协调(reconciliation)](https://react.bootcss.com/react/docs/reconciliation.html), [批量更新(batch/batchUpdate)](http://undefinedblog.com/understand-react-batch-update/), [异步渲染(开发中)](https://github.com/facebook/react/issues/8830)

---

> [mweststrate](https://github.com/mweststrate) 在 [2017年11月11日](https://github.com/facebook/react/issues/11527#issue-273131299) 问道：

很长一段时间以来，我试着去理解为什么 `setState` 是异步的。在努力找寻答案的尝试失败之后，我个人得出的结论是，这是历史原因并且在现在这个时间可能很难改变它。不过 [@gaearon](https://github.com/gaearon) 坚持说这是有明确理由的，所以我很好奇这个理由到底是什么 :)

无论如何，下面列举了一些我经常听到的理由，但我不认为它们是正确的，因为它们很容易被反驳

## 异步的 setState 之所以被需要是因为异步渲染(async rendering)
很多初学者认为是因为渲染效率。但我并不这样认为，因为在异步渲染的同时保持同步 `setState` 对我来说是一件微不足道的事情，与听起来对我来说微不足道，下列代码就可以说明问题：

``` js
Component.prototype.setState = (nextState) => {
  this.state = nextState
  if (!this.renderScheduled)
     setImmediate(this.forceUpdate)
}
```
事实上，比如 mobx-react 就允许同步分配观察值，并仍然尊重渲染的异步特性

## 异步的 setState 之所以被需要是因为它被用于找出哪个 state 被渲染了
另一个我时常会听到的论点是，你想要推断被渲染的状态，而不是被请求的状态。但是我不觉得为了这个原则（而选择异步 setState）是值得的。概念上说，我觉得非常奇怪。因为渲染是一个副作用，而状态只和事实有关。今天我32岁，明年我会变成33岁，不管今年这个组件有没有被重新渲染，这个事实都没有改变 :)

打一个（也许不那么恰当的）比方：如果在你把一个你自己写的 word 文档打印出来之前，你都不能知道它的最后一个版本是怎样的，这将是非常尴尬的。再例如，我不认为游戏引擎会给你提供一些反馈，来说明游戏的状态是恰好完全渲染的，还是说有一些帧被丢弃了。

一个有趣的观察是：两年来，在 `mobx-react` 上，至今没有人问我这个问题：我怎么知道我的 observables 被渲染了？这个问题看上去似乎是一个毫不相关的问题。

我确实遇到了一些与此相关的情况，需要知道哪些数据被渲染了。我记得是，我需要知道某些数据的像素尺寸来完成布局。但是，我通过使用 `didComponentUpdate` 优雅地解决了这个问题，而并没有真正依靠 `setState` 的异步特性。这些情况看起来非常罕见，几乎没有理由围绕着这些情况设计 api。只要能以某种方式完成，我想就足够了

我毫不怀疑，React 团队早已经意识到了 `setState` 的异步性质所经常引入的混淆，所以我猜肯定有另一个非常好的理由来支持当前的语义。 告诉我更多 :)

---

> 一些讨论之后，[gaearon](https://github.com/gaearon) 在 [2018年1月25日](https://github.com/facebook/react/issues/11527#issuecomment-360199710) 回答道：

这里是我的一些想法。从任何角度上说，这都不是一个完整的回应，但这仍然比什么都不说要好。

首先，我觉得我们都同意以下这一点：推迟协调(reconciliation)来实现批量更新是有益的。换句话说，我们同意如果 `setState()` 总是同步的重渲染，在很多情况下都是效率很低的。为此，如果我们知道我们很可能得到多个更新，我们再去批量更新它们，这样更好。

例如，如果我们在浏览器的 `click` 句柄(handler)中，`Child` 和 `Parent` 都调用了 `setState`，则我们不希望重新渲染 `Child` 两次，而是倾向于将它们标记为脏，然后在浏览器事件结束的时候重新渲染它们。

你正在问：为什么我们不能做同样的事情（批处理），而是选择把 `setState` 更新立即写入 `this.state`，而不等到协调结束。 我不认为有一个显而易见的答案（两种解决方案各有利弊），但是这是我可以想到的几个原因：

## 保证内部一致性

即使 `state` 是同步更新的，`props` 也不会是。 （再重新渲染父组件之前，你都不能得到 `props`，而如果你同步执行此操作，那么这一次批量更新将会在窗口之外。）

现在 React 提供的对象（state，props，refs）是具有内部一致性的。这意味着如果你只使用这些对象，它们将保证引用一个完全协调的树（即使它是该树的旧版本）。为什么这很重要？

如果你只使用 state，并且它是 _同步刷新_ 的（正如你所建议的），那么下列模式将是可行的：

``` js
console.log(this.state.value) // 0
this.setState({ value: this.state.value + 1 });
console.log(this.state.value) // 1
this.setState({ value: this.state.value + 1 });
console.log(this.state.value) // 2
```
然而，说这个 state 需要被提升到父类中，这样 state 可以在几个组件之间共享，于是你把它移到一个父类：

``` diff
-this.setState({ value: this.state.value + 1 });
+this.props.onIncrement(); // 在父类中做相同的事情
```
我想强调的是，在使用 `setState()` 的典型 React 应用程序中，这是 __React 特有的最常见的一种日常重构__ 。

> 译注：这也是我宣扬尽量多的把 state 写到 redux 里的原因之一

然而，这打破了我们的代码！

``` js
console.log(this.props.value) // 0
this.props.onIncrement();
console.log(this.props.value) // 0
this.props.onIncrement();
console.log(this.props.value) // 0
```

这是因为，在你提出的模型中，`this.state` 会立即刷新，但是 `this.props` 不会。并且如果我们不重新渲染父级元素，那么我们不能做到立即刷新 `this.props` ，这意味着我们不得不放弃批量更新（根据具体情况可能会显著降低性能）。

还有更微妙的例子可以解释这个问题。如果你把 `props`（尚未刷新）和 `state`（正如你的建议立即刷新）的数据混合起来，创建一个新的状态：[#122 (评论)](https://github.com/facebook/react/issues/122#issuecomment-81856416)，`refs` 也会有相同的问题：[#122 (评论)](https://github.com/facebook/react/issues/122#issuecomment-22659651)

__这些例子不是理论上的，__ 一点都不。事实上，React Redux 绑定(binding)过去常常就会遇到一模一样的这种问题，因为他们将 React props 和 non-React state 混合在一起：reactjs/react-redux#86, reactjs/react-redux#99, reactjs/react-redux#292, reactjs/redux#1415, reactjs/react-redux#525

> 译注：gaearon 拍出各种 issue 来佐证自己的观点

我不知道为什么 MobX 用户没有碰到这个，但我的直觉是，他们可能碰到了这种情况，却认为这是他们自己的错。或者，他们可能不会从 `props` 上读取尽可能多的东西，而是直接从 MobX 可变对象中读取。

那么，React 如何解决这个问题？ __在 React 中，`this.state` 和 `this.props` 只有在协调和刷新(flushing)之后才会更新，所以在重构之前和之后都会看到 0。__ 这保证了提升 state 的行为是安全的。

是的，在某些情况下这可能是不方便的。特别是对于来自有更多 OO 背景（面向对象）的朋友们，他们只是想多次改变状态，而不是考虑如何在一个地方完整的表达一次 _状态更新_ 。我可以感同身受，尽管我认为从调试的角度来看，保持状态更新集中会让代码更清晰：[#122 (评论)](https://github.com/facebook/react/issues/122#issuecomment-19888472)

不过，您仍然可以选择将 _想要_ 立即读取的状态移动到某个额外的(sideways)可变对象中，特别是，如果您不将其用于渲染的情况下。这正是 MobX 所能够做到的事情 🙂。

你还有另一个选择，如果你十分确定你在做什么，你可以 _刷新整个树_ ，这个 API 被称为 `ReactDOM.flushSync(fn)` 。我们应该还没有把它写到文档里，但我们肯定会在 16.x 发布周期的某个时候这样做。请注意，它实际上会强制完成重新渲染，以便在调用中发生更新，所以您应该非常谨慎地使用它。这个方法不会破坏 `props` ，`state` 和 `refs` 之间内在一致性的保证。

总而言之，__React 模型并不总是会引出最简洁的代码，但是它在内部是一致的，并且确保提升 state 是安全的。__

## 使并发更新变得可行

从概念上讲，React 表现的就好像每个组件有一个更新队列一样。这就是这个讨论为什么有意义的原因：我们讨论是否立即将更新应用到 `this.state`，因为我们毫不怀疑更新将按照确切的顺序被应用。然而情况并非你所想的那样（[哈哈](https://mobile.twitter.com/dan_abramov/status/956180994591285249)）。

> 译注：gaearon 之前发 twitter 说一直幻想有一天，在一个 RFC 当中很欠揍的说一句：然而情况并非你所想的那样。这里他引用了那条 twitter

最近，我们一直在谈论“异步渲染”。我承认，我们在这个概念到底是什么意思这方面沟通的还不够好，但是这就是研发的本质：你追求的是一个概念上看起来很光明很有希望的理念，但是只有花费了足够的时间，你才能真正理解它的含义。

过去我们解释“异步渲染”的一种方式是说：__React 可以根据 `setState()` 来自的不同的位置：事件句柄，网络响应，动画等，来为它们分配不同的优先级。__

例如，如果您正在输入消息，那么 TextBox 组件中的 `setState()` 调用需要立即被刷新。但是，如果 _恰好在输入时_ 收到了新的消息(message)，那么最好但做法是将新的 `MessageBubble` 延迟到某个固定的阈值(threshold)后（例如一秒）再进行渲染，这样就不会由于线程阻塞而导致输入被卡住。

如果我们让某些更新具有“低优先级”，我们可以将它们的渲染分割成几个毫秒的小块，这样它们就不会被用户注意到。

我知道这样的性能优化可能听起来不太令人兴奋或令人信服。你可以这样说：“如果我们使用MobX，就不需要这些了，我们的更新跟踪速度足够快，以避免重新渲染”。我不认为在所有情况下都是如此（例如，无论 MobX 有多快，你仍然需要创建 DOM 节点并为新安装(mount)的视图执行渲染）。就算在所有情况下都如此，如果你有意识地决定并且认为，总是把对象包装到一个特定的可以跟踪读写的 JavaScript 库中是可以被接受的，你从这些优化中获得的好处很可能没有你想象的那么多。

__但异步渲染不仅仅和性能优化有关。我们认为这是 React 组件模型可以做的一个根本转变。__

例如，考虑从一个屏幕导航到另一个屏幕的情况。通常，当新的屏幕正在渲染时，你会看到一个不停转动的圈(spinner)。

但是，就算导航足够快（一秒钟左右），闪烁并立即隐藏 spinner 也会导致用户体验下降。更糟糕的是，如果你有多个具有不同异步依赖关系的组件（数据，代码，图像），你最终会得到一个不停短暂闪烁的 spinner 的级联。这在视觉上是不愉快的，并且，因为所有的 DOM 回流了，你的应用事实上变得更慢了。这也是许多样板代码的来源。

这难道不够赞吗？当你简单地调用 `setState()` 渲染一个不同的视图，我们就可以“开始在后台”渲染更新后的视图了。想象一下，__你自己没有任何编写任何用来协调这件事情地代码，你可以选择在更新超过一定的阈值（如一秒）的情况下显示一个 spinner，或者在整个新的子树的异步依赖被满足时让 React 执行无缝的过渡。__ 而且，当我们“等待”的时候，“旧屏幕” 依旧可以保持互动（这种情况下，你可以选择其他的跳转），而 React 会最终强制：如果这个过程花费太长时间，你必须显示一个 spinner。

事实证明，用目前的 React 模型和 [一些生命周期的调整](https://github.com/reactjs/rfcs/pull/6)，我们实际上可以实现这个！[@acdlite](https://github.com/acdlite) 在过去的几个星期里一直在 [研发这个功能](https://mobile.twitter.com/acdlite/status/954585343839256577)，并且很快就会发布一个 [RFC](https://github.com/reactjs/rfcs)。

> 译注：acdlite 在 twitter 上发了一张特别搞笑的图，来说明他对 spinner 的痛恨

请注意，只有当 `this.state` 不会立即刷新，这才是可能的。如果它是立即刷新的，我们将无法开始在后台渲染视图的“新版本”的同时保持“旧版本”仍然可见并且互动。他们独立的状态更新会冲突。

我不想从 [@acdlite](https://github.com/acdlite) 那里窃取雷声，但是我希望这听起来至少有那么一点令人兴奋。我知道这听起来仍然很虚无缥缈。但我希望在接下来的几个月里我们能说服你，到时候你会体会到 React 模型的灵活性。并且据我所知，至少在一定程度上，这种灵活性是由于 _不_ 立即刷新状态更新才变得可能的。
