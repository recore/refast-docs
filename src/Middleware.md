# 中间件

Refast 支持自定义一个中间件，帮助用户在组件 setState 之前，进行数据比对、条件执行、记录日志、数据回溯等特定的操作。

这里以记录日志的功能来说明中间件的写法和用法。记录日志的功能会帮我们在每次 state 更新时，都将新的 state 记录下来，使得 state 的更新历史清晰透明。

#### 自定义中间件

自定义中间件非常方便，可以直接通过 Refast.use 来完成。

```jsx
import Refast from 'refast';

const logState = ctx => next => (...args) => {
  console.log('######### PRINT STATE LOG #########');
  console.log('ctx:', ctx);
  console.log('args:', args);
  // 如果不执行 next， 则中止组件 state 更新
  next(...args);
};

// logState 可以是一个函数
// 也可以是一个函数组, 从前到后依次执行
Refast.use('middleware', logState);
``` 
![image](https://user-images.githubusercontent.com/7709602/27062879-cd5320a6-5020-11e7-86e2-65e36fc3c602.png)

#### 中间件的使用

中间件一旦使用 Refast.use 注册，就会在组件状态更新时按照指定的顺序从前向后依次执行。这里我们定义两个中间件， log1 和 log2 ：

```jsx
import Refast from 'refast';

const log1 = ctx => next => (...args) => {
  console.log('######### MIDDLEWARE: 1 #########');
  next(...args);
};

const log2 = ctx => next => (...args) => {
  console.log('######### MIDDLEWARE: 2 #########');
  next(...args);
};

``` 
如果顺序注册 log1 和 log2,则执行结果如下：
```javascript
Refast.use('middleware', [log1, log2]);
```
![image](https://user-images.githubusercontent.com/7709602/27063122-57ddda08-5022-11e7-8ef2-4ed96d53208c.png)


反之，如果顺序注册 log2 和 log1,则执行结果如下：

```javascript
Refast.use('middleware', [log2, log1]);
```
![image](https://user-images.githubusercontent.com/7709602/27063148-7a04d6fe-5022-11e7-938a-bc4e5ac7244b.png)
