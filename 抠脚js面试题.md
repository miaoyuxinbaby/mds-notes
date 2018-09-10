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