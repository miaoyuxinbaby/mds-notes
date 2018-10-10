# 杂记

~[number] === -[number] - 1 eg: ~8 === -9

&& 优先级高于 ||  

真正会导致上下行解析出问题的 token 有 5 个：括号，方括号，正则开头的斜杠，加号，减号 。这些需要在行首加上分号

null 是基本类型中唯一的一个“假值”，typeof null === 'object'，所以检测null的类型需要：(!a && typeof a === "object")

> typeof function a(){ /* .. */ } === "function"; // true  function是object的一个子类型
  >> 具体来说，函数是“可调用对象”，它有一个内部属性 [[Call]]，该属性使其可以被调用
  >> 函数不仅是对象，还可以拥有属性
    >>> 函数的length属性将返回没有指定默认值的参数个数，rest 参数也不会计入length属性。

数组也是object的一个子类型

暂时性死区 也意味着typeof不再是一个百分之百安全的操作。

> 通过变量进行条件判断时可能报错

```js
// 这样会抛出错误
if (DEBUG) {
console.log( "Debugging is starting" );
}
// 这样是安全的
if (typeof DEBUG !== "undefined") {
console.log( "Debugging is starting" );
}
```

变量没有类型，但它们持有的值有类型。类型定义了值的行为特征。 -- 变量是标识符

使用 `delete` 运算符可以将单元从数组中删除，但是单元删除后，数组的 length 属性并不会发生变化。

数组通过数字进行索引，但有趣的是它们也是对象，所以也可以包含字符串键值和属性（但这些并不计算在数组长度内）

如果字符串键值能够被强制类型转换为十进制数字的话，它就会被当作数字索引来处理。

```js
var a = [ ];
a["13"] = 42;
a.length; // 14
```

> JavaScript 中的字符串和字符数组并不是一回事，最多只是看上去相似而已。
 >> 字符串不可变是指字符串的成员函数不会改变其原始值，而是创建并返回一个新的字符串。而数组的成员函数都是在其原始值上进行操作

```js
var a = 'abc'
a[1] // b
```

> . 运算符需要给予特别注意，因为它是一个有效的数字字符，**会被优先识别为数字常量的一部分**，然后才是对象属性访问运算符

```js
// 无效语法：
42.toFixed( 3 ); // SyntaxError
// 下面的语法都有效：
(42).toFixed( 3 ); // "42.000"
0.42.toFixed( 3 ); // "0.420"
42..toFixed( 3 ); // "42.000
```

> 应该怎样来判断 0.1 + 0.2 和 0.3 是否相等
  >> 最常见的方法是设置一个误差范围值，通常称为“机器精度”（machine epsilon）， 对JavaScript 的数字来说，这个值通常是 2^-52 (2.220446049250313e-16)。
  >> Number.EPSILON

```js
function numbersCloseEnoughToEqual(n1,n2) {
  return Math.abs( n1 - n2 ) < Number.EPSILON;
}
var a = 0.1 + 0.2;
var b = 0.3;
numbersCloseEnoughToEqual( a, b ); // true
numbersCloseEnoughToEqual( 0.0000001, 0.0000002 ); // false
```

> 能够呈现的最大浮点数大约是 1.798e+308（这是一个相当大的数字），它定义在 `Number.MAX_VALUE` 中。
> 最小浮点数定义在 `Number.MIN_VALUE` 中，大约是 5e-324，它不是负数，但无限接近于 0 ！
> 能够被“安全”呈现的最大整数是 2^53 - 1，即 9007199254740991  `Number.MAX_SAFE_INTEGER`
> 最小整数就是负的最大整数 `Number.MIN_SAFE_INTEGER`
  >> -9007199254740991 共16位

- 要检测一个值是否是整数，可以使用 ES6 中的 `Number.isInteger` 方法

```js
Number.isInteger( 42 ); // true
Number.isInteger( 42.000 ); // true
Number.isInteger( 42.3 ); // false
```

- NaN 无效数值（not a number）属于number类型
- 1/0 === `Infinity` 无穷大
- 复合值——对象（包括数组和封装对象）和函数，
- js中，引用指向的是值本身而非变量，值传递是复制一份，函数的形参就是个赋值操作，具体是值传递还是引用看赋值的类型(变量是个标识符，值传递，传的是指针)

```js
var a = [1,2,3];
var b = a;
a; // [1,2,3]
b; // [1,2,3]
// 然后
b = [4,5,6];
a; // [1,2,3]
b; // [4,5,6]
// b=[4,5,6] 并不影响 a 指向值 [1,2,3]，除非 b 不是指向数组的引用，而是指向 a 的指针，但在 JavaScript 中不存在这种情况！
```

```js
// 函数参数就经常让人产生这样的困惑：
function foo(x) {
  x.push( 4 );
  x; // [1,2,3,4]
  // 然后
  x = [4,5,6];
  x.push( 7 );
  x; // [4,5,6,7]
}
var a = [1,2,3];
foo( a );
a; // 是[1,2,3,4]，不是[4,5,6,7]
// 我们向函数传递 a 的时候，实际是将引用 a 的一个复本赋值给 x，而 a 仍然指向 [1,2,3]。
// 在函数中我们可以通过引用 x 来更改数组的值（push(4) 之后变为 [1,2,3,4]）。
// 但 x = [4,5,6] 并不影响 a 的指向，所以 a 仍然指向 [1,2,3,4]。
```

- [1,2].length = 0 清空数组 **vue中不可用**
- 标量基本类型值是不可更改的（字符串和布尔也是如此）。如果一个数字对象的标量基本类型值是 2，那么该值就不能更改，除非创建一个包含新值的数字对象
- typeof +'3' // "number"  在 JavaScript 开源社区中，一元运算 + 被普遍认为是显式强制类型转换
- 我们常用下面的方法来获得当前的时间戳
  > var timestamp = +new Date();
- 可能会碰到 var timestamp = +new Date  仅用于 new fn()，对一般的函数调用 fn() 并不适用。有争议
- if (a.indexOf( "lo" ) >= 0) 之类的写法不太好，，称为“抽象渗漏”，意思是在代码中暴露了底层的实现细节，这里是指用-1 作为失败时的返回值，这些细节应该被屏蔽掉。
  > ~ 和 indexOf() 一起可以将结果强制类型转换（实际上仅仅是转换）为真 / 假值
  >> 我觉得，这条可以忽略

```js
var a = "Hello World";
~a.indexOf( "lo" ); // -4 <-- 真值!
if (~a.indexOf( "lo" )) { // true
// 找到匹配！
}
~a.indexOf( "ol" ); // 0 <-- 假值!
!~a.indexOf( "ol" ); // true
if (!~a.indexOf( "ol" )) { // true
// 没有找到匹配！
}
```

- 如果 indexOf(..) 返回 -1， ~ 将其转换为假值 0，其他情况一律转换为真值。
- ~~x 能将值截除为一个 32 位整数， x | 0 也可以
- 解析允许字符串中含有非数字字符，解析按从左到右的顺序，如果遇到非数字字符就停止。而转换不允许出现非数字字符，否则会失败并返回 NaN。
- 重写 `toString` 方法会有很多神奇的效果，模拟Java多态
- 在 if(..).. 这样的布尔值上下文中，如果没有使用 Boolean(..) 和 !!，就会自动隐式地进行 ToBoolean 转换。建议使用 Boolean(..) 和 !! 来进行显式转换以便让代码更清晰易读。

- && 和 || 运算符的返回值并不一定是布尔类型，而是两个操作数其中一个的值。短路操作由此而来
- if (a) { foo(); } 等于 a && foo()

- ==允许强制类型转换，=== 不允许

- 很多人不知道，语句都有一个结果值。在控制台中输入 var a = 42 会得到结果值 undefined，而非 42。
- 语法不允许我们获得语句的结果值并将其赋值给另一个变量
- 语句系列逗号运算符（statement-series comma operator）将多个独立的表达式语句串联成一个语句
- js中没有else if ，所谓的else if 其实是else 后只有一行语句的时候不需要大括号而已
- , 运算符的优先级比 = 低
- 尾调用不需要创建一个新的栈帧，而是可以重用已有的的栈帧。速度更快，也更节省内存

> Javascript采用的是忽略原则，，同名变量声明忽略，同名函数声明覆盖
> js变量声明规则

对于同名的变量声明，Javascript采用的是忽略原则，后声明的会被忽略，变量声明和赋值操作可以写在一起，但是只有声明会被提升，提升后变量的值默认为undefined，结果是在赋值操作执行前变量的值必为undefined

对于同名的函数声明，Javascript采用的是覆盖原则，先声明的会被覆盖，因为函数在声明时会指定函数的内容，所以同一作用域下一系列同名函数声明的最终结果是调用时函数的内容和最后一次函数声明相同

对于同名的函数声明和变量声明，采用的是忽略原则，由于在提升时函数声明会提升到变量声明之前，变量声明一定会被忽略，所以结果是函数声明有效

```js
function gete(a) {
  console.log(a)
  var a = 1;
  function a() {
    console.log(3)
  }
}
gete(2)
// 先提升fn,在提升变量，然后fn覆盖形参a，变量a被忽略。但是当赋值后，变量a覆盖fn
```

> 可执行代码分三种

1. 全局代码 页面加载的时候会执行
2. eval代码 eval('eval code')
3. 函数代码 

```js
  function () {
    function code    
  }
  new Function(arg1, ... argn,  function code)
```

> js类型判断 toString(value)

```js
  // arguments := [object Arguments]
  function a () {
    console.log(toString.call(arguments))
  }
  a()
  // isObjectLike
  function isObjectLike(value) {
    return typeof value == 'object' && value !== null
  }
  // isArrayLike
  function isArrayLike(value) {
    return value != null && typeof value != 'function' && value.length > -1
  }
  // dom元素 nodeType === 1 && isObjectLike
  // [object DataView]
  // [object Map]
  // [object Set]
  // [object WeakMap]
  // [object Object]
  // [object Promise]
  // [object Undefined]
  // [object Null]
  // [object Boolean]
  // [object Number]
  // [object String]
  // [object Symbol]
  // [object Date]
  // [object RegExp]
  // [object Error] || [object DOMException]
  // [object Function] || [object AsyncFunction] || [object GeneratorFunction] || [object Proxy]
```

> js 中什么类型是引用传递, 什么类型是值传递? 如何将值类型的变量以引用的方式传递?

- emmm,初一看吓我一跳，如何将值类型的变量以引用的方式传递，我承认慌了，可能是思维定式，导致我忘了所谓的包装类型，毕竟平时用不到
- 通过将基础类型包装 (boxing) 可以以引用的方式传递

- 引用类型是在没有引用之后, 通过 v8 的 GC 自动回收, 值类型如果是处于闭包的情况下, 要等闭包没有引用才会被 GC 回收, 非闭包的情况下等待 v8 的新生代 (new space) 切换的时候回收.

- gc以前是引用计数，现在是标记清除（据说标记清除又升级了）

> Promise 中 .then 的第二参数与 .catch 有什么区别?
- then里的第二个参数，就近捕获的原则
- 全局异常，所有流程异常都捕获，所以如果粒度不是很细，那么处理起来很麻烦
- 封装是同步的，then的执行是异步

```js
let doSth = new Promise((resolve, reject) => {
  console.log('hello');
  resolve();
});

setTimeout(() => {
  doSth.then(() => {
    console.log('over');
  })
}, 10000);
```

```js
setTimeout(function() {
  console.log(1)
}, 0);
new Promise(function executor(resolve) {
  console.log(2);
  for( var i=0 ; i<10000 ; i++ ) {
    i == 9999 && resolve();
  }
  console.log(3);
}).then(function() {
  console.log(4);
});
console.log(5);
23541
定时器是宏队列 promise的then是微队列

console.log(1);

 setTimeout(() => {
   console.log('setTimeout');
 }, 0);

 let promise = new Promise(resolve => {
   console.log(3);
   resolve();
 }).then(data => {
   console.log(100);
 }).then(data => {
   console.log(200);
 });

 console.log(2);

 1 3 2 100 200 'set'

https://github.com/HcySunYang/vue-design/issues/130(有待修改)

> 总结！
> 事件循环肯定是从task开始的，但microtask优先级更高。2个marcoTask中间穿插着视图渲染
> 如果microtask请空前又加了microtask，那会直接在本次事件循环中执行，搭上末班车。
> process.nextTick注册的函数优先级高于Promise。 类似于插队（优先队列）
> 当执行栈中的任务清空，主线程会先检查微任务队列中是否有任务，如果有，就将微任务队列中的任务依次执行，直到微任务队列为空，之后再检查宏任务队列中是否有任务，如果有，则每次取出第一个宏任务加入到执行栈中，之后再清空执行栈，检查微任务，以此循环... ...
> **同一次事件循环中，新加入的微任务永远在宏任务之前执行（搭上顺风车）**。
```

- 优先队列

一般情况下，队列始终保持着先进先出的原则，但有些业务场景，并不一定要求先进来的要先出去，在出队的时候，要考虑队列中所有元素权重因子，优先级最高的元素最先出队，这种队列叫做优先队列。

- 常见宏队列
  - 定时器，延时器，网络请求，io操作，script，ui渲染，setImmediate（只有ie支持），MessageChannel
- 常见微队列
  - promise，process.nextTick，MutationObserver

- node里的emit是同步的

> url结构

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                    href                                     │
├──────────┬┬───────────┬─────────────────┬───────────────────────────┬───────┤
│ protocol ││   auth    │      host       │           path            │ hash  │
│          ││           ├──────────┬──────┼──────────┬────────────────┤       │
│          ││           │ hostname │ port │ pathname │     search     │       │
│          ││           │          │      │          ├─┬──────────────┤       │
│          ││           │          │      │          │ │    query     │       │
"  http:   // user:pass @ host.com : 8080   /p/a/t/h  ?  query=string   #hash "
│          ││           │          │      │          │ │              │       │
└──────────┴┴───────────┴──────────┴──────┴──────────┴─┴──────────────┴───────┘
```

- 在vue中，因为queueWatcher的存在，所以要优先选择微队列去更新状态，可以减少dom的重绘
- 遍历数组和对象的时候，可以考虑用while来（从后向前遍历）

```js
if (isA) {
  i = val.length
  while (i--) _traverse(val[i], seen)
} else {
  keys = Object.keys(val)
  i = keys.length
  while (i--) _traverse(val[keys[i]], seen)
}
```

- window.open('../balabala') 可以写相对路径
- 比如从接口里拿到后端的数据后，是一个对象，但是，我们又需要对这个对象添加一些需要用到的属性，这时候可以

```js
 let res = '后端返回的data'
 let data = {
   ...res,
   attr1: 巴拉巴拉,
   attr2: 巴拉巴拉
 }
```

> 判断数组的两种方法

```js
  // instanceof 不行，如果不是同一个window下的array，会返回false
  // 不考虑修改原型
  var a = []
  toString.call(a) // "[object Array]"
  Array.isArray(a) // true
```

> 判断number的方法

```js
  typeof

  toString

  if(a !== a  || +a === a) console.log('是number')
  // +symbol会报错。 要再加上对symbol的判断
```

> js词法作用域，作用域是变量（函数）声明时做决定的

```js
var z = 10;
function foo () {
  console.log(z)
}
function az () {
  var z = 20;
  
  foo();
}
az() // 10
```

> amd cmd umd

- amd 异步模块定义
  - AMD是RequireJS在推广过程中对模块定义的规范化产出，AMD是异步加载模块，推崇依赖前置。
- CMD 公共模块定义
  - 提到CMD，就不得不提起CommonJS，CommonJS是服务端模块的规范，由于Node.js被广泛认知。
  - 根据CommonJS规范，一个单独的文件就是一个模块。加载模块使用require方法，该方法读取一个文件并执行，最后返回文件内部的module.exports对象。
- UMD 通用模块定义
  - UMD是AMD和CommonJS的一个糅合。AMD是浏览器优先，异步加载；CommonJS是服务器优先，同步加载。

既然要通用，怎么办呢？那就先判断是否支持node.js的模块，存在就使用node.js；再判断是否支持AMD（define是否存在），存在则使用AMD的方式加载。这就是所谓的UMD。

> can we use __proto__?
export const hasProto = '__proto__' in {}

- string类型有trim方法，可以去除字符串两端的空白字符

- promise 如果前一个 then 方法的返回值是一个 Promise 实例，那么后一个 then 方法的回调函数会等待该 Promise 实例的状态改变后再执行

- call是一个一个值的传， apply是传数组

Json的2个方法组合深拷贝缺点，，，函数无法被拷贝下来，同时也无法拷贝 copyObj 对象原型链上的属性和方法。循环引用也不能

iview的input组件，明明暴露了input事件，但却没有写进文档里。

File.__proto 是Blob。说明，file继承了blob.

```js
// 验证 File 继承 Blob
var file = new File(["foo"], "foo.txt", {
  type: "text/plain",
});

file instanceof Blob    // true
```

FileList对象是一个类数组对象，拥有 length 属性，对象的每个元素都是一个 File 对象实例

按照 JSON 的规范，使用 JSON.stringify() 做对象序列化时，如果一个属性为函数或undefined，那这个属性就会被忽略。

如果属性为 null 则可以正常序列化这个属性:

遇到循环引用就GG

ES6模块的设计思想，是尽量的静态化，使得编译时就能确定模块的依赖关系(“静态优化”)，以及输入和输出的变量。CommonJS和AMD模块，都只能在运行时确定这些东西(“运行时加载”)。

ES6模块不是对象，而是通过export命令显式指定输出的代码，输入时也采用静态命令的形式。 
CommonJS和AMD模块是一个对象。

ES6模块加载的机制，与CommonJS模块完全不同。CommonJS模块输出的是一个值的拷贝，而ES6模块输出的是值的引用。

CommonJS模块输出的是被输出值的拷贝，也就是说，一旦输出一个值，模块内部的变化就影响不到这个值。ES6模块是动态引用，并且不会缓存值，模块里面的变量绑定其所在的模块。

处理“循环加载”的方法是不一样的，返回的结果也不一样。

## vue

vm._watchers 中存放的是实例属性的观察者

`newDepIds`、`newDeps` 以及 `depIds`、`deps`

这四个属性是用来避免收集重复的依赖的。

`newDepIds` 属性用来避免在 **一次求值** 的过程中收集重复的依赖，

`depIds` 属性是用来在 **多次求值** 中避免收集重复依赖的。

每一次求值之后 `newDepIds` 属性都会被清空，也就是说每次重新求值的时候对于观察者实例对象来讲 `newDepIds` 属性始终是全新的

vm.notify()触发响应  声明在Dep类中

Dep类的subs属性，存的是实例的观察者对象，notify()就是遍历subs的成员，并逐个调用观察者对象的 `update` 方法

vue 同值不触发更新, 新旧值对比

:numberArray="editingArray" @update:numberArray="val => editingArray = val"

子组件里this.$emit('update:numberArray',this.system_number_array)

