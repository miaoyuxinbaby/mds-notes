# 链表

> [删除链表中的节点](#删除链表中的节点) <br>

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