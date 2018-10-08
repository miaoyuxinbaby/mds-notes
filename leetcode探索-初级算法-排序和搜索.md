# 排序和搜索

> [合并两个有序数组](#合并两个有序数组) <br>
> [第一个错误的版本](#第一个错误的版本) <br>

## 合并两个有序数组

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/8/sorting-and-searching/52/

> [返回导航](#排序和搜索)

>> 我的答案

```js
/**
 * @param {number[]} nums1
 * @param {number} m
 * @param {number[]} nums2
 * @param {number} n
 * @return {void} Do not return anything, modify nums1 in-place instead.
 */
var merge = function (nums1, m, nums2, n) {
  // 剩余需要插入nums1的值数量
  let count = nums1.length - m
  // 删除多余的
  while (count--) {
    nums1.pop()
  }
  // 用于nums1的指针
  let sub = 0
  // 用于nums2的指针
  let sub2 = 0
  while (sub2 < n) {
    sub = 0
    sub2 = 0
    for (; sub < m + n; sub++) {
      if (nums1[sub] <= nums2[sub2] && nums1[sub + 1] > nums2[sub2] || nums1[sub] > nums2[sub2] || sub >= nums1.length) {

        // 往前插
        if (nums1[sub] > nums2[sub2]) {
          nums1.splice(sub, 0, nums2[sub2])
        } else {
        // 往后插
          nums1.splice(sub + 1, 0, nums2[sub2])
        }
        sub2++
      }
    }
  }
};
```

>> 别人的

```js
var merge = function (nums1, m, nums2, n) {
    let i = m - 1,
        j = n - 1,
        k = m + n - 1;
    while (i >= 0 && j >= 0) {
        if (nums1[i] > nums2[j])
            nums1[k--] = nums1[i--];
        else
            nums1[k--] = nums2[j--];
    }
    while (j >= 0)
        nums1[k--] = nums2[j--];
};
```

## 第一个错误的版本

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/8/sorting-and-searching/53/

> [返回导航](#排序和搜索)

>> 我的答案

```js
/**
 * Definition for isBadVersion()
 * 
 * @param {integer} version number
 * @return {boolean} whether the version is bad
 * isBadVersion = function(version) {
 *     ...
 * };
 */

/**
 * @param {function} isBadVersion()
 * @return {function}
 * 怎么找？ 我猜是二分查找
 */
var solution = function(isBadVersion) {
  /**
   * @param {integer} n Total versions
   * @return {integer} The first bad version
   */
  return function(n) {
    let low = 1
    let high = n
    while (low <= high) {
      let mid = parseInt(low / 2 + high / 2)
      // 这里的高减一底加一，有效的避免了死循环，慢慢逼近
      if (isBadVersion(mid)) high = mid - 1
      else low = mid + 1
    }
    return low
  }
}
```
