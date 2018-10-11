# 动态规划

> [爬楼梯](#爬楼梯) <br>
> [买卖股票的最佳时机](#买卖股票的最佳时机) <br>
> [最大子序和](#最大子序和) <br>
> [打家劫舍](#打家劫舍) <br>


## 爬楼梯

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/23/dynamic-programming/54/

> [返回导航](#动态规划)


>> 我的答案

```js
/**
 * @param {number} n
 * @return {number}
 * 爬楼梯符合斐波拉契数列
 */
var climbStairs = function(n) {
    let a = b = 1

    for (let i = 0; i < n; i++) {
      [a, b] = [b, b + a]
    }
    return a
};

```

## 买卖股票的最佳时机

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/23/dynamic-programming/55/

> [返回导航](#动态规划)


>> 我的答案

```js
/**
 * @param {number[]} prices
 * @return {number}
 * 首先找到最小买入是2，然后做差7-2=5，保存利润，然后到最小买入变成1，此时利润还是5，然后到3
 * 如果1后面出现的数字足够大，大到和1做差的值大于5，那么最大利润值就改变，否则，最大利润还是5.
 * 这里暗含的逻辑是，后面的数如果减1的差肯定比减2的差来的大。
 */
var maxProfit = function(prices) {
  let len = prices.length
  if (len < 2) return 0
  let profit = 0
  let minPrice = prices[0]

  for (const i of prices) {
    minPrice = Math.min(i, minPrice)
    profit = Math.max(i - minPrice, profit)
  }
  
  return profit
};

```

## 最大子序和

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/23/dynamic-programming/56/

> [返回导航](#动态规划)


>> 我的答案

```js
/**
 * @param {number[]} nums
 * @return {number}
 */
var maxSubArray = function(nums) {
    let sum = nums[0]
    let result = nums[0]

    for (let i = 1; i < nums.length; i++) {
      // 如果加着加着加成了负数，就舍弃之前的。
      if (result > 0) result += nums[i]
      else result = nums[i]
      if (sum < result) sum = result
    }

    return sum
};
```

## 打家劫舍

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/23/dynamic-programming/57/

> [返回导航](#动态规划)


>> 我的答案

```js
/**
 * @param {number[]} nums
 * @return {number}
 */
var rob = function(nums) {
  let len = nums.length
  if (len === 0) return 0
  if (len === 1) return nums[0]

  let dp = new Array(len)
  dp[0] = nums[0]
  dp[1] = Math.max(nums[0], nums[1])

  for (let i = 2; i < len; i++) {
    dp[i] = Math.max(dp[i - 1], dp[i - 2] + nums[i])
  }
  return dp[len - 1]
};
```