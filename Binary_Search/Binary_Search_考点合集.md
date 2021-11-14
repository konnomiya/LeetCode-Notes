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
