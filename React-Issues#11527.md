## 征求澄清：为什么 `setState` 是异步的？

> BY 张振衣([dancerphil@github](https://github.com/dancerphil/trick/))
>
> [原文](https://github.com/facebook/react/issues/11527)
>
> 除非另行注明，页面上所有内容采用 [MIT](https://opensource.org/licenses/MIT) 协议共享。

[mweststrate](https://github.com/mweststrate) 在 [2017年11月11日](https://github.com/facebook/react/issues/11527#issue-273131299) 评论道：

很长一段时间以来，我试着去理解为什么 `setState` 是异步的。在努力找寻答案的尝试失败之后，我个人得出的结论是，这是历史原因并且在现在这个时间可能很难改变它。不过 @gaearon 坚持说这是有明确理由的，所以我很好奇这个理由到底是什么 :)

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

---

我毫不怀疑，React 团队早已经意识到了 `setState` 的异步性质所经常引入的混淆，所以我猜肯定有另一个非常好的理由来支持当前的语义。 告诉我更多 :)
