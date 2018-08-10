# 字符串

> [反转字符串](#反转字符串) <br>
> [颠倒整数](#颠倒整数) <br>
> [字符串中的第一个唯一字符](#字符串中的第一个唯一字符) <br>
> [有效的字母异位词](#有效的字母异位词) <br>


## 反转字符串

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/5/strings/32/

> [返回导航](#字符串)


>> 我的答案

```js
/**
 * @param {string} s
 * @return {string}
 */
var reverseString = function(s) {
  return s.split('').reverse().join('')
};
```

>> 比人的答案

```js
// s.charAt(i) 获取s中索引为i的字符 （字符串可以通过下标获取字符）
/**
 * @param {string} s
 * @return {string}
 */
var reverseString = function(s) {
    var len = s.length;
    var r = '';
    for(var i=len-1;i>=0;i--) {
        r += s.charAt(i);
    }
    return r;
};
```

## 颠倒整数

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/5/strings/33/

> [返回导航](#字符串)


>> 我的答案

```js
// 前2次提交，一次失败是忘记还原成负数，一次是忘记了边界条件，超过最大和最小数溢出时返回0
/**
 * @param {number} x
 * @return {number}
 */
var reverse = function(x) {
  if (x < 0) {
    let num = -(+(0 - x + '').split('').reverse().join(''))
    if (num < Math.pow(-2, 31)) return 0
    else return num
  } else {
    let num = +((x + '').split('').reverse().join(''))
    if (num > Math.pow(2, 31) - 1) return 0
    else return num
  }
};
```

## 字符串中的第一个唯一字符

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/5/strings/34/

> [返回导航](#字符串)


>> 我的答案

```js
// 第一次提交，忘记了没找到返回-1，审题不太清，哈哈
/**
 * @param {string} s
 * @return {number}
 */
var firstUniqChar = function(s) {
  let len = s.length
  const map = {}

  for (let i = 0; i < len; i++) {
    if (map[s.charAt(i)]) continue
    if (s.indexOf(s.charAt(i)) !== s.lastIndexOf(s.charAt(i))) {
      map[s.charAt(i)] = true
    } else {
      return i
    }
  }
  return -1
};
```

>> 别人的答案

```js
// indexOf第二个参数可以指定起点。 也许比lastIndexOf性能更好一点，同时也是顺序寻找的一种方式
/**
 * @param {string} s
 * @return {number}
 */
var firstUniqChar = function(s) {
  for (var i = 0; i < s.length; i++) {
    if (s.indexOf(s[i]) == i && s.indexOf(s[i], i + 1) == -1) {
      return i;
    }
  }
  return -1;
};
```

## 有效的字母异位词

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/5/strings/35/

> [返回导航](#字符串)


>> 我的答案

```js
// 尝试用array.prototype.apply（s），失败 
// 也可以用Array.from()转换
/**
 * @param {string} s
 * @param {string} t
 * @return {boolean}
 */
var isAnagram = function(s, t) {
  if (s.split('').sort().join('') === t.split('').sort().join('')) return true
  else return false
};
```

>> 别人的答案

```js
// 很清奇的思路。 循环这个字符串，每次循环删除2个字符串里所有的‘当前’字符。
// 并进行判断
/**
 * @param {string} s
 * @param {string} t
 * @return {boolean}
 */
var isAnagram = function(s, t) {
    let i,z;
    
    if(s.length!==t.length) return false
    if(s.length === 0) return true
    for(i=0; i<s.length; i++){
      z = new RegExp(s[i],'g')
      s = s.replace(z,'')
      t = t.replace(z,'')
      console.log(s)
      if(s.length !== t.length) return false
      if(s.length === 0) return true
      i=-1
    }
};
```
