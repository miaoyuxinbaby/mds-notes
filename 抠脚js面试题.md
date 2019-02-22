# 抠脚js面试题

## 找出一个数组中有没有 -0

```js
funtion a (arr) {
  return arr.some(_ => 1 / _ === -Infinity)
}

// 也可以用Object.is
```

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

从输入URL到看到页面发生的全过程，越详细越好。

DNS查询、IP寻址、等待资源响应、解析下载的内容、css解析和dom树的绘制，解析过程中遇到要下载的资源就异步下载并解析（css、js、图片之类）、dom树绘制完毕后开始计算每个节点的位置，进行绘制，css解析和dom树绘制是异步的，所以有时候会看见页面加载，视图有一些细微的变化。

请简单实现双向数据绑定mvvm。

计算元素的宽高，可以先插入body,获取之后再删掉

前端压缩图片。
context.drawImage(img, dx, dy, dWidth, dHeight);
toBlob
然后new File 上传

getBoundingClientRect   left、right、top、bottom

```js
function father () {
  this.arr = [1,2]
}
function son () {}

// 原型链继承 缺点：父类中属性如果是引用类型，引用属性会被所有实例共享,
son.prototype = new father()

// test代码
var a = new son()
var b = new father()

a.arr.push(3)
console.log(a.arr)
console.log(b.arr)
// a和b的arr都变了

```

```js
// 借用构造函数继承 相当于把父类的代码复制到子类里面执行一遍 这样做的另一个好处就是可以给父类传参
function father () {
  this.arr = [1,2]
}
function son () {
  father.call(this)
}

// 借用构造函数解决了引用类型被所有实例共享的问题,但唯独函数没有被共享，而是copy了一份

```

继承方式	继承核心代码	优缺点
原型链继承	Student.prototype = new Person()	实例的引用类型共享
构造函数继承	在子类(Student)里执行 Person.call(this)	实例的引用类型不共享

原型链继承 和 构造函数继承 这两种继承方式的优缺点刚好是互相矛盾的

```js
// 组合继承 组合继承,就是各取上面2种继承的长处,普通属性 使用 构造函数继承,函数 使用 原型链继承,
// 既在子类中执行父类，又原型链继承父类
// 缺点 父类构造函数里面的代码会声明2遍
```

```js
// 原型式继承 有和原型链继承一样的毛病，区别是proto不一定要是构造函数，相当于浅拷贝了对象
// ECMAScript 5 通过新增 Object.create()方法规范化了原型式继承。
function clone (proto) {
    function F () {}
    F.prototype = proto
    return new F()
}
```

寄生组合式继承是引用类型最完美的继承 组合继承 和 寄生式 的结合

 ES6继承的结果和寄生组合继承相似，本质上，ES6继承是一种语法糖。但是，寄生组合继承是先创建子类实例this对象，然后再对其增强；而ES6先将父类实例对象的属性和方法，加到this上面（所以必须先调用super方法），然后再用子类的构造函数修改this。

- 进程和线程区别是什么
 进程是分配内存的最小单位，线程是CPU调度的最小单位，进程可以包含多个线程。

- 关于性能优化
  
减少一些不必要的http请求，比如10k以下的小文件。css和js， 合并后分成200k以下的小文件，一个域名下放3-4个，分多个域名放，有利于浏览器并行下载。因为浏览器对同一域名下的资源有并发上限，一般是4-6

小图标如果多的话，就用雪碧图，少的话，转成base64，页面上图片多可以懒加载，进入到视口再请求资源，

- CDN的原理是啥？

首先，浏览器会先请求CDN域名，CDN域名服务器会给浏览器返回指定域名的CNAME，浏览器在对这些CNAME进行解析，得到CDN缓存服务器的IP地址，浏览器在去请求缓存服务器，CDN缓存服务器根据内部专用的DNS解析得到实际IP，然后缓存服务器会向实际IP地址请求资源，缓存服务器得到资源后进行本地保存和返回给浏览器客户端。