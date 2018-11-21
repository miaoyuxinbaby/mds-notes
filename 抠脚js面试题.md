# 抠脚js面试题

## this老生常谈

```js
var len = 10

function fn () {
  console.log(this.length)
}

var obj = {
  length: 5,
  methodName: function (fn) {
    fn() // 0
    // 这里调用时，this指向windows
    arguments[0]() // 2
    // 这里指向arguments
  }
}

obj.methodName(fn, 1)

```

## 找出一个数组中有没有 -0

```js
funtion a (arr) {
  return arr.some(_ => 1 / _ === -Infinity)
}

```

牛逼吧

也可以用Object.is

## 手写promise实现, 符合promise/A+规范
  地址：https://github.com/miaoyuxinbaby/mds-notes/blob/master/%E5%AE%9E%E7%8E%B0%E4%B8%80%E4%B8%AAPromise.md


- vue生命周期整理  从源码中整理的

```js

mergeOption

// 初始化生命周期相关功能
// 在vue的原型上挂载一些方法 $forceUpdate $destroy _update
// 声明一些跟生命周期有关的函数比如 callHook
// mountComponent  和 beforeMount beforeUpdate mounted 有关
initLifecycle(vm)
// 挂载事件相关的一些api 如 $on $once $emit $off 之类
initEvents(vm)
// 初始化render函数
initRender(vm)
// 触发beforeCreate钩子
callHook(vm, 'beforeCreate')
// inject相关
initInjections(vm) // resolve injections before data/props
// props -> methods -> data -> computed -> watch
initState(vm)
// provide相关
initProvide(vm) // resolve provide after data/props
callHook(vm, 'created')
```

要对一个方法加以封装（让他做一些额外的事情，）可以用一个变量缓存它，然后新的方法中执行它。 vue中类似的处理有数组的几个非变异方法的重写和$mount方法的二次覆盖


- 问什么浏览器要引入跨域这个机制；跨域请求到底有没有正常发出去并收到响应；是否了解跨域预检

 安全限制吧。 发出去并收到响应了，但是被浏览器拦截了。 提前发一个option请求

- virtual DOM 为什么会提出。
  真实的DOM，有很多的属性，非常庞大，频繁的去更新，会有性能问题
  virtual DOM就是用一个原生的js对象去模拟DOM。在VUE中就是 VNode 。 hashMap查询速度很快O(1)
  Vnode只是映射到真实dom。不需要那些dom的操作方法，所以很轻量

  Vnode -> create diff patch -> DOM

  _render() 返回一个渲染vNode ，再执行update做patch

  patch的整体流程 createComponent -> 子组件初始化 -> 子组件render -> 子组件patch

  mergeOption时， 遇到同样的钩子，合并成数组，执行的时候先父后子。对于不同的key,合并策略不同
  组件的合并是通过initInternalComponent

  vue父组件重新渲染的话子组件也会重新渲染 V3版本会改进这一点

浏览器对同一个域名（同源）的并行请求数是有限制的，一般是4-10。

在chrome 49+，Opera 36+，打开添加了rel=noopener的链接， window.opener 会为null。在老的浏览器中，可以使用 rel=noreferrer 禁用HTTP头部的Referer属性

```js
const logc = (s) => { console.log(s); };
logc(1);

new Promise((resolve) => {
  resolve(2)
}).then(v => { logc(v); });

async function fn1() {
  logc(3);
}

async function fn2() {
  logc(4);
  await fn1();
  logc(5);
}

fn2();
```

14325 // await之后的代码都相当于在then中 相当于 fn1().then(x => logc(5))