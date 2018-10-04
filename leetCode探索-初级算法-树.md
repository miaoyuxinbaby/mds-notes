# 树

> [二叉树的最大深度](#二叉树的最大深度) <br>


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