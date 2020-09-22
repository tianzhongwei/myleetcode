# 1 快慢指针 + 前驱法删除 + 虚拟头结点

------------
1) 虚拟头结点的意义：当目标结点为头结点时，目标节点没有前驱，前驱法删除失效。
2) 快慢指针的距离为 $n + 1$，即 `fast - slow = n + 1`
3) 快指针先向前移动 $n + 1$ 步
4) 前驱法绕过式删除
------------
```C++
ListNode* removeNthFromEnd(ListNode* head,int n) {
    ListNode* dummy = new ListNode(-1);
    dummy->next = head;
    
    ListNode* fast = dummy;     // 初始化：同时指向虚拟头结点
    ListNode* slow = dummy;     // 初始化：同时指向虚拟头结点

    // fast - slow = n + 1 区间 [fast,slow) 的结点个数为 n + 1
    for(int i = 0 ; i < n + 1 ; ++i)    // 快指针先行移动 n + 1 步
        fast = fast->next;
    
    while(fast != nullptr) {    // fast 和 slow 同时移动，直到 fast 指向空
        fast = fast->next;
        slow = slow->next;
    }
    slow->next = slow->next->next;  // 前驱绕过目标结点，前驱指针指向目标结点的后继结点
    
    return dummy->next;
}
```