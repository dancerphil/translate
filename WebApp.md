# 工作中遇到的一些技巧

# material-ui muiTheme组件

muiTheme 入口：

```
const Main = () => (
  <MuiThemeProvider muiTheme={getMuiTheme(darkBaseTheme)}>
    <AppBar title="My AppBar" />
  </MuiThemeProvider>
);
```

自定义 muiTheme 组件

```
// First
muiTheme.myComponent = {
  textColor: 'red',
  backColor: 'green',
  other: 'whatever'
}

// Next
class myComponent extends React.Component {...}
export default muiThemeable()(myComponent);
```

现在你可以通过 `this.props.muiTheme` 来获取主题配置，尽量使用 `this.props.muiTheme.palette` 中的配置以保持 UI 的一致性。

[SEE](http://www.material-ui.com/#/customization/themes)

# react-route 获取页面来源信息

```
<RaisedButton
  label={'查看告警'}
/>

// 错误示范
<Link to={{pathname: '/app/alarm/', query: {siteID: this.props.siteID}}} >
  <RaisedButton
    label={'查看告警'}
  />
</Link>

// 正确示范
<RaisedButton
  label={'查看告警'}
  containerElement={<Link to={{pathname: '/app/alarm/', query: {siteID: this.props.siteID}}} />}
/>
```

而后可以在告警页面(/app/alarm/)获取query

```
this.props.location.query
> {siteID: '60'}
```
# position: relative | absolute

```
function getElementAbsPos(e) {
  var t = e.offsetTop;
  var l = e.offsetLeft;
  while(e = e.offsetParent) {
    t += e.offsetTop;
    l += e.offsetLeft;
  }
  return {left:l,top:t};
}
```

# 函数式

## 一个简单的例子：函数不作为过程

如果你想要打乱数组，那么你能写出的最优美的代码是这样的

```
arr.sort(() => (0.5 - Math.random()))
```

它接受一个函数作为参数，在这里函数并不是一个实时过程，而是一种数据的 mapping 方式。

## 一个终极例子：函数的第一性

在 3D 引擎中，一个点会以 `[x, y, z, w]` 来记录，在模型中有很多个点，构成了一个矩阵，现在我需要所有的点沿 Z 轴逆时针旋转（可能玩家转动了视角），求旋转后的矩阵。

我们会乘上一个旋转矩阵。

![](http://img.blog.csdn.net/20130814210510937)

这个旋转矩阵就可以抽象为一个函数 `rotate()`

```
camera = translate(rotate(scale(dots)))

// or we can combine transform
transform = translate(rotate(scale))
camera = transform(dots)
```

在这个例子中，数据和函数都是矩阵形式的表达，而且我们先把函数与函数相乘了，意味着在函数式编程中，函数与其他数据类型一样，处于平等地位（这部分内容需要简单的线性代数知识以便于理解）

## 没有对象，函数的纯洁

引用透明（Referential transparency），指的是函数的运行不依赖于外部变量或"状态"，只依赖于输入的参数，任何时候只要参数相同，引用函数所得到的返回值总是相同的。

副作用（side effect），指的是函数内部与外部互动（最典型的情况，就是修改全局变量的值），产生运算以外的其他结果。函数式编程强调没有"副作用"，意味着函数要保持独立，所有功能就是返回一个新的值，没有其他行为，尤其是不得修改外部变量的值。

所以第一，我们不应该的函数中依赖 `this.props` 或 `this.state`，this 是一个对象，我们拒绝，我们唯一接受的输入是函数参数。第二我们不应该在函数内修改任何值，我们唯一接受的输出是 return 值。

以 WebApp 中遇到的一个需求为例，在一个配电柜中有数个 device ，每个 device 有数个 realtimeData ，希望合并 realtimeData 的名称并去重，得到一个数组作为表格的标题栏。

```
getTableHeader(devices, indicatorTypes) {
  return _.uniqWith(
    _.concat(
      [{ key: 'name', name: '设备名称' }],
      _.flatten(
        devices.map(device => device.realtimeData.map(dataItem => ({
          key: dataItem.code,
          name: indicatorTypes[dataItem.code - 1].name
        }))))), _.isEqual);
}
```

# js, react 代码规范

[JavaScript 代码风格指南](https://github.com/dancerphil/trick/blob/master/README.md)

[React/JSX 代码风格指南](https://github.com/dancerphil/trick/blob/master/React.md)

# MVVM

[http://slides.com/dancerphil/deck-4/live#/](http://slides.com/dancerphil/deck-4/live#/)

# 单向数据流

![](https://raw.githubusercontent.com/dancerphil/dancerphil.github.com/master/img/redux-react.jpg)

# redux middleware

[Middleware | Redux 中文文档](http://cn.redux.js.org/docs/advanced/Middleware.html)

# react-saga

[Redux-saga 中文文档](http://leonshi.com/redux-saga-in-chinese/)

![](https://raw.githubusercontent.com/dancerphil/dancerphil.github.com/master/cat.gif)

[.](https://www.zhihu.com/question/22689579/answer/22318058)
