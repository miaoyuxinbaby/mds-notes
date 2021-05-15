call 和 apply 的共同点
它们的共同点是，都能够改变函数执行时的上下文，将一个对象的方法交给另一个对象来执行，并且是立即执行的。

# 缓存算法 LRU

举一个很简单的例子，比如说我们打开淘宝首页会看到很多商品的推荐。其实推荐商品的流程是非常复杂的，首先要根据一定的策略去商品库当中召回商品。比如根据用户之前的行为召回和历史行为相关的商品，召回了商品之后还要进行清洗，过滤掉一些明确不感兴趣或者是非法、违规的商品。过滤了之后需要使用机器学习或者是深度学习的模型来进行点击率预测，从而将发生点击可能性最高的商品排在前面。

比如推荐商品列表其实也是分展位的，有些位置的商品是运营配好的，有些位置固定展示的是广告。广告往往也有自己的一条链路，还有些位置有一些其他的逻辑。这些商品的数据都拿到了之后，还要获取图片以及其他一些零零散散的信息，最后才能展示出来。因此大家可以试一下打开淘宝首页要比打开百度首页慢得多，因为这中间的链路实在是太长了。

我们很容易发现，对于一些经常打开淘宝的用户来说，其实没有必要每一次都把完整的链路走一遍。我们大可以把之前展示的结果存储在内存里，下一次再遇到同样请求的时候，直接从内存当中读取并且返回就可以了。这样可以节省大量的时间以及计算资源，比如在大促的时候，就可以把计算资源节省下来用在更加需要的地方。

缓存虽然好用，但是也不是万能的，因为内存是很贵的，我们不可能把所有数据都存在内存里。内存里只能放一些我们认为比较高价值的数据，在这种情况下，计算科学家们想出了种种策略来调度缓存，保持缓存当中数据的高价值。LRU 就是其中一种比较常用的策略。

LRU（Least recently used，最近最少使用）算法。该算法的观点是，最近被访问的数据那么它将来访问的概率就大，缓存满的时候，优先淘汰最无人问津者

总结一下特点：

保证缓存的读写效率，比如读写的复杂度都是 O(1)
当一条缓存当中的数据被读取，将它最近使用的时间更新
当插入一条新数据的时候，弹出更新时间最远的数据

## LRU 原理

我们仔细想一下这个问题会发现好像没有那么简单，显然我们不能通过数组来实现这个缓存。因为数组的查询速度是很慢的，不可能做到 O(1)。其次我们用 HashMap 好像也不行，因为虽然查询的速度可以做到 O(1)，但是我们没办法做到更新最近使用的时间，并且快速找出最远更新的数据。

如果是在面试当中被问到想到这里的时候，可能很多人都已经束手无策了。但是先别着急，我们冷静下来想想会发现问题其实并没有那么模糊。首先 HashMap 是一定要用的，因为只有 HashMap 才可以做到 O(1)时间内的读写，其他的数据结构几乎都不可行。但是只有 HashMap 解决不了更新以及淘汰的问题，必须要配合其他数据结构进行。这个数据结构需要能够做到快速地插入和删除，其实我这么一说已经很明显了，只有一个数据结构可以做到，就是链表。

链表有一个问题是我们想要查询链表当中的某一个节点需要 O(n)的时间，这也是我们无法接受的。但这个问题并非无法解决，实际上解决也很简单，我们只需要把链表当中的节点作为 HashMap 中的 value 进行储存即可，最后得到的系统架构如下：

[](https://pic4.zhimg.com/80/v2-98b6c3d744a74c9b639ef6734bc5eb23_1440w.jpg)

对于缓存来说其实只有两种功能，第一种功能就是查找，第二种是更新。

### 查找

查找会分为两种情况，第一种是没查到，这种没什么好说的，直接返回空即可。如果能查到节点，在我们返回结果的同时，我们需要将查到的节点在链表当中移动位置。我们假设越靠近链表头部表示数据越旧，越靠近链表尾部数据越新，那么当我们查找结束之后，我们需要把节点移动到链表的尾部。

移动可以通过两个步骤来完成，第一个步骤是在链表上删除该节点，第二个步骤是插入到尾部

### 更新

更新也同样分为两种情况，第一种情况就是更新的 key 已经在 HashMap 当中了，那么我们只需要更新它对应的 Value，并且将它移动到链表尾部。对应的操作和上面的查找是一样的，只不过多了一个更新 HashMap 的步骤。

第二种情况就是要更新的值在链表当中不存在，这也会有两种情况，第一个情况是缓存当中的数量还没有达到限制，那么我们直接添加在链表结尾即可。第二种情况是链表现在已经满了，我们需要移除掉一个元素才可以放入新的数据。这个时候我们需要删除链表的第一个元素，不仅仅是链表当中删除就可以了，还需要在 HashMap 当中也删除对应的值，否则还是会占据一份内存。

因为我们要进行链表到 HashMap 的反向删除操作，所以链表当中的节点上也需要记录下 Key 值，否则的话删除就没办法了。删除之后再加入新的节点

```js
class LinkedList {
  constructor() {
    // head 指针指向表头元素，即为最常用的元素
    // 双向链表，收尾皆为纯指针
    let head = {
      key: 'header',
      value: null,
      next: null,
      prev: null,
    }
    let tail = {
      key: 'tail',
      value: null,
      next: null,
      prev: null,
    }

    head.next = tail
    tail.prev = head

    this.linked_list = head
  }

  // 将node节点添加在链表尾部
  append(node) {
    // 找到最后一个实体
    let prev = this.tail.prev

    // 先修改当前节点和前一个节点的双向交接链
    node.prev = prev
    node.next = prev.next
    prev.next = node

    // 最后修改尾指针的prev为新节点
    node.next.prev = node
  }

  delete(node) {
    prev = node.prev
    next = node.next
    next.prev = prev
    prev.next = next
  }

  get_head() {
    return this.head.next
  }
}

class LruCache {
  constructor(limit) {
    this.limit = limit || 10
    let linked_list = new LinkedList()

    this.cacheMap = {}
  }
  get(key) {
    if (!this.cacheMap[key]) return null

    this.put_recently(key)

    return this.cacheMap[key]
  }

  put_recently(key) {
    let node = this.cacheMap[key]

    this.linked_list.delete(node)
    this.linked_list.append(node)
  }

  set(key, value) {
    // 能查到的话先删除原数据再更新
    if (this.cacheMap[key]) {
      this.linked_list.delete(this.cache[key])
      this.cache[key] = {
        key: key,
        value: value,
        next: null,
        prev: null,
      }
      this.linked_list.append(this.cache[key])
      return
    }

    // 如果容量已经满了，删除最旧的节点
    if (Object.keys(this.cacheMap).length >= this.limit) {
      let node = this.linked_list.get_head()
      this.linked_list.delete(node)
      delete this.cacheMap[node.key]
    }

    let item = {
      key: key,
      value: value,
      next: null,
      prev: null,
    }

    this.linked_list.append(item)
    this.cache[key] = item
  }
}

module.exports = LruCache
```

在这种实现当中我们没有用除了 dict 之外的其他任何工具，连 LinkedList 都是自己实现的。

实际上在 Python 语言当中有一个数据结构叫做 OrderedDict，它是一个字典，但是它当中的元素都是有序的。我们利用 OrderedDict 来实现 LRU 就非常非常简单，代码量也要少得多。
