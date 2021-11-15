## Binary Search key points
1. When to terminate the loop?  
2. How to update left and right boundary in the if conditions  
3. Whether to include current element 
-> 比如，问greater than target 和 not smaller than target其实是有细微区别的，前者target不能是答案，后者target也可能是答案 
4. 注意search space, 应用题找boundary, no boundary 就自行决定boundary

## 最基本题 -- Find target in a sorted array
两种问法：  
1. 返回target所在的index值，否则返回-1  
2. 存在返回True, 不存在返回False
```
def binary_search(arr, target):
    left, right = 0, len(arr)-1 
    while left <= right:
        mid = (left + right)// 2
        if arr[mid] == target:
            return (True, mid)
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return (False, -1)
```
## 1. Find the first position of target in a sorted arry
写这道题的关键点是，这个问题按这种用boundary_index的结构写不能偷懒  
一定要写清楚nums[mid] == target, nums[mid] < target, nums[mid] > target要怎么移动  
**当且仅当** nums[mid] == target的时候我们才更新boundary_index  
然后因为要找first position，说明“虽然当前mid可能是答案，但我们可能可以找到一个更前的位置”，所以要移动右指针，搜索左边的范围找答案
```
def findFirstPosition(nums, target):
  left, right = 0, len(nums) - 1
  boundary_index = -1
  while left <= right:
    mid = (left + right) // 2
    if nums[mid] == target:
      boundary_index = mid
      right = mid - 1
    elif nums[mid] > target:
      right = mid - 1
    else:
      left = mid + 1
  return boundary_index
```
## 2. Find the last position of target in a sorted array 
思路跟上面类似  
**当且仅当** nums[mid] == target的时候我们才更新boundary_index  
因为要找last position，说明“虽然当前mid可能是答案，但我们可能可以找到一个更后的位置”，所以要移动左指针，搜索右边的范围找答案
```
def findLastPosition(nums, target):
  left, right = 0, len(nums) - 1
  boundary_index = -1
  while left <= right:
    mid = (left + right) // 2
    if nums[mid] == target:
      boundary_index = mid
      left = mid + 1
    elif nums[mid] < target:
      left = mid + 1
    else:
      right = mid - 1
 return boundary_index
```
## 3. Find Smallest element greater than target -> first true
```
def findFirstGreater(nums, target):
        left, right = 0, len(nums) - 1
        boundary_index = -1
        while left <= right:
            mid = (left + right) // 2
            if nums[mid] > target:
                boundary_index = mid
                right = mid - 1
            else:
                left = mid + 1
        return boundary_index
```
## 4. Find Smallest element not Smaller than target -> first true
greater than 和 not smaller than 只有一个区别，就是第一个判断的时候变成  
if nums[mid] >= target, 等于的时候也可以是答案
```
def findFirstNotSmaller(nums, target):
        left, right = 0, len(nums) - 1
        boundary_index = -1
        while left <= right:
            mid = (left + right) // 2
            if nums[mid] >= target:
                boundary_index = mid
                right = mid - 1
            else:
                left = mid + 1
        return boundary_index
```
## 5. Find largest element smaller than target -> last true
思路跟上面是类似的，只是因为找last true，当我们找到一个答案时，有可能找到更后的位置，所以要移动左边界
```
def findLargestSmaller(nums, target):
    left, right = 0, len(nums) - 1
    boundary_index = -1
    while left <= right:
        mid = (left + right) // 2
        if nums[mid] < target:
            boundary_index = mid
            left = mid + 1
        else:
            right = mid - 1
    return boundary_index
```
## 6. Find largest elemnt not greater than target -> last true
加个等于号改变condition即可
```
def findLargestNotGreater(nums, target):
    left, right = 0, len(nums) - 1
    boundary_index = -1
    while left <= right:
        mid = (left + right) // 2
        if nums[mid] <= target:
            boundary_index = mid
            left = mid + 1
        else:
            right = mid - 1
    return boundary_index
```
## 7. Peak of Mountain Array -> first true
想想怎样才能reduce to the our familiar problem  
peak点的特点是，nums[i] > nums[i+1]，也就是说如果我们apply a filter: if nums[i] > nums[i+1]  
可以得到两半，peak前的都是false,peak及以后的都是true  
-> find first true
一个corner case 要handle的是如果已经到了最后一个元素，把它也归到答案里，因为最后的元素无法比较nums[i+1], index会超出范围
```
def peakIndexInMountainArray(self, arr: List[int]) -> int:
        left, right = 0, len(arr) - 1
        boundary_index = -1
        while left <= right:
            mid = (left + right) // 2
            if mid == len(arr) - 1 or arr[mid] > arr[mid+1]:
                boundary_index = mid
                right = mid - 1
            else:
                left = mid + 1
        return boundary_index
```
## Minimum in rotated sorted array -> first true
这道题挺有意思的， 还是可以转化为找first true   
把最后一个元素作为pivot，那么整个array可以分为比这个pivot大和比这个pivot小   
找最小就是找比pivot小的里面的第一个  
apply filter: nums[mid] <= nums[-1]
```
def findMin(self, nums: List[int]) -> int:
        left, right = 0, len(nums) - 1
        index = -1
        while left <= right:
            mid = (left + right) // 2
            if nums[mid] <= nums[-1]:
                index = mid
                right = mid - 1
            else:
                left = mid + 1
        return nums[index]
```
## Search in rotated sorted array
```
def search(self, nums: List[int], target: int) -> int:
        if not nums:
            return -1
        
        left, right = 0, len(nums) - 1
        index = -1
        while left <= right:
            mid = (left + right) // 2
            if nums[mid] <= nums[right]:
                if nums[mid] <= target <= nums[right]:
                    index = mid
                    left = mid + 1
                else:
                    right = mid - 1
            else:
                if nums[left] <= target <= nums[mid]:
                    index = mid
                    right = mid - 1
                else:
                    left = mid + 1
        return index if nums[index] == target else -1
```
其实LC的官方答案写得更好，找到就直接返回
```
def search(self, nums: List[int], target: int) -> int:
        start, end = 0, len(nums) - 1
        while start <= end:
            mid = start + (end - start) // 2
            if nums[mid] == target:
                return mid
            elif nums[mid] >= nums[start]:
                if target >= nums[start] and target < nums[mid]:
                    end = mid - 1
                else:
                    start = mid + 1
            else:
                if target <= nums[end] and target > nums[mid]:
                    start = mid + 1
                else:
                    end = mid - 1
        return -1
```
## Search in 2D matrix - LeetCode 74
注意点，这道题的2Dmatrix的条件是每一行的第一个数一定大于前一行的最后一个数，所以其实是可以拉平成一条array的, 所以可以用二分
### 简单说一下为什么找点的坐标是mid // n, mid % n
想象一个m行n列的矩阵，第i个点的坐标是(a, b),怎么求a、b?  
关系式是a * n + b = i   
意味着，经过了a个整行，再加上平移b列位置    
所以a = i // n, b = i % n
```
def searchMatrix(self, matrix: List[List[int]], target: int) -> bool:
        m = len(matrix)
        n = len(matrix[0])
        
        left, right = 0, m * n - 1
        while left <= right:
            mid = (left + right) // 2
            pivot = matrix[mid // n][mid % n]
            if pivot == target:
                return True
            elif pivot > target:
                right = mid - 1
            else:
                left = mid + 1
        return False
```
## Search in 2D matrix II - LeetCode 240
这道题跟上一道题不同的是，这道题的矩阵是row-wise and column-wise sorted 而已，也就是某一行的第一个数完全可以小于上一行的最后一个数，所以不能从“拉平至一维”的角度思考        
最优解法如下：      
1. 选定从左下开始比较，对于每一个数只有两个方向可以走，一个是向上一个是向右   
2. 如果当前的数比target大，就往上走，因为右边的数一定比target更大   
3. 如果当前的数比target小，就往右走，因为上面的数一定比target更小   
4. 如果越界还没return就返回False咯     
-> 据说从右上开始也是可以的   
-> **LeetCode 74也可以用这个做法，因为那个矩阵隐含了列也是有序的的条件**
```
def searchMatrix(self, matrix: List[List[int]], target: int) -> bool:       
        m = len(matrix)
        n = len(matrix[0])
        
        row = m - 1
        col = 0
        
        # bottom left
        while col < n and row >= 0:
            if matrix[row][col] > target:
                row -= 1
            elif matrix[row][col] < target:
                col += 1
            else:
                return True
            
        return False
```
## Find the position of an element in a sorted array of infinite numbers - 无限式查找
据说M家面试出现过:), 要求能logn   
两个关键点：  
1. 找适合的end/right
2. 找到之后正常二分  
思路：
1. left = 0, right = 1, 每次将间隔扩大两倍，直到找到比target大的元素的位置
2. 这就有点像一个二叉树的每一层节点都是2^i个
```
def searchInInfiniteNumbers(nums, target):
    left, right = 0, 1
    while nums[right] < target:
        left = right
        right *= 2
        
    // 接下来就是普通的二分，找到target就返回
```
参考资料：
1. https://python.plainenglish.io/algorithm-pattern-binary-search-7352fbfc5a48   
2. https://blog.csdn.net/my9988/article/details/95336410
3. https://blog.51cto.com/neilhappy/1142075
## Capacity To Ship Packages Within D Days - 应用题 LeetCode 1011
思路：  
写了[memo](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/discuss/1576716/Python-solution-using-binary-search-memo)
基本思路就是想象有一个capacity的array, 下界是max(weights)，上界是sum(weights)，要在这个array找到合适点，使得req_days <= d days   
如果我们有一个helper function帮助filter: req_days <= d days, 再次可以得到FFFTTTTT   
所以转化成find first true了

对于feasible函数的思路，其实也不难
1. 我们的目的是计算当前选定的max_weight所需的天数req_days, 验证req_days <= days
2. 如果capacity能够载，就shrink by weights[i]
3. 如果不能，说明要重开一天，capacity reset to max_weight
```
def feasible(weights, max_weight, days):
            req_days = 1
            capacity = max_weight
            for weight in weights:
                if weight > capacity:
                    req_days += 1
                    capacity = max_weight
                capacity -= weight
            return req_days <= days       
        
        min_capacity = max(weights)
        max_capacity = sum(weights)
        boundary_index = max_capacity
        while min_capacity <= max_capacity:
            mid_point = (min_capacity + max_capacity) // 2
            if feasible(weights, mid_point, days):
                boundary_index = mid_point
                max_capacity = mid_point - 1
            else:
                min_capacity = mid_point + 1
        return boundary_index
```
# Koko Eating Bananas - LeetCode 875
这道题的框架跟上面是基本一样的，就是计算req_hours的时候还不用那么麻烦，因为题目说了pile < k，koko就不吃了，用ceiling就可以算出来  
```
def minEatingSpeed(self, piles: List[int], h: int) -> int:
        def feasible(piles, max_cap, h):
            req_hours = 0
            for pile in piles:
                req_hours += math.ceil(pile / max_cap)
            return req_hours <= h       
        
        min_cap = 1
        max_cap = sum(piles)
        res = max_cap
        while min_cap <= max_cap:
            mid = (min_cap + max_cap) // 2
            if feasible(piles, mid, h):
                res = mid
                max_cap = mid - 1
            else:
                min_cap = mid + 1
        return res
```
## Single Element in a sorted array - 神奇题，隔空二分 LeetCode 540
这道题告诉我们新思路，移动search space不一定是+1 or -1，还可以+2-2   
按之前的模板的话，要handle mid == len(nums) - 1，因为最后一个元素没有nums[mid+1]   
官方给的题解写得更漂亮其实  
思路是：  
1. 只在even index上面二分，在遇到single element之前，对于even index的数都满足nums[i] == nums[i+1],遇到single element之后就不满足了   
2. 所以如果apply filter if nums[i] != nums[i+1]之后，又变成find first true了  
3. 关键我们还是要在even index上loop
```
def singleNonDuplicate(self, nums: List[int]) -> int:
        left, right = 0, len(nums) - 1
        index = -1
        while left <= right:
            mid = (left + right) // 2
            if mid % 2 == 1:
                mid -= 1
            if mid == len(nums) - 1 or nums[mid] != nums[mid + 1]:
                index = mid
                right = mid - 2
            else:
                left = mid + 2
        return nums[index]
```
不同filter的同一效果
```
def singleNonDuplicate(self, nums: List[int]) -> int:
        left, right = 0, len(nums) - 1
        index = -1
        while left <= right:
            mid = (left + right) // 2
            if mid % 2 == 1:
                mid -= 1
            if mid == len(nums)-1 or nums[mid] == nums[mid+1]:
                left = mid + 2
            else:
                index = mid
                right = mid - 2
        return nums[index]
```
## Find the count of how many times a sorted array is rotated
这道题也是看别人整理的：https://python.plainenglish.io/algorithm-pattern-binary-search-7352fbfc5a48  
rotate其实就是把后面的数放到头，rotate k就是移动k次：  
[1, 2, 3, 4, 5]   
[5, 1, 2, 3, 4]  
[4, 5, 1, 2, 3]  
感觉只要找出peak index + 1就行了...



