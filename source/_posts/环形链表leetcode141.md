---
layout: w
title: 环形链表leetcode141
date: 2019-10-12 16:29:57
tags: leetcode
---
# 环形链表

面试中经常会考察的一种题型

给定一个链表，判断链表中是否有环。

为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。
## 快慢指针
定义两个指针若两个指针相遇则存在空
```
bool hasCycle(struct ListNode *head)
{
    struct ListNode *slow = head;
	struct ListNode *fast = head;
	
	while(fast && fast->next)
	{
		slow = slow->next;
		fast = fast->next->next;
		
		if(slow == fast)
		{
			return true;
		}
	 } 
	 return false; 
}
```

# hash表
们遍历所有结点并在哈希表中存储每个结点的引用（或内存地址）。如果当前结点为空结点 null（即已检测到链表尾部的下一个结点），那么我们已经遍历完整个链表，并且该链表不是环形链表。如果当前结点的引用已经存在于哈希表中，那么返回 true（即该链表为环形链表）
```
public boolean hasCycle(ListNode head) {
    Set<ListNode> nodesSeen = new HashSet<>();
    while (head != null) {
        if (nodesSeen.contains(head)) {
            return true;
        } else {
            nodesSeen.add(head);
        }
        head = head.next;
    }
    return false;
}



class Solution {
public:
    bool hasCycle(ListNode *head) {
        if(head==nullptr || head->next == nullptr)
        {
            return false;
        }
        map<ListNode*,int> ptrArr;
        ptrArr[head] = 1;
        while(head)
        {
            head = head->next;
            if(ptrArr.count(head) == 1)
            {
                return true;
            }
            else
            {
                ptrArr[head] = 1;
            }
        }
        
        return false;
    }
};


```
# map count()的值只能是0 或者1

# map 使用下标索引可以获取该键所关联的值，若下标访问不存在的元素则会导致容器中添加一个新的元素，键即为下标的值

count 和find 用于检查某个键是否存在而不会插入该键

默认下标访问插入的关联值为0

这种插入方式与insert（make_pair()）插入方式效果相同