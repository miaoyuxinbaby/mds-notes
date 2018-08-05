### 数组

- <a href="#1">从排序数组中删除重复项</a>
- <a href="#2">买卖股票的最佳时机</a>
- <a href="#3">旋转数组</a>
- <a href="#4">存在重复元素</a>
- <a href="#5">只出现一次的数字</a>
- <a href="#6">两个数组的交集 II</a>

<br>
<br>
<br>
<br>
<br>
<br>
<br>
> <span id="1">从排序数组中删除重复项</span>

  url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/1/array/21/

```js
/**
 * @param {number[]} nums
 * @return {number}
 */
var removeDuplicates = function (nums) {
  var len = nums.length
  var sub = 0
  for (var i = 0; i < len - 1; i++) {    
    if (nums[sub] === nums[sub + 1]) {
      nums.splice(sub, 1)
    } else {
      ;++sub
    }
  }

  return nums.length
};

removeDuplicates([1,1,1,1,1,1,2,2,3,4,4,4,4,4,4,4])

// 看了别人代码之后的总结：
// 完全没有必要删除重复的（审题不清），答案只要返回个数就行。
// 提前考虑边界情况也许可以降低复杂度
```

> <span id="2">买卖股票的最佳时机 II</span>

url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/1/array/22/

```js
/**
 * @param {number[]} prices
 * @return {number}
 */
var maxProfit = function(prices) {
    var profits = 0
    var max = 0
    var len = prices.length
    for (var i = 1; i < len; i++) {
        profits = prices[i] - prices[i-1]
        if (profits > 0) {
            max += profits
        }
    }
    return max
};

// 看了别人代码之后的总结：
// 判断是否盈利的时候可以不计算，直接比较大小，思维漏洞了
```

> <span id="3">旋转数组</span>

url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/1/array/23/

```js
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var rotate = function(nums, k) {
  var len = nums.length
  var tempArr = nums.splice(len-k)
  tempArr.reverse().forEach((item) => {
      nums.unshift(item)
  })
};

// 看了别人代码之后的总结：
// 总是记不得使用apply, 多用了个循环
```


> <span id="4">存在重复元素</span>

url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/1/array/24/

```js

// 我的答案
/**
 * @param {number[]} nums
 * @return {boolean}
 */
var containsDuplicate = function(nums) {
  let arr = nums.sort()
  let isHasSame = false
  for (let i = 0, len = arr.length - 1; i < len; i++) {
    if (arr[i] === arr[i+1]) {
      isHasSame = true
      break
    }
  }
  return isHasSame
};

// 别人的
// 根本就没想起来用set。。。
var containsDuplicate = function (nums) {
    const set = new Set(nums)
    return set.size != nums.length
};

// 和我的思路方向一致
var containsDuplicate = function(nums) {
    const map={};
    for(let v of nums){
        if(!map[v]){
            map[v]=true;
            continue;
        }
        return true;
    }
    return false;
};
```

> <span id="5">只出现一次的数字</span>

url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/1/array/25/

```js
// me 
/**
 * @param {number[]} nums
 * @return {number}
 */
var singleNumber = function(nums) {
  nums.sort()
  var i = 0
  while (true) {
    if (nums[i] === nums[i+1]) i += 2
    else return nums[i]
  }
};

// other
// 异或。。。。

/*
  真⊕假=真
  假⊕真=真
  假⊕假=假
  真⊕真=假
  1 ^ 2
  0001
  0010
  === 0011
  如下特性 
  与自身异或为0，与0异或不变
  异或满足交换律和结合律
  a^b^c=a^(b^c)
  [1,2,3,4,4,3,1]
  0^1^2^3^4^4^3^1 === 0^1^1^3^3^4^4^2
*/
var singleNumber = function(nums) {
  let result = 0;
  nums.forEach(el => (result ^= el));
  return result;
};
```

> <span id="6">两个数组的交集 II</span>

url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/1/array/26/

```js

// 脑子抽了，第一次写的时候有个set完全是多余的操作，忘记删掉了。。。
// 后来发现，删除后速度应该更快的代码，反而更慢了，然后再提交了一次，又快了，
// 以上： leetcode代码执行速度不准确（受一些因素的影响）
/**
 * @param {number[]} nums1
 * @param {number[]} nums2
 * @return {number[]}
 */
var intersect = function(nums1, nums2) {
  let len1 = nums1.length
  let len2 = nums2.length
  let ret = []

  if (len1 >= len2) {
    for (let i = 0; i < len2; i++) {
      let indexInNums1 = nums1.indexOf(nums2[i])
      if (~indexInNums1) {
        ret.push(nums2[i])
        nums1[indexInNums1] = ''
      }
    }
  } else {
    for (let i = 0; i < len1; i++) {
      let indexInNums2 = nums2.indexOf(nums1[i])
      if (~indexInNums2) {
        ret.push(nums1[i])
        nums2[indexInNums2] = ''
      }
    }
  }

  return ret
};

// 妙啊，key是出现的数字，val是次数，不管nums1、nums2哪个长，都只要循环一次。O(n)
/**
 * @param {number[]} nums1
 * @param {number[]} nums2
 * @return {number[]}
 */
var intersect = function(nums1, nums2) {
    const map={};
    const res=[];
    nums1.forEach(function (v) {
        map[v]?map[v]++:map[v]=1;
    });

    nums2.forEach(function (v) {
        if(map[v]){
            res.push(v);
            map[v]--;
        }
    });
    return res;
};
```