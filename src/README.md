# Refast

<a href="https://github.com/recore/refast" align="center"><img src="https://user-images.githubusercontent.com/7709602/27905492-afbe494e-6272-11e7-9ca2-7ecea4bd4c0b.jpg" alt="refast logo" height="120" align="right" /></a>

_[![DingTalk](https://img.alicdn.com/tps/TB1Wk4nOXXXXXXTaXXXXXXXXXXX-13-16.png)](dingtalk://dingtalkclient/action/sendmsg?dingtalk_id=alidou)5分钟就能学会的 React 组件状态管理工具_

[![npm version](https://img.shields.io/npm/v/refast.svg?style=flat)](https://www.npmjs.com/package/refast) [![download](https://img.shields.io/npm/dm/refast.svg?style=flat)](https://www.npmjs.com/package/refast) [![Packagist](https://img.shields.io/packagist/l/doctrine/orm.svg)](https://github.com/recore/refast) [![Refast](https://img.shields.io/github/stars/recore/refast.svg?style=social)](https://github.com/recore/refast) 

![npm install refast](https://nodei.co/npm/refast.png?downloadRank=true&downloads=true)

此文档适用于 Refast 2.x。如果你使用的是 Refast 1.x，请[移步这里](https://github.com/recore/refast-docs/blob/1.x/src/guide.md)，查看之前版本的文档。

## 优势

- 概念少，学习成本低，5 分钟上手精通。
- 异步操作更加流畅，官方推荐的 [async-await](https://github.com/tc39/proposals/blob/master/finished-proposals.md)，已经在 Chrome v55 和 Firefox 52.0 提供支持

## 安装使用
#### 通过 npm 安装
Refast 可以通过 npm 安装到项目中： 
`npm install refast --save`

#### 通过 CDN 引用

Refast 可以通过 CDN 引入到项目中。
* 每次均引入最新版本：
https://unpkg.com/refast/dist/index.js

* 引入指定版本。用版本号替换 `[version]` 部分的内容即可：
https://unpkg.com/refast@[version]/dist/index.js

## 设计思路

![Refast架构图](https://user-images.githubusercontent.com/7709602/27064069-df2fccb4-5027-11e7-967c-90791a1422c2.png)

#### 核心概念
##### Logic
Logic 是一些修改组件状态的函数(称之为 Action )集合。正如下面的例子这样，根据实际业务场景的需要，拆分出多个修改 Action：
```javascript
// 直接修改组件的状态
export function update(context, someState) {
  context.setState(someState);
}

// 异步请求后修改组件状态
export async function init(context, params){
  const asyncState = await window.fetch(params);
  context.setState(asyncState);
}
```

Action 函数有一个共同特点，他们的第一个参数都是 context。context 封装了一个修改组件状态的方法 setState。 `context.setState` 与 React 组件的 setState 用法完全一样。

更多内容可以看[这里](Logic.md)。

##### View
View 就是 React 的组件：
```javascript
// 某个页面 PageDemo.js
import { Component } from 'refast';
import * as logic from './logic';

export default class PageDemo extends Component {
  constructor(props) {
    super(props, logic);
  }
  render(){
    // ...
  }
}
```
Refast 对 React.Component 进行了一层封装。为其增加了 dispatch 等几个方法。可以通过在组件中使用 `this.dispatch` 调用 Logic 中的 Action 函数。

更多内容可以看[这里](guide.md)。

## 示例

具体示例在这里 [https://github.com/recore/refast-demo](https://github.com/recore/refast-demo)。线上效果可以看 [http://demo.refast.cn](http://demo.refast.cn)
