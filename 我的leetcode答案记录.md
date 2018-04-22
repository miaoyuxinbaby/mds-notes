
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