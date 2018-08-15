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