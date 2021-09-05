# BFS

## 一般使用场景
1. finding the **shortest distance** between two vertices (根据一定变换规则，从初始状态到终止状态最少需要多少步)
2. graph of unknown size, e.g. word ladder, or even infinite size, e.g. knight shortest path  
3. 分层遍历 (Graph or Tree, Level order traversal)
4. 连通块
5. 拓扑排序

## 复杂度
T = O(n + m), where n is the # of vertices, m is the # of edges  
S = O(n)

## 一般思路
1）找到遍历的起点，放进queue里
2）确定遇到target的返回条件
3）变换规则（上下左右，东南西北，正交）
4）符合变换规则的valid node才会被放进queue里（不超过图的constraints, 没有被visit过，防止走回头路，这样才能保证最小步数）
5）视情况需要看是否需要层次遍历

## 模板
关键分析，bfs其实就是在做两件事：
1. bfs: uses a queue to keep track of nodes to be visited
2. get_neighbors: returns a node's neighbors. In an adjacency list representation, this would be returning the list of neighbors for the node. If the problem is about a matrix, this would be the surrounding valid cells as we will see in number of islands and knight shortest path. If the graph is implicit, we have to generate the neighbors as we traverse. We will see this in word ladder.

从start到target求最小步数
```
from collections import deque
def bfs(start, target):
  queue = deque([start])
  visited = set(start)
  step = 0
  while queue:
    size = len(queue)
    for _ in range(size):
      node = queue.popleft()
      if node == target:
        return step
      for neignbor in get_neighbors(node):
        if neighbor not in visited:
          queue.append(neighbor)
          visited.add(neighbor)
    # 遍历完一层
    step += 1
      
```
get_neighbors()多用在遍历matrix上，方便一点:
```
directions = [(-1, -1), (-1, 0), (-1, 1), (0, 1), (0, -1), (1, 0), (1, -1), (1, 1)]
        
        def get_neighbors(row, col):
            for dx, dy in directions:
                new_row = row + dx
                new_col = col + dy
                if not (0 <= new_row <= max_row and 0 <= new_col <= max_col and grid[new_row][new_col] == 0):
                    continue
                yield (new_row, new_col)
```
对于directions的计算也可以写成下面这样：
```
def get_neighbors(coord):
        res = []
        row, col = coord
        delta_row = [-1, 0, 1, 0]    #重点记录
        delta_col = [0, 1, 0, -1]    #重点记录
        for i in range(len(delta_row)):
            r = row + delta_row[i]
            c = col + delta_col[i]
            if 0 <= r < num_rows and 0 <= c < num_cols:
                res.append((r, c))
        return res
```

整体观看，以[Shortest Path in Binary Matrix](https://leetcode.com/problems/shortest-path-in-binary-matrix/)为例：
下面是把距离带进queue的写法，每次遇到一个node都要更新它的distance。而模板写法是，遍历完每一层再更新当前层的distance
```
from collections import deque
class Solution:
    def shortestPathBinaryMatrix(self, grid: List[List[int]]) -> int:
        max_row = len(grid) - 1
        max_col = len(grid[0]) -1
        directions = [(-1, -1), (-1, 0), (-1, 1), (0, 1), (0, -1), (1, 0), (1, -1), (1, 1)]
        
        def get_neighbors(row, col):
            for dx, dy in directions:
                new_row = row + dx
                new_col = col + dy
                if not (0 <= new_row <= max_row and 0 <= new_col <= max_col and grid[new_row][new_col] == 0):
                    continue
                yield (new_row, new_col)
                
        if grid[0][0] != 0 or grid[max_row][max_col] != 0:
            return -1
        
        queue = deque([(0, 0, 1)])
        visited = {(0, 0)}
        while queue:
            row, col, distance = queue.popleft()
            if (row, col) == (max_row, max_col):
                return distance
            for neighbor in get_neighbors(row, col):
                if neighbor in visited:
                    continue
                visited.add(neighbor)
                queue.append((*neighbor, distance + 1))
        return -1
```



