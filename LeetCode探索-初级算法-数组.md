# 数组

> [从排序数组中删除重复项](#从排序数组中删除重复项) <br>
> [买卖股票的最佳时机 II](#买卖股票的最佳时机-II) <br>
> [旋转数组](#旋转数组) <br>
> [存在重复元素](#存在重复元素) <br>
> [只出现一次的数字](#只出现一次的数字) <br>
> [两个数组的交集 II](#两个数组的交集-II) <br>
> [加一](#加一) <br>
> [移动零](#移动零) <br>
> [两数之和](#两数之和) <br>
> [有效的数独](#有效的数独) <br>


## 从排序数组中删除重复项

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/1/array/21/

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

## 买卖股票的最佳时机 II

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/1/array/22/

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

## 旋转数组

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/1/array/23/

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


## 存在重复元素

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/1/array/24/

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

## 只出现一次的数字

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/1/array/25/

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

## 两个数组的交集 II

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/1/array/26/

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

## 加一

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/1/array/27/

  >> 第一次的答案
```js
// 解答错误
// 超出了最大精度好像。。。。
// js最大整数 9007199254740991 共16位
// parseInt("6145390195186705543899")
// 6145390195186705000
/**
 * @param {number[]} digits
 * @return {number[]}
 */
var plusOne = function(digits) {
  var resNum = +(digits.join('')) + 1 + ''
  return resNum.split('').map(_ => +_)
};
```

  >> 第二次的答案


```js
// 超出内存  这代码对[9]毫无抵抗能力，死循环了
/**
 * @param {number[]} digits
 * @return {number[]}
 */
var plusOne = function(digits) {
  const len = digits.length
  let whileing = true
  let i = len - 1
  while (whileing) {
    if (++digits[i] < 10) {
      whileing = false
      break
    } else {
      digits[i] = 0
      i--
    }
  }

  return digits
};
```

  >> 最终答案

```js
/**
 * @param {number[]} digits
 * @return {number[]}
 */
var plusOne = function(digits) {
  const len = digits.length
  let whileing = true
  let i = len - 1
  while (whileing) {
    if (++digits[i] < 10) {
      whileing = false
      break
    } else {
      digits[i] = 0
      i--
      if (i < 0) {
        digits.unshift(1)
        break
      }
    }
  }

  return digits
};
```

 >> 别人的答案

```js
// 相比于用for循环，我用while，多维护了一个i的状态， for循环中，第二和三个参数，直接解决了这个状态
/**
 * @param {number[]} digits
 * @return {number[]}
 */
var plusOne = function(digits) {
  var len = digits.length;
  for (var i = len - 1; i >= 0; i--) {
    if (digits[i] < 9) {
      digits[i]++;
      return digits; 
    }
    digits[i] = 0;
  }
  return [1, ...digits];
};
```

## 移动零

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/1/array/28/

>> 我的答案

```js
/**
 * @param {number[]} nums
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var moveZeroes = function(nums) {
  let len = nums.length
  for (let i = 0; i < len; i++) {
    if (nums[i] === 0) {
      let j = i
      while (true) {
        j++        
        if (j >= len) return
        if (nums[j] !== 0) {
          nums[i] = nums[j]
          nums.splice(j, 1)
          nums.push(0)
          break
        }
      }
    }
  }
  return
};
```

>> 别人的答案

```js
// mmp, 又把问题复杂化了，本来一开始也想着从后面往前循环的。
// 从前往后循环，必须要保证当前索引不会消失，下面的方法没有这个担心
// 从后往前循环，可以删除当前项
/**
 * @param {number[]} nums
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var moveZeroes = function(nums) {
  for (var i = nums.length - 1; i >= 0; i--) {
    if (nums[i] === 0){
      nums.splice(i,1);
      nums.push(0);
    }
  }
};

// 递归
/**
 * @param {number[]} nums
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var moveZeroes = function(nums) {
    let n = -1;
    const move = (i) => {
        n++;
        if (n < nums.length) {
            if (nums[i] === 0) {
                nums.push(0);
                nums.splice(i, 1);
                move(i);
            } else {
                move(i + 1);
            }
        }
    }
    move(0);
};
```

## 两数之和

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/1/array/29/

>> 我的答案

```js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var twoSum = function(nums, target) {
  let len = nums.length
  for (let i = 0; i < len; i++) {
    // 第一次提交，我赌了一次，他说的整数不包含负整数。。。。太嫩了。
    // if (nums[i] > target) continue
    for (let j = i + 1; j < len; j++) {
      if (nums[i] + nums[j] === target) return [i, j]
    }
  }
};
```

>> 别人的答案

```js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */

// js中的对象是基于哈希表结构的.哈希表的查找时间复杂度为O(1) 所以很多人喜欢用对象来做映射，减少遍历循环。比如常见的数组去重
// 对象中的key在obj[key]时都被自动转成了字符串类型。

// 下面那句话是别人提交记录里的，学到了。
// 用对象的 hash来查找的效率会比数组便利高很多 所以这里就相当于利用了缓存的思想
var twoSum = function(nums, target) {
  var len = nums.length;
  var numObj = {};
  for (var i = 0; i < len; i++) {
    var current = nums[i];
    var match = target - current;
    if (match in numObj) {
      return [i, numObj[match]]
    } else {
      numObj[current] = i;
    }
  }
};
```

## 有效的数独

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/1/array/30/

>> 我的答案

```js

// 跑了三次才跑出来。。。每次都是粗心导致的错误。
/**
 * @param {character[][]} board
 * @return {boolean}
 */
var isValidSudoku = function(board) {
  let len = 9

  let index = 9
  const mapY = []
  const mapXY = []
  
  while (index--) {
    mapY.push({}) 
    mapXY.push({}) 
  }
  
  for (let i = 0; i < len; i++) {
    const mapX = {}
    for (let j = 0; j < len; j++) {
      if (board[i][j] === '.') continue

      if (board[i][j] in mapX) return false
      else mapX[board[i][j]] = true
      
      if (board[i][j] in mapY[j]) return false
      else mapY[j][board[i][j]] = true

      let subfix

      if ((i + 1) / 3 <= 1) {
        if ((j + 1) / 3 <= 1) subfix = 0
        else if ((j + 1) / 3 <= 2) subfix = 1
        else subfix = 2
      } else if ((i + 1) / 3 <= 2) {
        if ((j + 1) / 3 <= 1) subfix = 3
        else if ((j + 1) / 3 <= 2) subfix = 4
        else subfix = 5
      } else if ((i + 1) / 3 <= 3) {
        if ((j + 1) / 3 <= 1) subfix = 6
        else if ((j + 1) / 3 <= 2) subfix = 7
        else subfix = 8
      }

      if (board[i][j] in mapXY[subfix]) return false
      else mapXY[subfix][board[i][j]] = true
    }
  }

  return true
};
```