# 入门

可以通过阅读下面的内容快速入门 Refast：
- [联接 React 组件](#联接-React-组件)
- [管理组件状态](#管理组件状态)
- [中间件 Middleware](#中间件-middleware)
- [LogicRender](#logicrender)

## 联接 React 组件
假定我们的目录结构如下：

```
demo  ----------------------- 某一个页面
  ├── index.js  ------------- 入口文件
  ├── logic.js  ------------- Logic
  ├── PageDemo.jsx   -------- View, React 组件
  └── PageDemo.less  -------- css 样式
```

那么将 PageDemo.jsx 和 logic.js 联接起来，只需要很简单的一个操作:

```jsx
// 使用 Refast 的 Component 替代 React 的 Component
import { Component } from 'refast';

// 引入 logic.js
import logic from './logic';

class PageDemo extends Component {

  constructor(props) {
    // 通过 super 绑定 logic
    super(props, logic);
  }
}
```

当然也可以传入 Logic 数组，以绑定多个 Logic:

```jsx
class PageDemo extends Component {

  constructor(props) {
    // 可以绑定一个 Logic 数组
    super(props, [logic1, logic2, ...]);
  }
}
```

## 管理组件状态

当 Logic 与组件联接后，即可通过 dispatch 方法调用 Logic 中的 Action 来修改组件的状态。

```javascript
// logic.js
export default update(ctx, someState) {
  // ...
  ctx.setState(someState);
}

// PageDemo.jsx
// 可以通过 dispatch 给方法传参（可选）
this.dispatch('update', data);

```
被 dispatch 的 Action，其第一个参数始终是 context (下简称 ctx)。 此外，ctx 还封装了以下几个通用方法：

- `ctx.setState` 设置组件的 state, 用法与组件的 `setState` 相同
- `ctx.getState` 获取组件当前的 state
- `ctx.getProps` 获取组件当前的 props

dispatch 调用 Action 时传入的参数，会依次放入到 ctx 后面。

更多内容请阅读[这里](Logic.md)


## 中间件 Middleware

Refast 支持自定义一个中间件，在组件 setState 之前，进行数据比对、条件执行、打印日志等特定的操作。

中间件的定制和用法请看[这里](Middleware.md)

## LogicRender

LogicRender 是一个可以嵌套使用的逻辑组件，提供以下功能：

- LogicRender 集成了通用视图处理逻辑，提升开发效率
- LogicRender 可根据条件自动执行脚本，减少逻辑处理

更多内容请看[这里](LogicRender.md)