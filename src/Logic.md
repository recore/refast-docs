# 管理组件状态
#### 初始化组件的 state

``` javascript
// Refast 使用 logic.js 中 defaults 方法的返回值初始化组件的 state 
export default {
  // defaults 的参数 props 是组件初始化时的 props
  // defaults 函数返回的对象会用来初始化组件的 state
  defaults(props) {
    return {
      name: 'wenzhao',
      age: 18,
    }
  },
  ...
}
```

#### 修改组件的 state

当 Logic 与 组件联接后，就可以通过组件的 dispatch 方法直接调用 Logic 中的 Action 了。

```javascript
// PageDemo.jsx
// 可以通过 dispatch 给方法传参（也可以不传）
this.dispatch('update', someState);

// logic.js
export default { 
  ...
  update(ctx, someState) {
    ctx.setState(someState);
  },
}
```

被 dispatch 的 Action，其第一个参数始终是 context (下简称 ctx)。 此外，ctx 还封装了以下几个通用方法：

- `ctx.setState` 设置组件的 state, 用法与组件的 `setState` 相同
- `ctx.getState` 获取组件当前的 state
- `ctx.getProps` 获取组件当前的 props

在 1.1.0 之前，dispatch 方法有个别名 execute，用法与 dispatch 完全相同。后续版本推荐使用 dispatch。

#### 执行多个方法

Refast 支持一次 dispatch 多个 Action。它们继发执行，上一个的执行结果，将作为参数传入下一个方法。如果上一个 Action 的执行结果为 false, 则终止执行后续的 Action。

```javascript
// PageDemo.jsx
// 上一个方法的执行结果会作为参数，传入下一个方法
this.dispatch(['update', 'search'], data);

// logic.js
export default { 
  ...
  update(ctx, data) {
    // 返回的 obj 会作为 search 的参数
    return obj;
  },
 
  search(ctx, data, obj) {
    ctx.setState(data);
  },
}
```

#### 异步修改state

Refast 推荐使用 async/await 的方式管理异步请求。

```javascript
// logic.js
export default {
  ...
  async search(ctx, data) {
    // 可以通过 natty-fetch/window.fetch/$.ajax 等各种方式做异步请求的管理
    const state = await DB.User.search(data);
    // ...
    ctx.setState(state);
  }
}
```

如果要处理异常，可以通过 try-catch 捕获处理

```javascript
// logic.js
export default {
  // ...
  async search(ctx, data) {
    let state = {};
    try {
      state = await DB.User.search(data);
    } catch (error) {
      // 异步操作的异常信息会作为 catch 的参数传回来
      state = { hasError: true };
    }
    // ...
    ctx.setState(state);
  }
}
```
或者在 await 的时候处理 catch。

```javascript
// logic.js
export default {
  // ...
  await search(ctx, data) {
    let state =  await DB.User.search(data).catch(() => {
       // 这里处理异常信息
    });
    // ...
    ctx.setState(state);
  }
}
```

### 执行 Action 后的回调处理

从 1.2.0 开始， dispatch 方法将返回一个 Promise 的实例，以方便用户做进一步的操作。

比如，现在有一个表单查询的方法 handleSearch，需要执行一个称为  remoteSearch 的异步 Action，并在查询之后调用 afterSearch 做进一步的处理。在 1.2.0 之前，我们只能将 afterSearch 参数，在 disptach 时传入： 

```javascript
  handleSearch(someParams) {
    this.dispatch('remoteSearch', someParams, this.afterSearch);
  }
```

从 1.2.0 开始，Refast 开始支持如下的写法：

```javascript
 async handleSearch(someParams) {
  // dispatch 的结果会返回
  // 如果 dispatch 的是一个数组，则会返回最后一个的执行结果
  const result = await this.dispatch('remoteSearch', someParams);
  this.afterSearch(result);
 }
```

#### 扩展ctx

通过 Refast 提供的 use 可以很轻松地扩展 ctx。

```javascript
import Refast from 'Refast ';
import { Message } from 'your-custom-message';
import { Dialog } from 'your-custom-dialog';
import { DB } from 'your-custom-ajax-handler';

// 可以通过 use 自定义
Refast.use('fn', {
  message: Message,
  dialog: Dialog
})
```

这样，在我们就可以在 Action 的定义中使用了

```javascript
// logic.js
export default { 
  ...
  async search(ctx, data) {
    // ...
    try {
      // ...
      await ctx.fn.DB.User.search(data);
      // ...
      ctx.fn.message.success('员工信息获取成功');
    } catch(e) {
      ctx.fn.message.error(`员工信息获取不成功:${e.errorMsg}`);
    }
  },
}
```
在 1.1.0 之前，use 方法有个别名 setup，用法与 use 完全相同。后续版本推荐使用 use。

