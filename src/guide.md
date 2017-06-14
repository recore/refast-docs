# 入门
可以通过阅读下面的内容快速入门 Refast：
- [联接 React 组件](#联接react组件)
- [管理组件状态](#更新组件的state)
- [LogicRender](#logicrender)
- [中间件](#中间件)

## 联接 React 组件
假定我们的目录结构如下：

```
demo  ----------------------- 某一个页面
  ├── index.js  ------------- 入口文件
  ├── logic.js  ------------- Logic
  ├── PageDemo.jsx   -------- View, React 组件
  └── PageDemo.less  -------- css 样式
```

那么可以将 PageDemo.jsx 和 logic.js 通过下面的方式联接起来:

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

如果想绑定多个 Logic ，可以通过下面的方式：

```jsx
class PageDemo extends Component {

  constructor(props) {
    // 可以绑定一个 Logic 数组
    super(props, [logic1, logic2, ...]);
  }
}
```

这样的话，我们就可以直接在 Logic 里面写 Action 操作了：

```javascript

export default update(ctx, someState) {
  // ...
  ctx.setState(someState);
}
```

## 管理组件状态

当 Logic 与 组件联接后，就可以通过组件的 dispatch 方法直接调用 Logic 中的 Action 了。

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

## LogicRender

LogicRender 是一个可以嵌套使用的逻辑组件，提供以下功能：

#### 执行 Action

可以给 LogicRender 指定一个 Action，初始化时执行一次。如果设置了 awareOf，在组件更新时，一旦 awareOf 发生变化，就会再次执行 Action.

```javascript
import { render } from 'react-dom';
const { workNo } = this.state;

// 此处调用 Action，与直接调用 this.dispatch('update) 效果一致;
render() {
  return (
    <LogicRender action={'update'} awareOf={{ workNo }}>
    </LogicRender> 
  )
}
```


#### 通用UI的封装

LogicRender 将常用的 Empty/Loadig UI 状态封装了起来。
```javascript
import { render } from 'react-dom';
import { LogicRender } from 'refast';

// 如果 isLoading 为 true,就展示 loading 状态
// 如果 isEmpty 为 true，则展示 empty 状态
render() {
  return (
    <LogicRender loading={ isLoading } empty={ isEmpty }>
    </LogicRender> 
  )
}
```
更多内容请看[这里](LogicRender.md)


## 中间件

支持自定义一个中间件，在组件 setState 之前，进行数据比对、条件执行、打印日志等特定的操作。

中间件的写法和用法请看[这里](Middleware.md)
