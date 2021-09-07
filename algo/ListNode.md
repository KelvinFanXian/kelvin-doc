- ##### 快慢指针 - 找到链表的后半部分

- ##### 链表反转 - 进行比较

```java
boolean isPalindrome(Node node){
  if (null==node || null == node.next) return true;
  Node fast = node, slow = node;
  while(fast.next != null && fast.next.next != null){
    fast = fast.next.next;
    slow = slow.next;
  }
  Node preNode = slow;
  Node firstNode = slow.next;
  Node curNode = slow.next.next;
  firstNode.next = null;
  while (null != curNode) {
    Node nextNode = curNode.next;
    curNode.next = preNode.next;
    curNode = nextNode;
  }
  slow = node;
  fast = preNode.next;
  while (null!= fast) {
    if (fast.data != slow.data) return false;
    slow = slow.next;
    fast = fast.next;
  }
  return true;
}
```





## 链表相关操作

- [206. 反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)

```
 class Solution {
 public:
   ListNode* reverseList(ListNode* head){
     ListNode* prev = nullptr;
     ListNode* curr = head;
     while(curr) {
       ListNode* next = curr->next;
       curr->next = prev;
       prev = curr;
       curr = next;
     }
     return prev;
   }
 };
 class Solution {
 public:
   ListNode* reverseList(ListNode* node){
     if(!head || !head->next) return head;
     ListNode* newHead = reverse(head->next);
     head->next->next = head;
     head->next = nullptr;
     return newHead;
   }
 };
```



- #### [141. 环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)

```
 class Solution {
 public:
   bool hasCycle(ListNode * head){
     if(!head||!head->next) return false;
     ListNode* fast = head->next; // 找环时，起点先一步？
     ListNode* slow = head;
     while (fast != slow) {
       if(!fast || !fast->next) return false;
       fast = fast->next->next;
       slow = slow->next;
     }
     return true;
   }
 }
```



- #### [21. 合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

```
 class Solution{
 public:
   ListNode* mergeTwoLists(ListNode* l1, ListNode* l2){
     if(!l1) return l2;
     else if(!l2) return l1;
     else if (l1->val < l2->val) {
       l1->next = mergeTwoLists(l1->next, l2);
       return l1;
     } else {
       l2->next = mergeTwoLists(l1, l2->next);
       return l2;
     }
   }
 };
 class Solution{
 public:
   ListNode* mergeTwoLists(ListNode* l1, ListNode* l2){
     ListNode* preHead = new ListNode(-1);
     
     ListNode* prev = preHead;
     while(l1 && l2) {
       if(l1->val < l2->val) {
         prev->next = l1;
         l1=l1->next;
       } else {
         prev->next = l2;
         l2=l2->next;
       }
       prev = prev->next;
     }
     prev->next=!l1?l2:l1;
     return preHead->next;
   }
 };
```

- #### [19. 删除链表的倒数第 N 个结点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

```
 class Solution{
 public:
   ListNode* removeNthFromEnd(ListNode* head, int n){
     ListNode* dummy = new ListNode(0, head);
     ListNode* first = head;
     ListNode* second = dummy; // second晚一步
     for(int i=0; i<n; ++i){
       first = first->next;
     }
     while(first){
       first = first->next;
       second = second->next;
     }
     second->next = second->next->next;
     ListNode* ans = dummy->next;
     delete dummy;
     return ans;
   }
 };
```



- #### [876. 链表的中间结点](https://leetcode-cn.com/problems/middle-of-the-linked-list/)

```
 class Solution{
 public:
   ListNode* middleNode(ListNode* head){
     ListNode* fast = head; // 找中点时起点相同
     ListNode* slow = head;
     while (fast && fast->next){
       fast=fast->next->next;
       slow=slow->next;
     }
     return slow;
   }
 };
```
