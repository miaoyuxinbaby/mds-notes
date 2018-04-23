
- 从排序数组中删除重复项

  desc: 给定一个排序数组，你需要在原地删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。不要使用额外的数组空间，你必须在原地修改输入数组并在使用 O(1) 额外空间的条件下完成。

  url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/1/array/21/

```
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

- 买卖股票的最佳时机 II

url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/1/array/22/

```
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
- 旋转数组

url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/1/array/23/
```
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