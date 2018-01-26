# LogicRender

LogicRender 是一个配合 Refast 使用的逻辑组件。可以嵌套使用，可以根据条件执行特定的 Action。如果你是用的是 Refast 1.x，请移步[这里](https://github.com/recore/refast-docs/blob/1.x/src/LogicRender.md)，查看之前版本的文档。

可以通过阅读下面的内容快速定位: 
- [安装](#安装)
- [初始化](#初始化)
- [功能](#功能)
  - [条件渲染](#条件渲染)
  - [执行 Action](#执行-action)
  - [通用 UI 的封装](#通用-ui-的封装)
  - [自定义展示方式](#自定义展示方式)
  - [修改默认的展示方式](#修改默认的展示方式)

## 安装
#### 通过 npm 安装
Refast 可以通过 npm 安装到项目中： 
`npm install refast-logic-render --save`

#### 通过 CDN 引用

Refast 可以通过 CDN 引入到项目中。
* 每次均引入最新版本：
https://unpkg.com/refast-logic-render/dist/index.js

* 引入指定版本。用版本号替换 `[version]` 部分的内容即可：
https://unpkg.com/refast-logic-render@[version]/dist/index.js

## 初始化

- 如果你使用了 Decorators

```javascript
import { Component } from 'refast';
import LogicRender, { connect } from 'refast-logic-render';

@connect
class Foo extends Component {
}

export default Foo;
```

- 如果没有使用 Decorators

```javascript
import { Component } from 'refast';
import LogicRender, { connect } from 'refast-logic-render';

class Foo extends Component {
}

export default connect(Foo);
```


## 功能

### 条件渲染

可以给 LogicRender 设置一个名为 isShow 的 props。 当其为真时，LogicRender 才会渲染其子组件。

```javascript
import { render } from 'react-dom';

render(){
  return (
    <LogicRender isShow={ this.state.show }>
    </LogicRender> 
  )
}
```

### 执行 Action

如果给 LogicRender 指定一个 Action。则在 LogicRender 在初始化(componentDidMount)后执行一次。如果又设置了 awareOf，则 awareOf 发生变化时再次执行 Action。

```javascript
import { render } from 'react-dom';

const { workNo } = this.state;

// 此处调用 action，与直接调用 this.dispatch('update) 效果一致;
render(){
  return (
    <LogicRender action={ 'update' } awareOf={{ workNo }}>
    </LogicRender> 
  )
}
```

### 通用 UI 的封装

LogicRender 封装了常用的 Empty/Loadig 等 UI 状态。

```jsx
import { render } from 'react-dom';
import { LogicRender } from 'refast';

// 如果 isLoading 为 true,就展示 loading 状态
// 如果 isEmpty 为 true，则展示 empty 状态
render(){
  const { isLoading, isEmpty } = this.state;
  return (
    <LogicRender isLoading={ isLoading } isEmpty={ isEmpty }>
    </LogicRender> 
  )
}
```
### 自定义展示方式
Empty/Loadig 的展示可以通过修改 LogicRender 的 props 来修改:

```jsx
import { render } from 'react-dom';
// 用以展示 empty 状态的 React 组件
import Empty from 'your-custom-empty';
// 用以展示 loading 状态的 React 组件
import Loading from 'your-custom-loading';

// 自定义样式
const cls = 'your-custom-className';

render(){
  return (
    <LogicRender
      className={ cls }
      Empty={ Empty }
      Loading={ Loading }
    />
  )
}
```

在修改的同时，LogicRender 会将自身的 props 传给自定义的组件。我们以修改 empty 状态为例，对 LogicRender 做如下修改：
 
```javascript
import { render } from 'react-dom';
const Empty = (props) => (
  <div className="refast-empty">
    {props.msg}
  </div>
);

render(){
  return (
    <LogicRender isEmpty={ this.state.isEmpty } msg={ 'No Data' } Empty={ Empty }>
    </LogicRender>
  )
}
```

当 isEmpty 为 true 时，就会展示 No Data。

### 修改默认的展示方式

如果要统一修改 LogicRender 的默认展示方式，可以像下面的例子这样直接修改 LogicRender.defaultProps。

```javascript
Object.assign(LogicRender.defaultProps, {
  Empty: Empty,
  Loading: Loading,
  className: cls,
});

```
