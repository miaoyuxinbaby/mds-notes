# es6
## 一、函数扩展

#### 尾调用
- 以下三种都不是尾调用
```
// 情况一
function f(x){
  let y = g(x);
  return y;
}

// 情况二
function f(x){
  return g(x) + 1;
}

// 情况三
function f(x){
  g(x);
}

```
- 上面代码中，情况一是调用函数g之后，还有赋值操作，所以不属于尾调用，即使语义完全一样。情况二也属于调用后还有操作，即使写在一行内。情况三等同于下面的代码。
```
function f(x){
  g(x);
  return undefined;
}
```

#### 尾调用优化总结
- 当funcA中执行funcB时，funcA的内存不会释放。如果funcB是尾调用的话就会释放funcA的内存
- 尾调用优化，释放父级函数的内存，每次执行时，保证调用帧只有一项。return nextFunc();
```
function f(x) {
  if (x > 0) {
    return m(x)
  }
  return n(x);
}
```
- 只有不再用到外层函数的内部变量，内层函数的调用帧才会取代外层函数的调用帧，否则就无法进行“尾调用优化”。
```
function addOne(a){
  var one = 1;
  function inner(b){
    return b + one;
  }
  return inner(a);
}
```
- 上面的函数不会进行尾调用优化，因为内层函数inner用到了外层函数addOne的内部变量one。

#### 尾递归
- 函数调用自身，称为递归。如果尾调用自身，就称为尾递归。

递归非常耗费内存，因为需要同时保存成千上百个调用帧，很容易发生“栈溢出”错误（stack overflow）。但对于尾递归来说，由于只存在一个调用帧，所以永远不会发生“栈溢出”错误。

```
function factorial(n) {
  if (n === 1) return 1;
  return n * factorial(n - 1);
}

factorial(5) // 120
```
- 上面代码是一个阶乘函数，计算n的阶乘，最多需要保存n个调用记录，复杂度 O(n) 。
- 如果改写成尾递归，只保留一个调用记录，复杂度 O(1) 。
```
function factorial(n, total) {
  if (n === 1) return total;
  return factorial(n - 1, n * total);
}

factorial(5, 1) // 120
```
- 函数式编程有一个概念，叫做柯里化（currying），意思是将多参数的函数转换成单参数的形式。
- ES6 的尾调用优化只在严格模式下开启，正常模式是无效的。

这是因为在正常模式下，函数内部有两个变量，可以跟踪函数的调用栈。

func.arguments：返回调用时函数的参数。
func.caller：返回调用当前函数的那个函数。
尾调用优化发生时，函数的调用栈会改写，因此上面两个变量就会失真。严格模式禁用这两个变量，所以尾调用模式仅在严格模式下生效。
```
function restricted() {
  'use strict';
  restricted.caller;    // 报错
  restricted.arguments; // 报错
}
restricted();
```

#### 函数参数的尾逗号 
- ES2017 允许函数的最后一个参数有尾逗号（trailing comma）。
这样的规定也使得，函数参数与数组和对象的尾逗号规则，保持一致了。

#### rest参数
- ES6 引入 rest 参数（形式为...变量名），用于获取函数的多余参数，这样就不需要使用arguments对象了。rest 参数搭配的变量是一个数组，该变量将多余的参数放入数组中。
- rest参数和函数的参数默认值都只能写在最后，否则报错

#### 箭头函数
- 箭头函数绑定This,函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。
  或者说本身没有This,他的this是上一层的this.相当于
```
// ES6
function foo() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100);
}

// ES5
function foo() {
  var _this = this;

  setTimeout(function () {
    console.log('id:', _this.id);
  }, 100);
}

```

## 二、数组扩展

##### 扩展运算符
- 扩展运算符（spread）是三个点（...）。它好比rest参数的逆运算，将一个数组转为用逗号分隔的参数序列。
- 该运算符主要用于函数调用。
```
console.log(...[1, 2, 3])
// 1 2 3

console.log(1, ...[2, 3, 4], 5)
// 1 2 3 4 5

[...document.querySelectorAll('div')]
// [<div>, <div>, <div>]


function push(array, ...items) {
  array.push(...items);
}

function add(x, y) {
  return x + y;
}

var numbers = [4, 38];
add(...numbers) // 42
```
- 扩展运算符很灵活。有如下应用
- 合并数组
```
// ES5
[1, 2].concat(more)
// ES6
[1, 2, ...more]

var arr1 = ['a', 'b'];
var arr2 = ['c'];
var arr3 = ['d', 'e'];

// ES5的合并数组
arr1.concat(arr2, arr3);
// [ 'a', 'b', 'c', 'd', 'e' ]

// ES6的合并数组
[...arr1, ...arr2, ...arr3]
// [ 'a', 'b', 'c', 'd', 'e' ]
```
- 与解构赋值结合
```
// ES5
a = list[0], rest = list.slice(1)
// ES6
[a, ...rest] = list
```
- 将字符串转为真正的数组。
```
[...'hello']
// [ "h", "e", "l", "l", "o" ]
```
- 实现了 Iterator 接口的对象，都可以用扩展运算符转为真正的数组
```
var nodeList = document.querySelectorAll('div');
var array = [...nodeList];
```
- 上面代码中，querySelectorAll方法返回的是一个nodeList对象。它不是数组，而是一个类似数组的对象。这时，扩展运算符可以将其转为真正的数组，原因就在于NodeList对象实现了 Iterator 。

##### Array.from()
- Array.from方法用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象（包括ES6新增的数据结构Set和Map）。
- 值得提醒的是，扩展运算符（...）也可以将某些数据结构转为数组。
- 扩展运算符背后调用的是遍历器接口（Symbol.iterator），如果一个对象没有部署这个接口，就无法转换。Array.from方法还支持类似数组的对象。所谓类似数组的对象，本质特征只有一点，即必须有length属性。因此，任何有length属性的对象，都可以通过Array.from方法转为数组，而此时扩展运算符就无法转换。
```
Array.from({ length: 3 });
// [ undefined, undefined, undefined ]
```
- 上面代码中，Array.from返回了一个具有三个成员的数组，每个位置的值都是undefined。扩展运算符转换不了这个对象。
- Array.from还可以接受第二个参数，作用类似于数组的map方法，用来对每个元素进行处理，将处理后的值放入返回的数组。
```
Array.from(arrayLike, x => x * x);
// 等同于
Array.from(arrayLike).map(x => x * x);

Array.from([1, 2, 3], (x) => x * x)
// [1, 4, 9]
```

##### Array.of()
- Array.of方法用于将一组值，转换为数组。
```
Array.of(3, 11, 8) // [3,11,8]
Array.of(3) // [3]
Array.of(3).length // 1
```
- 这个方法的主要目的，是弥补数组构造函数Array()的不足。因为参数个数的不同，会导致Array()的行为有差异。
```
Array() // []
Array(3) // [, , ,]
Array(3, 11, 8) // [3, 11, 8]
```
- 上面代码中，Array方法没有参数、一个参数、三个参数时，返回结果都不一样。只有当参数个数不少于2个时，Array()才会返回由参数组成的新数组。参数个数只有一个时，实际上是指定数组的长度。
- Array.of基本上可以用来替代Array()或new Array()，并且不存在由于参数不同而导致的重载。它的行为非常统一。
```
Array.of() // []
Array.of(undefined) // [undefined]
Array.of(1) // [1]
Array.of(1, 2) // [1, 2]
```
- Array.of总是返回参数值组成的数组。如果没有参数，就返回一个空数组。

##### 数组实例的 find() 和 findIndex() 
- 数组实例的find方法，用于找出第一个符合条件的数组成员。它的参数是一个回调函数，所有数组成员依次执行该回调函数，直到找出第一个返回值为true的成员，然后返回该成员。如果没有符合条件的成员，则返回undefined。
```
[1, 4, -5, 10].find((n) => n < 0)
// -5
```
- 上面代码找出数组中第一个小于0的成员。
```
[1, 5, 10, 15].find(function(value, index, arr) {
  return value > 9;
}) // 10
```
- 上面代码中，find方法的回调函数可以接受三个参数，依次为当前的值、当前的位置和原数组。
- 数组实例的findIndex方法的用法与find方法非常类似，返回第一个符合条件的数组成员的位置，如果所有成员都不符合条件，则返回-1。

##### 数组实例的fill()
- fill方法使用给定值，填充一个数组。
- fill方法用于空数组的初始化非常方便。数组中已有的元素，会被全部抹去。
- fill方法还可以接受第二个和第三个参数，用于指定填充的起始位置和结束位置。
```
['a', 'b', 'c'].fill(7, 1, 2)
// ['a', 7, 'c']
```
- 上面代码表示，fill方法从1号位开始，向原数组填充7，到2号位之前结束。

##### 数组实例的 entries()，keys() 和 values()
- ES6 提供三个新的方法——entries()，keys()和values()——用于遍历数组。它们都返回一个遍历器对象（详见《Iterator》一章），可以用for...of循环进行遍历，唯一的区别是keys()是对键名的遍历、values()是对键值的遍历，entries()是对键值对的遍历。
```
for (let index of ['a', 'b'].keys()) {
  console.log(index);
}
// 0
// 1

for (let elem of ['a', 'b'].values()) {
  console.log(elem);
}
// 'a'
// 'b'

for (let [index, elem] of ['a', 'b'].entries()) {
  console.log(index, elem);
}
// 0 "a"
// 1 "b"
```
- 如果不使用for...of循环，可以手动调用遍历器对象的next方法，进行遍历。
```
let letter = ['a', 'b', 'c'];
let entries = letter.entries();
console.log(entries.next().value); // [0, 'a']
console.log(entries.next().value); // [1, 'b']
console.log(entries.next().value); // [2, 'c']
```

##### 数组实例的 includes()
- Array.prototype.includes方法返回一个布尔值，表示某个数组是否包含给定的值，与字符串的includes方法类似。ES2016 引入了该方法。
```
[1, 2, 3].includes(2)     // true
[1, 2, 3].includes(4)     // false
[1, 2, NaN].includes(NaN) // true
```
- 该方法的第二个参数表示搜索的起始位置，默认为0。如果第二个参数为负数，则表示倒数的位置，如果这时它大于数组长度（比如第二个参数为-4，但数组长度为3），则会重置为从0开始
- 另外，Map 和 Set 数据结构有一个has方法，需要注意与includes区分。
- Map 结构的has方法，是用来查找键名的，比如Map.prototype.has(key)、WeakMap.prototype.has(key)、Reflect.has(target, propertyKey)。
- Set 结构的has方法，是用来查找值的，比如Set.prototype.has(value)、WeakSet.prototype.has(value)。

##### 数组的空位
- 数组的空位指，数组的某一个位置没有任何值。比如，Array构造函数返回的数组都是空位。
```
Array(3) // [, , ,]
```
- 上面代码中，Array(3)返回一个具有3个空位的数组。
注意，空位不是undefined，一个位置的值等于undefined，依然是有值的。空位是没有任何值，in运算符可以说明这一点。
```
0 in [undefined, undefined, undefined] // true
0 in [, , ,] // false
```
- 上面代码说明，第一个数组的0号位置是有值的，第二个数组的0号位置没有值。
- ES5 对空位的处理，已经很不一致了，大多数情况下会忽略空位。
    1、 forEach(), filter(), every() 和some()都会跳过空位。
    2、 map()会跳过空位，但会保留这个值
    3、 join()和toString()会将空位视为undefined，而undefined和null会被处理成空字符串。
- ES6 则是明确将空位转为undefined。
- 由于空位的处理规则非常不统一，所以建议避免出现空位。

## 三、 对象的扩展

##### 属性的简洁表示法 
- ES6 允许直接写入变量和函数，作为对象的属性和方法。这样的书写更加简洁。
```
var foo = 'bar';
var baz = {foo};
baz // {foo: "bar"}

// 等同于
var baz = {foo: foo};

/////////////////////////////////

function f(x, y) {
  return {x, y};
}

// 等同于

function f(x, y) {
  return {x: x, y: y};
}

f(1, 2) // Object {x: 1, y: 2}


////////////////

var o = {
  method() {
    return "Hello!";
  }
};

// 等同于

var o = {
  method: function() {
    return "Hello!";
  }
};
```
- 注意，简洁写法的属性名总是字符串，这会导致一些看上去比较奇怪的结果。
```
var obj = {
  class () {}
};

// 等同于

var obj = {
  'class': function() {}
};
```

##### 属性名表达式
- JavaScript 定义对象的属性，有两种方法。
```
// 方法一
obj.foo = true;

// 方法二
obj['a' + 'bc'] = 123;
```
- 上面代码的方法一是直接用标识符作为属性名，方法二是用表达式作为属性名，这时要将表达式放在方括号之内。
但是，如果使用字面量方式定义对象（使用大括号），在 ES5 中只能使用方法一（标识符）定义属性。
- ES6 允许字面量定义对象时，用方法二（表达式）作为对象的属性名，即把表达式放在方括号内。
```
var obj = {
  foo: true,
  abc: 123
};


//////////////分割线

let propKey = 'foo';

let obj = {
  [propKey]: true,
  ['a' + 'bc']: 123
};
```

##### Object.is()
- ES5 比较两个值是否相等，只有两个运算符：相等运算符（==）和严格相等运算符（===）。它们都有缺点，前者会自动转换数据类型，后者的NaN不等于自身，以及+0等于-0。JavaScript 缺乏一种运算，在所有环境中，只要两个值是一样的，它们就应该相等。

ES6 提出“Same-value equality”（同值相等）算法，用来解决这个问题。Object.is就是部署这个算法的新方法。它用来比较两个值是否严格相等，与严格比较运算符（===）的行为基本一致。
```
Object.is('foo', 'foo')
// true
Object.is({}, {})
// false
```
- 不同之处只有两个：一是+0不等于-0，二是NaN等于自身。

##### Object.assign()
- Object.assign方法用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象（target）。
```
var target = { a: 1 };

var source1 = { b: 2 };
var source2 = { c: 3 };

Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}
```
- Object.assign方法的第一个参数是目标对象，后面的参数都是源对象。

注意，如果目标对象与源对象有同名属性，或多个源对象有同名属性，则后面的属性会覆盖前面的属性。
- 如果只有一个参数，Object.assign会直接返回该参数。
- 如果该参数不是对象，则会先转成对象，然后返回。
- 由于undefined和null无法转成对象，所以如果它们作为参数，就会报错。
```
var obj = {a: 1};
Object.assign(obj) === obj // true


typeof Object.assign(2) // "object"

Object.assign(undefined) // 报错
Object.assign(null) // 报错
```
- Object.assign方法实行的是浅拷贝，而不是深拷贝。也就是说，如果源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用。
