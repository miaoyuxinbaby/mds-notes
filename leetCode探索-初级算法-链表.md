# 链表

> [删除链表中的节点](#删除链表中的节点) <br>
> [删除链表的倒数第N个节点](#删除链表的倒数第N个节点) <br>
> [反转链表](#反转链表) <br>
> [合并两个有序链表](#合并两个有序链表) <br>
> [回文链表](#回文链表) <br>

## 删除链表中的节点

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/6/linked-list/41/

> [返回导航](#链表)

>> 我的答案

```js
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} node
 * @return {void} Do not return anything, modify node in-place instead.
 */
// 非末尾，说明一定可以找到下一个节点  只给了当前要删除的节点，不知道前面一个节点
var deleteNode = function(node) {
    // 12345  12445
    // 原来 3 next 是4，现在3next是5 所以，4和这个链表没关系了，会被gc回收
    node.val = node.next.val
    node.next = node.next.next
};
```

>> 别人的答案

```js
  // 大家的代码一毛一样。。。
```

> [删除链表的倒数第N个节点](#删除链表的倒数第N个节点) <br>

## 删除链表的倒数第N个节点

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/6/linked-list/42/

> [返回导航](#链表)

>> 我的答案

```js
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} head
 * @param {number} n
 * @return {ListNode}
 */

var removeNthFromEnd = function(head, n) {
  // 只有一个节点，肯定是删除自己
  if (head.next === null) {
    head = null
    return head
  }

  // 第一个和第n + 1个
  let node1 = head
  let node2 = head
  while (n--) {
    // 第n+1个不存在就把第一个节点删掉
    if (node2.next) {
      node2 = node2.next
    } else {
      node1.val = head.next.val
      node1.next = head.next.next
      return node1
    }
  }

  // 如果node2是最后一个节点，就把node1的下一个节点删掉
  while (true) {
    if (node2.next === null) {
      node1.next = node1.next.next
      return head
    }

    node1 = node1.next
    node2 = node2.next
  }
};
```

>> 别人的答案

```js
// 整体的逻辑比我的清晰，思路是一致的
var removeNthFromEnd = function (head, n) {
    if (!head) {
        return null;
    }

    // 规范化链表，链表应该有一个头指针指向第一个节点。这个头指针没有value
    var dummyHead = new ListNode(-1);
    dummyHead.next = head;

    // 跟我一样， 确定双指针的起点
    var p1 = dummyHead;
    for (var i = 0; i < n; i++) {
        p1 = p1.next;
    }

    // n超过总长度，直接返回
    if (p1 == null) {
        return head;
    }

    // p1、p2同步后移
    var p2 = dummyHead;
    while (p1.next != null) {
        p1 = p1.next;
        p2 = p2.next;
    }

    var delNode = p2.next;
    p2.next = delNode.next;

    return dummyHead.next;
};

// 这不是1遍遍历，而是两遍， 但也是一种思路
var removeNthFromEnd = function (head, n) {
    if (!head) {
        return null;
    }

    var dummyHead = new ListNode(-1);
    dummyHead.next = head;

    // 先遍历一遍链表，获取总长度
    var listLength = 0;
    var h = dummyHead.next;

    while (h != null) {
        listLength++;
        h = h.next;
    }

    // n大于链表长度，直接返回
    if (n > listLength) {
        return head;
    }

    // 删除倒数第n个节点，即删除正数第listLength - n个节点
    var cur = dummyHead;
    for (var i = 0; i < listLength - n; i++) {
        cur = cur.next;
    }
    var delNode = cur.next;
    cur.next = delNode.next;

    return dummyHead.next;
};
```


> [反转链表](#反转链表) <br>

## 反转链表

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/6/linked-list/43/

> [返回导航](#链表)

>> 我的答案

```js
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} head
 * @return {ListNode}
 * 取巧了，因为是单链表，我直接把值替换了。。。
 */
var reverseList = function(head) {
  if (!head || !head.next) return head
  let len = 0
  let valList = []
  let currentNode = head
  while (currentNode.next) {
    valList.push(currentNode.val)
    currentNode = currentNode.next
    len++
  }

  valList.push(currentNode.val)

  currentNode = head
  while (currentNode.next || len) {
    currentNode.val = valList[len]
    currentNode = currentNode.next
    len--
  }

  currentNode.val = valList[len]

  return head
};

```

```js
var reverseList = function(head) {
  if (!head || !head.next) return head
  new_head = reverseList(head.next)
  // 把当前节点变成下一个节点的next
  head.next.next = head
  // 因为当前节点的next是链接原链表的，所以要断掉当前节点的next
  head.next = null
  return new_head
};

var reverseList = function(head) {
  // 三指针换位
  let prev = null;
  let node = head;
  let tempNext;
  while (node) {
    // 前面的节点变成当前节点的next，再把当前节点赋值给prev 最后把当前节点切换为下一个
    // 要缓存下一个节点，不然就丢了
    tempNext = node.next;
    node.next = prev;
    prev =  node;
    node = tempNext;
  }
  return prev;
};
```

> [合并两个有序链表](#合并两个有序链表) <br>

## 合并两个有序链表

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/6/linked-list/44/

> [返回导航](#链表)

>> 我的答案

```js
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} l1
 * @param {ListNode} l2
 * @return {ListNode}
 */
var mergeTwoLists = function(l1, l2) {
  // 一个头指针
  let head = new ListNode(0)
  // 新链表的最后一个节点
  let current = head
  // l1,l2的指针（当前节点）
  let cl1 = l1
  let cl2 = l2

  // 2者同时有后续节点
  while (cl1 !== null && cl2 !== null) {    
    if (cl1.val < cl2.val) {
      current.next = new ListNode(cl1.val)
      // 更新指针至最后一个节点
      current = current.next
      // 修改当前节点
      cl1 = cl1.next
    } else if (cl1.val === cl2.val) {
      current.next = new ListNode(cl1.val)
      current = current.next
      cl1 = cl1.next

      current.next = new ListNode(cl2.val)
      current = current.next
      cl2 = cl2.next
    } else {
      current.next = new ListNode(cl2.val)
      current = current.next
      cl2 = cl2.next
    }
  }

  // 因为是有序的，当其中一个结束时，另一个肯定是可以直接拿过来接到尾部的
  if (cl1 !== null) {
    current.next = cl1
  }

  if (cl2 !== null) {
    current.next = cl2
  }

  return head.next
};
```

>> 别人的

```js
// 递归的思路
var mergeTwoLists = function(l1, l2) {
  if (l1 == null) return l2
  if (l2 == null) return l1
  let newH = null
  if (l1.val < l2.val) {
      newH = l1
      newH.next = mergeTwoLists(l1.next, l2)
  } else {
      newH = l2
      newH.next = mergeTwoLists(l1, l2.next)
  }
  return newH
};
```

## 回文链表

> url: https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/6/linked-list/45/

> [返回导航](#链表)

>> 我的答案

```js

/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 * 第一次的答案，卒。看网上别人说的，取一般的链表，反转，前后同时进行可以避免
 * Line 15: RangeError: Maximum call stack size exceeded
 */
/**
 * @param {ListNode} head
 * @return {boolean}
 */
var isPalindrome = function (head) {
  // 先逆转一下，然后新的和旧的对比
  let oldList = JSON.parse(JSON.stringify(head))
  // 反转后的链表第一个节点
  let prev = null
  let node = head
  let tempNext
  while (node) {
    tempNext = node.next
    node.next = prev
    prev = node
    node = tempNext
  }

  // 原链表第一个节点
  while (oldList) {
    if (prev.val !== oldList.val) return false
    oldList = oldList.next
    prev = prev.next
  }

  return true
}

// 第二次，转成数组处理,性能好像不太好。。。。但也不算差
var isPalindrome = function (head) {
  // 先逆转一下，然后新的和旧的对比

  let arr = []
  let node = head
  while (node) {
    arr.unshift(node.val)
    node = node.next
  }

  let i = 0
  let node2 = head

  // 原链表第一个节点
  while (node2) {
    if (arr[i] !== node2.val) return false
    node2 = node2.next
    i++
  }

  return true
}
```