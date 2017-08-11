# 常见问题

#### 1、之前的 setup 和 execute 在文档里没找到，是不是不支持了？

从 1.1.0 开始，setup 增加了一个别名 use，execute 增加了别名 dispatch。用法和效果是一样的。之所以增加别名，是为了方便有 Reflux 经验 和 Redux 经验的同学能够快速使用 Refast。


#### 2、Action 的互相调用是怎样的？
详见 [Logic](Logic.md) 相关部分。

#### 3、IE9/10 下 LogicRender 报错 `childContextTypes must be defined in order to use getChildContext` 

`LogicRender` 使用了 React 的 context 特性来实现 `Action` 调用的。 因此继承 Refast.Component 的时候，需要能够继承其 childContextTypes。而在这一点上，Babel 转换静态变量继承所需要的`__proto__`   IE 9/10 不支持。导致在 IE9/10 下报错：

![logicrender-ie9](https://user-images.githubusercontent.com/7709602/27169337-05eace2a-51dc-11e7-849f-94719d835a34.jpg)

解决的办法也很简单，需要你开启 es2015 的 loose 模式，同时加入 babel-plugin-transform-proto-to-assign 来解决这个问题。

一个可供参考的 `.babelrc` 文件内容如下：
```json
{
  "presets": [
    [
      "es2015",
      {
        "loose": true
      }
    ],
    "react",
    "stage-1"
  ],
  "plugins": [
    "add-module-exports",
    "transform-proto-to-assign"
  ]
}
```

可以看[这里](https://babeljs.io/docs/usage/caveats/#internet-explorer)了解更多内容。


如果你使用 [nowa-webpack](https://nowa-webpack.github.io)作为项目的脚手架，请在 `abc.json` 中的 `options` 处额外配置：
```js
{
  options: {
      "lazyload": true,
      "loose": true,
      // ...
  }
  // ...
}
```

