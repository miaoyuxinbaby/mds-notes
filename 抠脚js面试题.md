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