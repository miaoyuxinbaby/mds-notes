# react笔记

## react

纯展示类组件（或者是无内部状态组件），用function Component比较好。

pureComponent组件的shouldComponentUpdate不会直接返回true,而是会对属性和状态进行浅层比较

- 挂载

组件挂载会依次执行 constructor componentWillMount render componentDidMount（数据获取经常会放在这里）

- 更新

react更新一个组件有三种途径，父组件更新、自身状态变化、自身强制更新

父组件变化时，子组件需要重新渲染，会触发下面函数1-5
自身状态变化会触发 2-5
forceUpdate会调用3-5

1、componentWilReceiveProps 这个用来将新的props同步到state
2、shouldComponentUpdate  这个可以用来提升性能
3、componentWillUpdate
4、render
5、componentDidUpdate

- 卸载

componentWillUnmount 一般回调中做清理工作，定时器、事件之类的卸载（手动）

props被抽象为属性、state是状态

props有个特殊的属性 children 代表当前组件的子组件集合

defaultProps是默认值
protoTypes是props的类型验证 生产环境不会验证

setState也有一个异步更新队列，所以在setState之后读取state,可能会发现未更新。解决方法是缓存下更新的值。
或者添加第二个参数，一个回调，会在更新后执行

不要在render中 使用 setState。 因为setState会触发render,循环引用

在vue中跟ui无关的数据不放到data中，react也是一样的。

看起来事件是绑定在元素上，，其实是事件代理。

react event事件是经过封装的，解决了浏览器之间的差异，如果要访问原生的，可以 event.nativeEvent

jsx中传递列表索引给事件处理函数，可以bind,也可以用箭头函数包一层

关于组件通信

react,父子组件通信: 父传子,子的render中根据props自动变化，也可以调用子组件的方法
  子传父 父把回调函数传递给子，需要时调用，或者，用一个基类当做消息接口（消息中间件）。父订阅，子发布，项目中一般会用比较成熟的开源库
  爷孙（祖先和后辈） 可以用context传递数据，祖先和后代都要声明声明，后代再this.context调用，缺点是容易混乱和重名
  兄弟组件 状态提升
  任意组件 消息中间件 状态管理
vue, 父传子 props(可watch)，调子组件方法. 子传父$on $emit

## redux

redux可以理解为一个严格规定了使用模式的库

redux借鉴了函数式编程的思想，采用了单向数据流理念，为实现对数据状态的管理封装提供了不同的方法，并规定了我们进行管理的模式。以此来达到数据可预测，可追溯。

redux store getState subscribe dispatch reducer

开发者预先定义reducer和action函数，在恰当的时候派发action，即dispatch(action),如果所编写的reducer函数没有问题，那么在正常更新状态后可以通过store.subscribe注册更新后的回调

reducer要是一个纯函数

redux设计哲学

  1. 数据来源单一 store是唯一的 这区别于flux
  2. state是只读的，不能直接修改它
  3. 使用reducer函数来接收action

redux 提供了一套中间件机制，使开发者可以在派发任何一个action和执行reducer这两步之间，添加自定义扩展功能

- 网上的

JSX做表达式判断时候，需要强转为boolean类型

尽量不要在 componentWillReviceProps 里使用 setState，如果一定要使用，那么需要判断结束条件，不然会出现无限重渲染，导致页面崩溃。

给组件添加ref时候，尽量不要使用匿名函数，因为当组件更新的时候，匿名函数会被当做新的prop处理，让ref属性接受到新函数的时候，react内部会先清空ref，也就是会以null为回调参数先执行一次ref这个props，然后在以该组件的实例执行一次ref，所以用匿名函数做ref的时候，有的时候去ref赋值后的属性会取到null。