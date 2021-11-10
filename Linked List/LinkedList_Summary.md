# Linked List
## 一些基本操作模板
### Reverse Linked List
#### 递归做法 
1. 注意一定要handle not head 和 not head.next, 否则遇到None会继续递归一次，可是None没有next,那么tail = self.reverseList(head.next)这一行就会报错  
2. 思路关键：相信整个函数会return一个已经翻转好的链表的头结点
3. return tail前那两行所对应的图要滚瓜烂熟
```
def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if not head or not head.next:
            return head
        
        tail = self.reverseList(head.next)
        head.next.next = head
        head.next = None
        return tail
```
![image](https://user-images.githubusercontent.com/31752886/141057136-c750652d-f835-4d07-bb04-ad01e8e63159.png)

#### 迭代做法
1. 口诀：cur.next = prev, prev = cur, cur = cur.next  
2. 迭代的思路其实是这样的：想象翻转两个节点a, b, a.next = b，prev指向的是a的前一个节点，a要指回prev, prev更新指向a, curr更新指向a的后一节点b  
3. 易错点：最后curr会指向None,而prev才是指向最后一个节点，即翻转好的链表的头结点，所以要返回prev  
```
def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        prev, curr = None, head
        while curr:
            curr.next, prev, curr = prev, curr, curr.next
        return prev
```

### 翻转链表的前N个节点
1. base case翻转本身，但是要记录successor  
2. head.next最后要跟successor连上  
3. 返回的是翻转的前n部分最后的头结点  
4. 背图！  
```
def reverseN(self, head, n):
        self.successor = None
        
        def reverseFirstN(head, n):
            if n == 1:
                self.successor = head.next
                return head
            
            last = reverseFirstN(head.next, n-1)
            head.next.next = head
            head.next = self.successor
            return last
        return reverseFirstN(head, n)

```
![image](https://user-images.githubusercontent.com/31752886/141057274-1f324aa0-1793-4361-95f1-cdfbc5419bfe.png)

### 翻转链表的一部分 - Leetcode 92
1. 题目要求翻转某个区间[left, right] or [m, n]之间的链表, 假设left = 1, 问题就变成了翻转前n个节点，所以这是base case  
2. left != 1的话，对于head,来说是翻转前n个节点，对于head.next来说，是从第**m-1**个元素开始翻转**n-1**个节点，以此类推  
```
def reverseBetween(self, head: Optional[ListNode], left: int, right: int) -> Optional[ListNode]:
        if left == 1:
            return self.reverseN(head, right)
        
        head.next = self.reverseBetween(head.next, left - 1, right - 1)
        return head
               
    def reverseN(self, head, n):
        self.successor = None
        
        def reverseFirstN(head, n):
            if n == 1:
                self.successor = head.next
                return head
            
            last = reverseFirstN(head.next, n-1)
            head.next.next = head
            head.next = self.successor
            return last
        return reverseFirstN(head, n)
        
```
### k个一组翻转链表 - Leetcode 25
1. 这个问题其实包含两个子问题：1）翻转两点之间的链表 2） 重复多次1）的step直到不满足条件  
2. 那么翻转a, b之间的链表应该怎么写呢？-> 翻转一整条链表其实是翻转a和None之间，那么翻转a和b之间只要把while循环的条件改掉即可，如下
```
def reverse(self, a, b):
        prev, curr = None, a
        while curr != b:
            curr.next, prev, curr = prev, curr, curr.next
        return prev
```
3. 那么子问题一解决，假设我们当前是a，怎么找到距离它k的节点b? 整一个range(k)的for 循环就可以了  
4. 我们相信整个函数能返回“以某个节点起始的翻转好了kgrop的链表的头结点”，所以a.next要等于它  
5. a和b之间经过上面的reverse函数已经翻转好了，假设a是最初的节点，想象一下，我们要返回的就是经过reverse函数翻转的新head  
```
def reverseKGroup(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
        if not head:
            return None
        
        a = b = head
        for _ in range(k):
            if not b:
                return head
            b = b.next
            
        new_head = self.reverse(a, b)
        a.next = self.reverseKGroup(b, k)
        return new_head
    
    def reverse(self, a, b):
        prev, curr = None, a
        while curr != b:
            curr.next, prev, curr = prev, curr, curr.next
        return prev
        
```
### 合并两个有序链表
