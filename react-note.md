# react 笔记

## react

纯展示类组件（或者是无内部状态组件），用 function Component 比较好。

pureComponent 组件的 shouldComponentUpdate 不会直接返回 true,而是会对属性和状态进行浅层比较

- 挂载

组件挂载会依次执行 constructor componentWillMount render componentDidMount（数据获取经常会放在这里）

- 更新

react 更新一个组件有三种途径，父组件更新、自身状态变化、自身强制更新

父组件变化时，子组件需要重新渲染，会触发下面函数 1-5
自身状态变化会触发 2-5
forceUpdate 会调用 3-5

1、componentWilReceiveProps 这个用来将新的 props 同步到 state
2、shouldComponentUpdate 这个可以用来提升性能
3、componentWillUpdate
4、render
5、componentDidUpdate

- 卸载

componentWillUnmount 一般回调中做清理工作，定时器、事件之类的卸载（手动）

props 被抽象为属性、state 是状态

props 有个特殊的属性 children 代表当前组件的子组件集合

defaultProps 是默认值
protoTypes 是 props 的类型验证 生产环境不会验证

setState 也有一个异步更新队列，所以在 setState 之后读取 state,可能会发现未更新。解决方法是缓存下更新的值。
或者添加第二个参数，一个回调，会在更新后执行

不要在 render 中 使用 setState。 因为 setState 会触发 render,循环引用

看起来事件是绑定在元素上，，其实是事件代理。

react event 事件是经过封装的，解决了浏览器之间的差异，如果要访问原生的，可以 event.nativeEvent

关于组件通信

react,父子组件通信: 父传子,子的 render 中根据 props 自动变化，也可以调用子组件的方法
子传父 父把回调函数传递给子，需要时调用，或者，用一个基类当做消息接口（消息中间件）。父订阅，子发布，项目中一般会用比较成熟的开源库
爷孙（祖先和后辈） 可以用 context 传递数据，祖先和后代都要声明声明，后代再 this.context 调用，缺点是容易混乱和重名
兄弟组件 状态提升
任意组件 消息中间件 状态管理
vue, 父传子 props(可 watch)，调子组件方法. 子传父$on $emit

- 网上的

JSX 做表达式判断时候，需要强转为 boolean 类型

尽量不要在 componentWillReviceProps 里使用 setState，如果一定要使用，那么需要判断结束条件，不然会出现无限重渲染，导致页面崩溃。

给组件添加 ref 时候，尽量不要使用匿名函数，因为当组件更新的时候，匿名函数会被当做新的 prop 处理，让 ref 属性接受到新函数的时候，react 内部会先清空 ref，也就是会以 null 为回调参数先执行一次 ref 这个 props，然后在以该组件的实例执行一次 ref，所以用匿名函数做 ref 的时候，有的时候去 ref 赋值后的属性会取到 null。

## ssr 服务端渲染 同构

之前看哔哩哔哩首页，刷新页面发现有一部分仿佛没有销毁一样，直接就出现在了页面上，当时很惊讶，现在发现，应该是因为，首页是服务的渲染，采用的是流的方式。渐进式渲染，减少了 TTFB 时间

- renderToString 和 renderToStaticMarkUp

renderToString 生成的 HTML 字符串的每个 DOM 节点都有一个 data-react-id 属性。根节点会有一个 data-checksum 属性

renderToStaticMarkUp 则不会有。

上面是 16 版本之前的，16 版本 renderToString 不会有自定义属性，所以就无法判断是否需要重复渲染，reactDom 提供了一个新的 api，ReactDom.hydrate()

16 版本还提供了 renderToNodeStream 和 renderToStaticNodeStream 方法。

renderToNodeStream 方法持续产生字节流，返回 Readable stream. 最终通过流的形式返回 html 字符串。

原来是 res.send() 流的形式就是 res.write()

静态页面更适合 renderToStaticNodeStream。交互多的更适合 renderToNodeStream

- TTFB（Time To First Byte）

指从浏览器发起最初的网络请求，到从服务器接收到第一个字节的时间，它包含了 TCP 连接时间，发送 HTTP 请求的时间和获得响应消息的第一个字节的时间。

- DOMContentLoaded 是初始的 HTML 解析完成后调用的钩子 load 是所有资源加载完毕的钩子
- 如果 html 中包含脚本，则脚本会阻塞 html，加载完会继续解析 html。这也是 js 文件放到 body 最下面的原因之一

## next.js

零配置，但可以选择定制化配置。

pages 目录下可以放页面级组件，会自动映射成一个基于服务器的路由

关于获取数据，next.js 扩展了 React.Component 的 getInitialProps 方法。一般使用它来获取数据

```js
import React from 'react'
import 'isomorphic-fetch'
export default class extends React.Component {
  static async getInitialProps() {
    const res = await fetch('url')
    const data = await res.json()
    return { username: data.profile.username }
  }
}
```

## 代码复用

从 mixin 到 hoc 再到如今的 render prop。是代码复用的进步。抛弃 mixin 的契机是 class 方式声明的组件不支持 mixin。 hoc 也很流行。但未来应该是 render prop 的。给组建传递一个函数，在子组件中执行，获得对应的组件

如果列表项目的顺序可能会变化，我们不建议使用索引来用作键值，因为这样做会导致性能的负面影响，还可能引起组件状态问题。
