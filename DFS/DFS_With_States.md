# DFS With States
## 应用 - Combinatorial search  
1) How many ways are there to arrange something  
2) Find all possible combinations of ...  
3) Find all solutions to a puzzle  

### Combinatorial search == DFS on tree
In combinatorial search problems, search space is in the shape of a tree. The tree that represents all the possible states is called a **State-space Tree**.  

Each node of the state-space tree represents a state we can reach in a combinatorial search (by doing a particular combination). Leaf nodes are the solutions to the problem (permutations in the above example).  

**Combinatorial search problems boil down to DFS/backtracking on the state-space tree.**  

Since the search space can be quite large, we often have to "prune" the tree, i.e. discard branches.

### Three steps to conquer combinatorial search problems
1. Identify the state(s)
2. Draw the state-space tree
3. DFS/backtrack on the state-space tree

In step 1, we want to answer the following two questions to identify the states:
1) What state do we need to know whether we have reached a solution (and using it to construct a solution if the problem asks for it). In permutation example,
we need to keep track of the letters we have already selected when we do DFS.   
比如permutation那道题，当len(permutation) == len(nums)时，我们决定res.append(list(permutation))

2) What state do we need to decide which child nodes should be visited next and which ones should be pruned. In the permutation example, we have to know what are the letters left that we can still use(since each letter can only be used once).

For step 2, once you draw the tree, the rest of the work becomes so much easier - simply traverse the tree depth-first. 

For step 3, apply template 套模板时间：
```
def dfs(node, state):
  if state is a solution:
    report(state)      # e.g. add state to final result list
    return
    
  for child in children:
    if child is a part of potential solution:
      state.add(child) # make move
      dfs(child, state)
      state.remove(child)  # backtrack
```

## Memoization - 优化上面的dfs
当有重复计算的state时，我们可以用一个map记录计算过的结果，当再次遇到相同的state就可以直接读取结果不必再计算了。
以 [Word Break](https://leetcode.com/problems/word-break/)为例：  
1）Identify the states  
To determine whether we have completely constructed target s, we have to find

What are the characters left to be matched using words in the list.  
To make a choice when we visit the current node's children, we don't need any additional states since we can use any word in the list unlimited times.  

2) Draw the space-state tree
![image](https://user-images.githubusercontent.com/31752886/132454278-15be82b0-9ba0-408d-bf6b-44d0e85b3cec.png)

3) DFS on the state tree:  
Using the backtracking template as a basis, we add the state we identified in step 1:

We use index i to record the current position in the target we have matched so far. s[:i] is matched and s[i:] is to be matched.  
思路就是记录当前index，意思是，如果i == len(s)，说明已经到了叶子节点，返回结果；如果需要检测匹配的字符串s[i:]以word list里的某一个word开头，说明找到了一个valid state，继续dfs, 如果剩下的字符串也能返回true直到叶子节点，说明找到了某条完整的valid path

```
def word_break(s, words):
    def dfs(i):
        if i == len(s): # we have constructed the entire target s
            return True

        for word in words:
            if s[i:].startswith(word): # is this a valid path
                if dfs(i + len(word)):
                      return True # any path leads to true is fine

        return False

    return dfs(0)
```

然而从图中可以看到，在state tree里有重复的state,说明可以用memo来减少重复计算
![image](https://user-images.githubusercontent.com/31752886/132454982-a485d5ca-92bd-43e5-aeec-f691c208701e.png)

这里memo的定义是：记录以index = i的字母开头的字符串是否有valid path的结果。因为我们只要找到一条valid path就可以了，所以可以提前break。
```
def word_break(s, words):
  memo = {}
  def dfs(i):
    if i == len(s):
      return True
      
    if i in memo:
      return memo[i]
      
    ok = False
    for word in words:
      if s[i:].startswith(word):
        if dfs(i + len(word)):
          ok = True
          break
    memo[i] = ok
    return ok
   return dfs(0)
```

Time Complexity: O(s * w * max(w[i]))

s is the lenght of the string, w is the length of the words array and max(wi) is the maximal possible word length. Here our time complexity is polynomial since we memoize and we iterate through the possibilities. At every position s we try every word in w which takes time proportionate to the word length.
