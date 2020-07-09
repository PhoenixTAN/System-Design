# K way merge algorithm
K路归并算法
Leetcode 23. Merge k sorted lists

Merge k sorted linked lists and return it as one sorted list. Analyze and describe its complexity.

Example:
```
Input:
[
  1->4->5,
  1->3->4,
  2->6
]
Output: 1->1->2->3->4->4->5->6
```

## 方法一
从每个列表的头拿出最小的。

时间复杂度：O(kN)

## 方法二 优先队列
全都塞进优先队列。

时间复杂度：KNlog(KN)

## 方法三 优化的优先队列
维护一个长度为k的优先队列。
所有链表的元素之和为N.
while循环执行O(N)次.
时间复杂度：O(Nlogk)，这个logk是排序的时间代价。
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    private Comparator<ListNode> ListNodeComparaotr = new Comparator<ListNode>() {
        @Override
        public int compare(ListNode node1, ListNode node2) {
            return node1.val - node2.val;   // 从小到大
        }
    };

    public ListNode mergeKLists(ListNode[] lists) {
        if ( lists == null || lists.length == 0 ) {
            return null;
        }

        Queue<ListNode> heap = new PriorityQueue<ListNode>(lists.length, ListNodeComparaotr);

        // initialize the heap
        for( int i = 0; i < lists.length; i++ ) {
            if ( lists[i] != null ) {
                heap.add(lists[i]);
            }
        }

        ListNode dummy = new ListNode(0);
        ListNode tail = dummy;
        while ( !heap.isEmpty() ){
            ListNode head = heap.poll();
            tail.next = head;
            tail = head;
            if ( head.next != null ) {
                heap.add(head.next);
            }
        }
        return dummy.next;
    }
}
```

## 分而治之
![alt text](./images/k-merge-divide-conquer.png)

时间复杂度分析：
设每个链表有N个元素，一共k个链表。
第一次合并：k/2对链表，每个链表长度为N，一共操作次数为2N.一共操作 k/2 * 2N = kN 次。

第二次合并：k/4对链表，每个链表长度为4N，一共操作次数为2N.一共操作 k/4 * 4N = kN 次。

一共进行logk次，每次都是kN次操作。

整体时间复杂度O(kNlogk).

### 直接自底向上
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {

    public ListNode mergeKLists(ListNode[] linkLists) {
        if ( linkLists == null || linkLists.length == 0 ) {
            return null;
        }
        List<ListNode> lists = new ArrayList<ListNode>(linkLists.length);
        for ( int i = 0; i < linkLists.length; i++ ) {
            lists.add(linkLists[i]);
        }

        while ( lists.size() > 1 ) {
            List<ListNode> newList = new ArrayList<ListNode>();
            for ( int i = 0; i < lists.size() - 1; i += 2 ) {
                ListNode mergedList = mergeTwoLists(lists.get(i), lists.get(i+1));
                newList.add(mergedList);
            }
            if ( lists.size() % 2 == 1 ) {
                newList.add(lists.get(lists.size() - 1));
            }
            lists = newList;
        }

        return lists.get(0);
    }

    /**
    * merge two sorted lists in place
    */
    private ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode head = new ListNode(0);
        ListNode tail = head;
        while ( l1 != null && l2 != null ) {
            if ( l1.val < l2.val ) {
                tail.next = l1;
                tail = l1;
                l1 = l1.next;
            }
            else {
                tail.next = l2;
                tail = l2;
                l2 = l2.next;
            }
        }
        if ( l1 != null ) {
            tail.next = l1;
        }
        if ( l2 != null ) {
            tail.next = l2;
        }
        return head.next;
    }
}
```

### 采用类似归并排序的方法 -- 自顶向下


## 归并排序对比

