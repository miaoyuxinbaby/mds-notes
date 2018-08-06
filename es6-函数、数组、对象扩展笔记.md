# es6
## 一、函数扩展

#### 尾调用

> 以下三种都不是尾调用

```js
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

上面代码中，情况一是调用函数g之后，还有赋值操作，所以不属于尾调用，即使语义完全一样。情况二也属于调用后还有操作，即使写在一行内。情况三等同于下面的代码。

```js
function f(x){
  g(x);
  return undefined;
}
```

#### 尾调用优化总结

- 当funcA中执行funcB时，funcA的内存不会释放。如果funcB是尾调用的话就会释放funcA的内存
- 尾调用优化，释放父级函数的内存，每次执行时，保证调用帧只有一项。return nextFunc();

```js
function f(x) {
  if (x > 0) {
    return m(x)
  }
  return n(x);
}
```

- 只有不再用到外层函数的内部变量，内层函数的调用帧才会取代外层函数的调用帧，否则就无法进行“尾调用优化”。

```js
function addOne(a){
  var one = 1;
  function inner(b){
    return b + one;
  }
  return inner(a);
}
```
上面的函数不会进行尾调用优化，因为内层函数inner用到了外层函数addOne的内部变量one。

#### 尾递归

- 函数调用自身，称为递归。如果尾调用自身，就称为尾递归。

递归非常耗费内存，因为需要同时保存成千上百个调用帧，很容易发生“栈溢出”错误（stack overflow）。但对于尾递归来说，由于只存在一个调用帧，所以永远不会发生“栈溢出”错误。

```js
function factorial(n) {
  if (n === 1) return 1;
  return n * factorial(n - 1);
}

factorial(5) // 120
```

> 上面代码是一个阶乘函数，计算n的阶乘，最多需要保存n个调用记录，复杂度 O(n) 。
> 如果改写成尾递归，只保留一个调用记录，复杂度 O(1) 。

```js
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

```js
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

## 二、数组扩展


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
```js
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

> 注意，简洁写法的属性名总是字符串，这会导致一些看上去比较奇怪的结果。

```js
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

```js
// 方法一
obj.foo = true;

// 方法二
obj['a' + 'bc'] = 123;
```
- 上面代码的方法一是直接用标识符作为属性名，方法二是用表达式作为属性名，这时要将表达式放在方括号之内。
但是，如果使用字面量方式定义对象（使用大括号），在 ES5 中只能使用方法一（标识符）定义属性。
- ES6 允许字面量定义对象时，用方法二（表达式）作为对象的属性名，即把表达式放在方括号内。

```js
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

```js
Object.is('foo', 'foo')
// true
Object.is({}, {})
// false
```

> 不同之处只有两个：一是+0不等于-0，二是NaN等于自身。

##### Object.assign()
- Object.assign方法用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象（target）。

```js
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

```js
var obj = {a: 1};
Object.assign(obj) === obj // true


typeof Object.assign(2) // "object"

Object.assign(undefined) // 报错
Object.assign(null) // 报错
```
- Object.assign方法实行的是浅拷贝，而不是深拷贝。也就是说，如果源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用。
