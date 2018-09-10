# 实现一个符合PromiseA+规范的Promise

```js
/**
 * Promise对象
 * @param {Function} executor 执行器
 */
function Promise(executor) {
  var _this = this
  this.state = 'pending'
  this.value = undefined
  this.reason = undefined
  this.onFulfilledFunc = []
  this.onRejectedFunc = []

  try {
    executor(resolve, reject)
  } catch (e) {
    reject(e.message)
  }

  function resolve(value) {
    if (_this.state === 'pending') {
      _this.value = value
      _this.state = 'resolved'
      _this.onFulfilledFunc.forEach(f => f(value))
    }
  }

  function reject(reason) {
    if (_this.state === 'pending') {
      _this.reason = reason
      _this.state = 'rejected'
      _this.onRejectedFunc.forEach(f => f(reason))
    }
  }
}

/**
 * 状态更新后执行
 * @param {Function} onFulfilled 成功态回调
 * @param {Function} onRejected 失败态回调
 */
Promise.prototype.then = function(onFulfilled, onRejected) {
  // 解决值的传递，如果没给任何参数就使用默认回调
  onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : val => val
  onRejected =
    typeof onRejected === 'function'
      ? onRejected
      : e => {
          throw e
        }

  var promise2 = new Promise((resolve, reject) => {
    if (this.state === 'pending') {
      if (typeof onFulfilled === 'function') {
        this.onFulfilledFunc.push(value => {
          setTimeout(() => {
            try {
              let x = onFulfilled(value) // 要保证onFulfilled是异步执行
              resolvePromise(promise2, x, resolve, reject)
            } catch (error) {
              reject(error)
            }
          })
        })
      }
      if (typeof onRejected === 'function') {
        this.onRejectedFunc.push(reason => {
          setTimeout(() => {
            try {
              let x = onRejected(reason)
              resolvePromise(promise2, x, resolve, reject)
            } catch (error) {
              reject(error)
            }
          })
        })
      }
    }

    if (this.state === 'resolved') {
      if (typeof onFulfilled === 'function') {
        setTimeout(() => {
          try {
            let x = onFulfilled(this.value)
            resolvePromise(promise2, x, resolve, reject)
          } catch (error) {
            reject(error)
          }
        })
      }
    }
    if (this.state === 'rejected') {
      if (typeof onRejected === 'function') {
        setTimeout(() => {
          try {
            let x = onRejected(this.reason)
            resolvePromise(promise2, x, resolve, reject)
          } catch (error) {
            reject(error)
          }
        })
      }
    }
  })

  return promise2
}

/**
 * 根据上一个then的结果解析Promise
 * @param {Object} promise2 新Promise对象
 * @param {Object} x 上一个then的返回值
 * @param {Function} resovle 新Promise的成功态回调
 * @param {Function} reject 新Promise的失败态回调
 */
function resolvePromise(promise2, x, resolve, reject) {
  if (promise2 === x) {
    reject(new TypeError('Promise发生了循环引用'))
  }

  let called = false // 调用标记，防止成功和失败都调用
  if (x !== null && (typeof x === 'object' || typeof x === 'function')) {
    // 是对象或函数
    try {
      let then = x.then
      if (typeof then === 'function') {
        // 如果是Promise就执行它，继续递归
        then.call(
          x,
          y => {
            if (called) return
            called = true // 调用过了，将标记更新
            resolvePromise(promise2, y, resolve, reject)
          },
          r => {
            if (called) return
            called = true // 调用过了，将标记更新
            reject(r)
          }
        )
      } else {
        resolve(x)
      }
    } catch (error) {
      if (called) return
      called = true // 调用过了，将标记更新
      reject(error)
    }
  } else {
    //是普通值
    resolve(x)
  }
}

//Code for test
// Promise.defer = Promise.deferred = function () {
//     var dfd = {};
//     dfd.promise = new Promise((resolve, reject) => {
//         dfd.resolve = resolve;
//         dfd.reject = reject;
//     });
//     return dfd;
// };

module.exports = Promise
```