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
