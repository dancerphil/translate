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

# js, react 代码规范

[JavaScript 代码风格指南](https://github.com/dancerphil/trick/blob/master/README.md)

[React/JSX 代码风格指南](https://github.com/dancerphil/trick/blob/master/React.md)

# MVVM

[http://slides.com/dancerphil/deck-4/live#/](http://slides.com/dancerphil/deck-4/live#/)

# 单向数据流

# redux middleware

[Middleware | Redux 中文文档](http://cn.redux.js.org/docs/advanced/Middleware.html)

# react-saga

[Redux-saga 中文文档](http://leonshi.com/redux-saga-in-chinese/)

[.](https://www.zhihu.com/question/22689579/answer/22318058)
