- [x] #### [剑指 Offer 38. 字符串的排列](https://leetcode-cn.com/problems/zi-fu-chuan-de-pai-lie-lcof/)

```java
ListNode reverse(ListNode head) {
  if (head.next == null) return head;
  ListNode last = reverse(head.next);
  head.next.next = head;
  head.next = null;
  return last;
}
```

