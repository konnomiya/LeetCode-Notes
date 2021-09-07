# DFS on Tree
## 树里的应用场景
1. 遍历树，增删改查某个节点
2. Traverse with return value(finding max subtree, detect balanced tree, 这俩没记错的话是返回height然后判断)

## 一般过程分析
### Think Like A Node
当我们只是一个node的时候，我们只知道两个条件：
1. node.value
2. how to get to your children

### general模板
```
def dfs(node, state):
  if not node:
    # do something
    return 
    
  left = dfs(node.left, state)
  right = dfs(node.right, state)
  
  # do something
  return ...
```

## 思考 —— 怎么定义一个recursive function?
**1. Return Value(Passing value up from child to parent)**  
What do we want to return after visiting a node. 例如，max depth problem里我们返回the max depth for the current node's subtree -> 以当前节点为跟的子树的最大深度。  
Use return value to pass information from children to parent.

**2. Identify State(s)(Passing value down from parent to child)**  
What states do we need to maintain to compute the return value for the current node. For example, to know if the current node's value is larger than its parent, 
we have to maintain the parent's value as state. States becomes DFS's function arguments. Use states to pass information from parent to children.

## Coding 的两种形式(or 模板)：Using return value VS. Global variable  
以找二叉树的最大值为例：  
1. Using return value (divide and conquer 分治)  
One way to solve it is to use return value to pass the maximum value we have encountered back to parent node, and let the parent node compare it with the return value from the other child. This is more of a divide and conquer approach.  
翻译一下，这种写法的思路就是：这个function本身返回的值代表“以该节点为根的子树的最大值”，那么我们知道了左子树和右子树分别的最大值后，跟当前(根)的值相比，就知道了整棵树的最大值。
```
def dfs(node):
  if not node:
    return MIN_VALUE
    
  lef_max_value = dfs(node.left)
  right_max_value = dfs(node.right)
  return max(node.val, left_max_value, right_max_value)
```

2. Using global variable
Another way to solve it is to traverse the tree while keeping a global variable that keeps track of the maximum value we have encountered. After the dfs, we return the global variable.  
翻译一下，这种写法的思路是，我们用一个global variable(比如 res)去维持、更新递归过程中遇到的最大值，当从根层层traverse完它的左右子树后，我们返回res的最终结果。
```
def get_max_val(root): #这是外层的总function 
  res = MIN_VALUE
  def dfs(node):
    if not node:
      return
    if node.val > res:
      res = node.val
      
    #recurse
    dfs(root.left)
    dfs(root.right)
    
  dfs(root) # 调用dfs function
  return res
```

3. 优缺点  
It's more of a personal preference which one you use. One could argue global variables are bad and therefore the divide and conquer. However, sometimes it's easier to use a global variable. Recall that divide and conquer has two steps - partition and merge. If the merge step is complex, then using a global variable might simplify things.  
分治有两步：partition and merge，如果merge 这一步很复杂，那么用global variable会简单一点。



 
