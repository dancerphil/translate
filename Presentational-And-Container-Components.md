# 展示组件和容器组件

> 节选自 [Dan Abramov 2015/05/23 的文章](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)

在写 React 应用时，我发现了一个简单而有用的模式。如果你已经使用了一段时间的 React，你可能已经发现了它。[这篇文章](https://medium.com/@learnreact/container-components-c0e67432e005)解释得很好，但我想补充几点。

如果将组件分为两类，你会发现组件更易于重用和处理。我称之为容器组件和展示组件，但我也听说过 Fat/Skinny，Smart/Dumb，Stateful/Pure，Screens/Components 等等。这些都不完全相同，但核心思想是相似的。

展示组件：

- 关注事物的外观。

- 可以在里面包含展示组件和容器组件，并且通常有一些自己的 DOM 标记和样式。

- 有时会允许 this.props.children。

- 不依赖于应用的其余部分，例如 Flux actions 或 stores。

- 不要指定数据的 load 方式或 mutate 方式。（注：通常已经处理过了）

- 仅通过 props 接收数据和回调函数。

- 很少有他们自己的状态（即使有，也是 UI 状态而不是数据）。

- 通常写成函数式组件，除非它们需要状态，生命周期钩子或性能优化。

- 示例：Page, Sidebar, Story, UserInfo, List。

容器组件：

- 关心事情是如何运作的。

- 可以在里面包含展示组件和容器组件，但通常没有自己的 DOM 标记，除了一些包装 div，并且从不具有任何样式。（注：随着 react@16，可能是 Fragment）

- 向展示组件或其他容器组件提供数据和行为。

- 调用 Flux actions 并将其作为回调提供给展示组件。

- 通常是有状态的，因为它们往往充当数据源。

- 通常使用高阶组件生成，例如来自 React Redux 的 connect()，来自 Relay 的 createContainer() 或来自 Flux Utils 的 Container.create()，而不是手工编写。

- 示例：UserPage, FollowersSidebar, StoryContainer, FollowedUserList。

我把它们放在不同的文件夹中，以区分这种区别。

## 这种方法的好处

- 更好地分离关注点。通过这种方式编写组件，你可以更好地理解你的应用和 UI。

- 更好的可重用性。你可以将完全不同的状态传给相同的展示组件，并将它们转换为可以进一步重用的单独容器组件。

- 演示组件基本上是你的应用程序的“调色板”。你可以将它们放在一个页面上，让设计师调整所有变体，而无需触及应用程序的逻辑。你可以在该页面上运行屏幕截图回归测试。

- 这会强制你提取“布局组件”，例如补充工具栏，页面，ContextMenu，并使用 this.props.children 而不是在多个容器组件中复制相同的标记(markup)和布局。

请记住，组件不必触发(emit) DOM。他们只需要在不同的 UI 关注点之间提供组合边界（注：原文 composition boundaries，我认为指逻辑块的分割组合和布局）。

好好利用这些优势。
