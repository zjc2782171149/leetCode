### 70.删除排序链表中的重复元素 II

难度：中等

给定一个已排序的链表的头 `head` ， *删除原始链表中所有重复数字的节点，只留下不同的数字* 。返回 *已排序的链表* 。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2021/01/04/linkedlist1.jpg)

```
输入：head = [1,2,3,3,4,4,5]
输出：[1,2,5]
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2021/01/04/linkedlist2.jpg)

```
输入：head = [1,1,1,2,3]
输出：[2,3]
```

 

**提示：**

- 链表中节点数目在范围 `[0, 300]` 内
- `-100 <= Node.val <= 100`
- 题目数据保证链表已经按升序 **排列**

<br></br>

<br></br>

# 解答

遍历一次 用图 存储值和对应的出现次数

后续遍历时如果出现次数不为1的，进行删除结点

删除的话，参照删除链表中的元素做法，新增头结点或递归的方法都行



### 新增头结点

#### 代码

```
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */
/**
 * @param {ListNode} head
 * @return {ListNode}
 */
var deleteDuplicates = function(head) {
    if(!head) return null;
    let p = head, map = new Map();
    while(p) {
        if(map.get(p.val)) {
            map.set(p.val, map.get(p.val) + 1)
        } else {
            map.set(p.val, 1);
        }
        p = p.next;
    }
    let newHead = new ListNode(0, head);
    p = newHead;
    while(p.next) {
        if(map.get(p.next.val) !== 1) {
            p.next = p.next.next;
        } else {
            p = p.next;
        }
    }
    return newHead.next;
};
```



#### 时间和空间复杂度

![image-20220217100044223](https://gitee.com/zjc13544361063/zjc-markdown-picture/raw/master/image-20220217100044223.png)

