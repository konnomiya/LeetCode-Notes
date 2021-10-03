# Binary Search

单纯的二分查找已经基本能写对了。  

模板：

```
left, right = 0, len(arr) - 1
while left + 1 < right:
  mid = (left + right) // 2
  if ....:
    right = 
  else:
    left = ...
    
# check left and right
if arr[left]:
  return ...
if arr[right]:
  return ...
```
用left + 1 < right来写不容易出错。  
循环里左右指针的移动要判断哪一边包含答案，比如给定某个target，在一个sorted list里找出最后一个最大的比这个target小的数的index，  
如果arr[mid] > target, 说明arr[mid]不可能是答案，答案在mid的左边，移动右指针right = mid - 1，  
否则arr[mid] <= target, 说明arr[mid]可能是答案，答案在mid的右边，移动左指针left = mid  
最后检查区间两端，因为要求最后一个，所以先检查右端，arr[right] <= target的话就返回右指针，否则返回左指针。  


