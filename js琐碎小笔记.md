- ~[number] === -[number] - 1 eg: ~8 === -9
- && 优先级高于 ||   
- null 是基本类型中唯一的一个“假值”，typeof null === 'object'，所以检测null的类型需要：(!a && typeof a === "object")
- typeof function a(){ /* .. */ } === "function"; // true  function是object的一个子类型
  - 具体来说，函数是“可调用对象”，它有一个内部属性 [[Call]]，该属性使其可以被调用
  - 函数不仅是对象，还可以拥有属性
    - 函数的length属性将返回没有指定默认值的参数个数，rest 参数也不会计入length属性。
- 数组也是object的一个子类型
- 暂时性死区 也意味着typeof不再是一个百分之百安全的操作。
- 通过变量进行条件判断时可能报错

```
// 这样会抛出错误
if (DEBUG) {
console.log( "Debugging is starting" );
}
// 这样是安全的
if (typeof DEBUG !== "undefined") {
console.log( "Debugging is starting" );
}
```

- 变量没有类型，但它们持有的值有类型。类型定义了值的行为特征。
- 使用 delete 运算符可以将单元从数组中删除，但是单元删除后，数组的 length 属性并不会发生变化。
- 数组通过数字进行索引，但有趣的是它们也是对象，所以也可以包含字符串键值和属性（但这些并不计算在数组长度内）
- 如果字符串键值能够被强制类型转换为十进制数字的话，它就会被当作数字索引来处理。

```
var a = [ ];
a["13"] = 42;
a.length; // 14
```

- JavaScript 中的字符串和字符数组并不是一回事，最多只是看上去相似而已。
- 字符串不可变是指字符串的成员函数不会改变其原始值，而是创建并返回一个新的字符串。而数组的成员函数都是在其原始值上进行操作

```
var a = 'abc'
a[1] // b
```

-  . 运算符需要给予特别注意，因为它是一个有效的数字字符，会被优先识别为数字常量的一部分，然后才是对象属性访问运算符

```
// 无效语法：
42.toFixed( 3 ); // SyntaxError
// 下面的语法都有效：
(42).toFixed( 3 ); // "42.000"
0.42.toFixed( 3 ); // "0.420"
42..toFixed( 3 ); // "42.000
```

- 应该怎样来判断 0.1 + 0.2 和 0.3 是否相等
  - 最常见的方法是设置一个误差范围值，通常称为“机器精度”（machine epsilon）， 对JavaScript 的数字来说，这个值通常是 2^-52 (2.220446049250313e-16)。
  - Number.EPSILON

```
function numbersCloseEnoughToEqual(n1,n2) {
  return Math.abs( n1 - n2 ) < Number.EPSILON;
}
var a = 0.1 + 0.2;
var b = 0.3;
numbersCloseEnoughToEqual( a, b ); // true
numbersCloseEnoughToEqual( 0.0000001, 0.0000002 ); // false
```

- 能够呈现的最大浮点数大约是 1.798e+308（这是一个相当大的数字），它定义在 Number.MAX_VALUE 中。
- 最小浮点数定义在 Number.MIN_VALUE 中，大约是 5e-324，它不是负数，但无限接近于 0 ！
- 能够被“安全”呈现的最大整数是 2^53 - 1，即 9007199254740991  Number.MAX_SAFE_INTEGER
- 最小整数就是负的最大整数 Number.MIN_SAFE_INTEGER

- 要检测一个值是否是整数，可以使用 ES6 中的 Number.isInteger(..) 方法

```
Number.isInteger( 42 ); // true
Number.isInteger( 42.000 ); // true
Number.isInteger( 42.3 ); // false
```

- NaN 无效数值（not a number）属于number类型
- 1/0 === infinity 无穷大
- 复合值——对象（包括数组和封装对象）和函数，
- js中，引用指向的是值本身而非变量，值传递是复制一份，函数的形参就是个赋值操作，具体是值传递还是引用看赋值的类型

```
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

```
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

- [1,2].length = 0 清空数组
- 标量基本类型值是不可更改的（字符串和布尔也是如此）。如果一个数字对象的标量基本类型值是 2，那么该值就不能更改，除非创建一个包含新值的数字对象