# 树

> [二叉树的最大深度](#二叉树的最大深度) <br>
> [验证二叉搜索树](#验证二叉搜索树) <br>
> [对称二叉树](#对称二叉树) <br>
> [二叉树的层次遍历](#二叉树的层次遍历) <br>
> [将有序数组转换为二叉搜索树](#将有序数组转换为二叉搜索树) <br>


## 二叉树的最大深度

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/7/trees/47/

> [返回导航](#树)

>> 我的答案

```js
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number}
 */
var maxDepth = function(root) {
  if (root === null) return 0

  let leftCount = maxDepth(root.left)
  let rightCount = maxDepth(root.right)

  let childDepth = leftCount > rightCount ? leftCount : rightCount

  return childDepth + 1
}
```

>> 别人

```js
var maxDepth = function(root) {
  // 将递归函数分离
  let max = 0;
  const DFS = (node, depth) => {
    if (node === null) {
      max = max>depth?max:depth;
      return;
    }
    DFS(node.left, depth + 1);
    DFS(node.right, depth + 1);
  }
  DFS(root,0);
  return max;
};
```

## 验证二叉搜索树

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/7/trees/48/

> [返回导航](#树)

>> 我的答案

```js
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {boolean}
 * 中序遍历应该越来越大
 * 贼坑，声明的全局变量，提交的时候通不过，怎么改名字都不行。哎。hack一下，只能传递引用或者是在isValidBST中声明isValidBSTUtilfn。感觉不太好
 */


var isValidBST = function (root) {
  if (root === null || !root.left && !root.right) return true

  let obj = {
    res: true,
    max: Number.MIN_SAFE_INTEGER
  }
  isValidBSTUtilfn(root, obj)
  return obj.res
};

function isValidBSTUtilfn (node, obj) {
  if (!obj.res || node === null) return

  isValidBSTUtilfn(node.left, obj)

  if (node.val > obj.max) obj.max = node.val
  else {
    obj.res = false
    return
  }

  isValidBSTUtilfn(node.right, obj)
}
```

>> 别人的

```js
// 操作比较秀
var isValidBST = function(root) {
  return validate(root, Number.MIN_SAFE_INTEGER, Number.MAX_SAFE_INTEGER);
};

const validate = (node, min, max) => {
  if (!node) {
    return true;
  }

  if (node.val < min || node.val > max) {
    return false;
  }

  return (validate(node.left, min, node.val - 1) && validate(node.right, node.val + 1, max));
}

```

## 对称二叉树

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/7/trees/49/

> [返回导航](#树)

>> 我的答案

```js
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {boolean}
 * 弄个新树，在原树的基础上，每个节点左右互换，然后和原树做比较 迭代递归都行
 * 用中序遍历取值
 */
var isSymmetric = function (root) {
  let newTree = JSON.parse(JSON.stringify(root))

  reverseTree(newTree)

  let newList = []
  let oldList = []
  
  getList(newTree, newList)
  getList(root, oldList)

  for (let i = 0, len = oldList.length; i < len; i++) {
    if (newList[i] !== oldList[i]) return false
  }
  return true
};

// 先序遍历镜像反转二叉树
function reverseTree (node) {
  if (!node) return

  let tempNode = node.left
  node.left = node.right
  node.right = tempNode

  reverseTree(node.left)
  reverseTree(node.right)
}

// 中序遍历获取值列表
function getList (node, list) {
  if (node) {
    getList(node.left, list)

    pushIntoList(node.left, list)
    pushIntoList(node.right, list)

    getList(node.right, list)
  }
}

function pushIntoList (node, list) {
  if (!node) {
    list.push(null)
    return
  }

  list.push(node.val)
}
```

>> 别人的

```js
// 我还是太嫩了
// 采用BFS方式时，从两侧遍历同一层节点，比对对应节点值
function comp(root1,root2){
    if(!root1 && !root2){
        return true;
    }
    if(!root1 || !root2){
        return false;
    }
    if(root1.val != root2.val){
        return false;
    }
    return comp(root1.left, root2.right) && comp(root1.right, root2.left);
}


var isSymmetric = function(root) {
    if (!root) return true;
    return comp(root.left, root.right);
};

```

## 二叉树的层次遍历

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/7/trees/50/

> [返回导航](#树)

>> 我的答案

```js
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[][]}
 */

var levelOrder = function (root) {
  if (!root) return []

  let answer = []
  // 第一层只有一个根节点
  let tempNodes = [root]

  while (tempNodes.length) {
    let res = []
    // 下一层的节点
    let _tmp = []

    tempNodes.forEach(item => {
      res.push(item.val)
      // 收集下一层节点
      item.left && _tmp.push(item.left)
      item.right && _tmp.push(item.right)
    })

    answer.push(res)
    tempNodes = _tmp
  }

  return answer
}

```

>> 别人的

```js
// 递归处理
var levelOrder = function(root) {
    let ans = new Array();
    if (!root) {
      return ans;
    }
    helper(root, ans, 0);
    function helper(node, ans, level) {
      if(node==null)
           return;

       let list ;
       if(ans.length==0 || ans.length-1<level){
           list = new Array();
           list.push(node.val);
           ans.push(list);
       }else{
           list = ans[level];
           list.push(node.val);

       }
       helper(node.left,ans,level+1);
       helper(node.right,ans,level+1);
    }
    return ans;
};
```

## 将有序数组转换为二叉搜索树

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/7/trees/51/

> [返回导航](#树)

>> 我的答案

```js
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {number[]} nums
 * @return {TreeNode}
 */
var sortedArrayToBST = function(nums) {
  if (nums.length === 0) {
    return null
  } else if (nums.length === 1) {
    return new TreeNode(nums[0])
  } else {
    var mid = parseInt(nums.length / 2)

    var root = new TreeNode(nums[mid])

    root.left = sortedArrayToBST(nums.slice(0, mid))
    root.right = sortedArrayToBST(nums.slice(mid + 1))
    return root
  }
}

```