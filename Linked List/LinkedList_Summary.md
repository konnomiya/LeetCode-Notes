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
2. left == 1的话，对于head,来说是翻转前n个节点，对于head.next来说，是从第**m-1**个元素开始翻转**n-1**个节点，以此类推  
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
### 合并两个有序链表 - LeetCode 21
最佳解法： 
1. 初始化一个dummy Node  
2. 比较l1 and l2 's val, 如果l1的值小于l2, 说明dummy接下来要连接的应该是l1, 反之亦然  
3. 如果还有剩就应该再连接剩下的  
* 注意：移动的不是dummy，是指向dummy的指针head  
```
def mergeTwoLists(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        dummy = curr = ListNode(-1)
        while l1 and l2:
            if l1.val < l2.val:
                curr.next = l1
                l1 = l1.next
            else:
                curr.next = l2
                l2 = l2.next
            curr = curr.next
        curr.next = l1 if l1 else l2
        return dummy.next
```
### 合并k个有序链表 - LeetCode 23
#### 分治做法
1. 分治做法是可以联系到上面的“合并两个有序链表”的  
2. 思路就是先分成两半，不断分成两半直到不能再分，再把所有链表先两两合并，在此之上继续两两合并，直到合成一条有序链表  
```
def mergeKLists(self, lists: List[Optional[ListNode]]) -> Optional[ListNode]:
        if not lists:
            return None
        if len(lists) == 1:
            return lists[0]
        mid = len(lists) // 2
        left, right = self.mergeKLists(lists[:mid]), self.mergeKLists(lists[mid:])
        return self.merge(left, right)
    
    def merge(self, l1, l2):
        dummy = curr = ListNode(-1)
        while l1 and l2:
            if l1.val < l2.val:
                curr.next = l1
                l1 = l1.next
            else:
                curr.next = l2
                l2 = l2.next
            curr = curr.next
        curr.next = l1 if l1 else l2
        return dummy.next
```
#### 用优先队列的做法
list里面有k个头结点，怎样快速找出值最小的那个节点？--> 用priorityqueue, 这样每次都能得出k个点里最小的那个.  

之后就是常规步骤：  
1. 得到最小的节点，然后移动指向dummy node的指针把它连上dummy node  
-> 因为我们最终的目标是返回dummy.next  
2. 移动1里面的指针  
3. 查看当前node.next是否为空，不为空的话就放进queue里继续比较  
```
def mergeKLists(self, lists: List[Optional[ListNode]]) -> Optional[ListNode]:
        class Wrapper():
            def __init__(self, node):
                self.node = node
                
            def __lt__(self, other):
                return self.node.val < other.node.val
        
        
        head = dummy = ListNode(-1)
        q = PriorityQueue()
        for l in lists:
            if l:
                q.put(Wrapper(l))
                       
        while not q.empty():
            node = q.get().node
            head.next = node
            head = head.next
            if node.next:
                q.put(Wrapper(node.next))
        return dummy.next
```
### 单链表的倒数第k个节点
1. 一个事实：倒数第k个节点，正数走n-k步可以到达  
2. 所以只要知道n再来一个for loop，正数也能找到。问题是对于链表要知道n，我们要先遍历一遍链表，这样就要two pass才能找到正数n-k的节点  
3. 所以one pass做法：先用1个pointer走k步，那么它剩下n-k步到达终点；此时再用另一个pointer从head开始跟前一个pointer一起走n-k步，那么第二个pointer最终会到达倒数第k个node
```
def findFromEnd(self, head, k):
        p1 = p2 = head
        for _ in range(k):
            p1 = p1.next
            
        while p1:
            p2 = p2.next
            p1 = p1.next
        return p2
```
### 删除倒数第k个节点 - LeetCode 19
1. 要删除第k个节点，我们就要找到倒数第k+1个节点，让它指向倒数第k-1个节点 
```
def removeNthFromEnd(self, head: Optional[ListNode], n: int) -> Optional[ListNode]:
        dummy = ListNode(-1)
        dummy.next = head
        prev = self.findFromEnd(dummy, n + 1)
        prev.next = prev.next.next
        return dummy.next
    
    def findFromEnd(self, head, k):
        p1 = p2 = head
        for _ in range(k):
            p1 = p1.next
            
        while p1:
            p2 = p2.next
            p1 = p1.next
        return p2
```
### 找到链表的中间节点 - LeetCode 876
1. 双指针经典题，fast指针以两倍速走，slow一倍速走，这样当fast走到终点时，slow就停在了middle
```
def middleNode(self, head: Optional[ListNode]) -> Optional[ListNode]:
        slow = fast = head
        while fast and fast.next:
            fast = fast.next.next
            slow = slow.next
        return slow
```
### 判断链表是否有环 - LeetCode 141
#### 简单做法
1. 遍历链表，用一个set来存储遇到的节点，如果再次遇到说明有环

#### 不用额外空间的做法
1. 快慢指针经典题，快指针走两步慢指针走一步，如果相遇说明有环
```
def hasCycle(self, head: Optional[ListNode]) -> bool:
        slow = fast = head
        while fast and fast.next:
            fast = fast.next.next
            slow = slow.next
            if slow == fast:
                return True
        return False
```
### 找有环链表的起点 - LeetCode 142
1. 这道题就包含了上一道题。假设相遇时慢指针走了k步，那么快指针一定走了2k步   
2. 假设环的起点到相遇点的距离是m,那么快指针要再走k-m步到达起点，而慢指针如果再从头开始走k-m步，也能走到起点  
3. 事实：k是环的长度的整数倍  
4. 背图！
```
def detectCycle(self, head: ListNode) -> ListNode:
        if not head:
            return None
        
        intersection = self.getIntersection(head)
        if not intersection:
            return None
        
        p1 = head
        p2 = intersection
        while p1 != p2:
            p1 = p1.next
            p2 = p2.next
        return p1
    
    def getIntersection(self, head):
        slow = fast = head
        while fast and fast.next:
            fast = fast.next.next
            slow = slow.next
            if slow == fast:
                return fast           
        return None
```
![image](https://user-images.githubusercontent.com/31752886/141183184-1c4b94e6-e10c-4ad9-b96f-2f14250b1808.png)

### 相交链表 - LeetCode 160
这道题相当神奇
#### 简单做法
跟判断有没有环类似，遍历一个链表，把遍历过的点放入set中，再遍历另一个链表，如果有相交点，就会在set里找到
#### 最优做法
1. 如果两个链表都不为空，我们分别遍历两个链表   
2. 如果其中遍历完短的，我们让该指针遍历长的;同理长的遍历完继续遍历短的    
3. 其实相当于补全了短的缺少的开头，就是这样最终两个指针能同时达到相交点  
4. 背图！ 
```
def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:
        if not headA or not headB:
            return None
        
        p1, p2 = headA, headB
        while p1 != p2:
            p1 = p1.next if p1 else headB
            p2 = p2.next if p2 else headA
        return p1
```
![image](https://user-images.githubusercontent.com/31752886/141187917-a255c754-bba2-4668-8f7e-eb5d1cd455c7.png)

### 复制一个链表 - LeetCode
#### 正常做法
1. 用一个hashmap建立就节点和新节点的对应关系
2. 遍历一遍旧链表，建立新链表
3. 再遍历一遍旧链表，把新链表的random的关系也连接上
```
    def copyRandomList(self, head: 'Node') -> 'Node':
        # key is old node, value is new node
        node_map = dict()
        dummy = Node(0, None, None)
        new_head, pointer = dummy, head
        while pointer:
            new_node = Node(pointer.val, pointer.next, None)
            node_map[pointer] = new_node
            new_head.next = new_node
            new_head = new_head.next
            pointer = pointer.next
        pointer = head
        while pointer:
            if pointer.random:
                node_map[pointer].random = node_map[pointer.random]
            pointer = pointer.next
        return dummy.next
```
