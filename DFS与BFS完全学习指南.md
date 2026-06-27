# 深度优先搜索（DFS）与广度优先搜索（BFS）完全学习指南

---

## 目录

- [第一部分：深度优先搜索（DFS）](#第一部分深度优先搜索dfs)
  - [1.1 核心概念](#11-核心概念)
  - [1.2 DFS 的实现方式](#12-dfs-的实现方式)
  - [1.3 DFS 代码模板](#13-dfs-代码模板)
  - [1.4 DFS 的关键技巧](#14-dfs-的关键技巧)
  - [1.5 DFS 的应用场景](#15-dfs-的应用场景)
  - [1.6 DFS 经典题目分类](#16-dfs-经典题目分类)
- [第二部分：广度优先搜索（BFS）](#第二部分广度优先搜索bfs)
  - [2.1 核心概念](#21-核心概念)
  - [2.2 BFS 的实现方式](#22-bfs-的实现方式)
  - [2.3 BFS 代码模板](#23-bfs-代码模板)
  - [2.4 BFS 的关键技巧](#24-bfs-的关键技巧)
  - [2.5 BFS 的应用场景](#25-bfs-的应用场景)
  - [2.6 BFS 经典题目分类](#26-bfs-经典题目分类)
- [第三部分：DFS vs BFS 全面对比](#第三部分dfs-vs-bfs-全面对比)
  - [3.1 核心区别](#31-核心区别)
  - [3.2 复杂度分析](#32-复杂度分析)
  - [3.3 如何选择 DFS 还是 BFS](#33-如何选择-dfs-还是-bfs)
  - [3.4 常见陷阱与易错点](#34-常见陷阱与易错点)
- [第四部分：分阶段学习计划](#第四部分分阶段学习计划)
  - [第一阶段：基础入门（第 1-3 天）](#第一阶段基础入门第-1-3-天)
  - [第二阶段：进阶应用（第 4-7 天）](#第二阶段进阶应用第-4-7-天)
  - [第三阶段：综合提升（第 8-10 天）](#第三阶段综合提升第-8-10-天)
  - [第四阶段：实战冲刺（第 11-14 天）](#第四阶段实战冲刺第-11-14-天)
- [附录：推荐刷题清单](#附录推荐刷题清单)

---

# 第一部分：深度优先搜索（DFS）

## 1.1 核心概念

### 什么是 DFS？

深度优先搜索（Depth-First Search）是一种**沿着一条路径尽可能深入探索，直到无法继续后再回溯**的图/树遍历算法。

### 核心思想

> **"一条路走到黑，不撞南墙不回头"**

- 策略：**优先向深度方向扩展**，而非广度方向
- 数据结构：**栈**（Stack）—— 递归调用栈 或 显式栈
- 本质：**图的遍历**，按深度优先的顺序访问所有节点

### DFS 的遍历过程（以树为例）

```
        1
       / \
      2   3
     / \   \
    4   5   6

DFS 遍历顺序（前序）：1 → 2 → 4 → 5 → 3 → 6
```

遍历路径：`1 → 2 → 4`（4 没有子节点，回溯）→ `5`（5 没有子节点，回溯）→ `2`（2 已遍历完，回溯）→ `1` → `3` → `6`

### DFS 的三种遍历顺序（以二叉树为例）

| 遍历方式 | 访问顺序 | 说明 |
|---------|---------|------|
| **前序遍历** | 根 → 左 → 右 | 先访问当前节点，再递归左右子树 |
| **中序遍历** | 左 → 根 → 右 | 先递归左子树，再访问当前节点，最后递归右子树 |
| **后序遍历** | 左 → 右 → 根 | 先递归左右子树，最后访问当前节点 |

---

## 1.2 DFS 的实现方式

### 方式一：递归实现（最常用）

```python
def dfs(node):
    if node is None:
        return
    # 处理当前节点
    visit(node)
    # 递归访问相邻节点
    for neighbor in node.neighbors:
        if not visited[neighbor]:
            visited[neighbor] = True
            dfs(neighbor)
```

**优点**：代码简洁直观，逻辑清晰
**缺点**：可能栈溢出（递归深度过大时），Python 默认递归深度约 1000

### 方式二：显式栈实现（迭代）

```python
def dfs_stack(start):
    stack = [start]
    visited[start] = True

    while stack:
        node = stack.pop()
        visit(node)

        # 注意：压栈顺序与期望访问顺序相反
        for neighbor in reversed(node.neighbors):
            if not visited[neighbor]:
                visited[neighbor] = True
                stack.append(neighbor)
```

**优点**：不会栈溢出，可处理更深的递归
**缺点**：代码稍复杂，需要手动管理栈

### 递归 vs 迭代 对比

| 对比项 | 递归 | 迭代（显式栈） |
|-------|------|--------------|
| 代码简洁度 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| 栈溢出风险 | 有（深度大时） | 无 |
| 执行效率 | 略低（函数调用开销） | 略高 |
| 适用场景 | 递归深度可控时 | 递归深度可能很大时 |

---

## 1.3 DFS 代码模板

### 模板一：图的 DFS 遍历

```python
def graph_dfs(graph):
    visited = set()

    def dfs(node):
        visited.add(node)
        # 处理当前节点
        process(node)

        for neighbor in graph[node]:
            if neighbor not in visited:
                dfs(neighbor)

    # 处理不连通图的情况
    for node in graph:
        if node not in visited:
            dfs(node)
```

### 模板二：回溯法（排列/组合/子集）

```python
def backtrack(路径, 选择列表):
    if 满足结束条件:
        结果.add(路径)
        return

    for 选择 in 选择列表:
        做选择(路径, 选择)
        backtrack(路径, 选择列表)
        撤销选择(路径, 选择)
```

**Python 实例 —— 全排列**：

```python
def permute(nums):
    result = []
    path = []
    used = [False] * len(nums)

    def backtrack():
        if len(path) == len(nums):
            result.append(path[:])
            return

        for i in range(len(nums)):
            if used[i]:
                continue
            used[i] = True
            path.append(nums[i])
            backtrack()
            path.pop()
            used[i] = False

    backtrack()
    return result
```

### 模板三：网格/矩阵中的 DFS

```python
def grid_dfs(grid, i, j):
    rows, cols = len(grid), len(grid[0])

    # 边界检查 + 条件检查
    if i < 0 or i >= rows or j < 0 or j >= cols:
        return
    if grid[i][j] != target_value:
        return
    if visited[i][j]:
        return

    visited[i][j] = True

    # 四个方向
    directions = [(0, 1), (0, -1), (1, 0), (-1, 0)]
    for dx, dy in directions:
        grid_dfs(grid, i + dx, j + dy)
```

### 模板四：带记忆化的 DFS（记忆化搜索 / 自顶向下 DP）

```python
def dfs_memo(state):
    if state in memo:
        return memo[state]

    if 满足边界条件:
        return base_value

    result = 0
    for next_state in possible_transitions(state):
        result = max(result, dfs_memo(next_state))

    memo[state] = result
    return result
```

---

## 1.4 DFS 的关键技巧

### 技巧一：回溯三步曲

1. **做选择**：将当前选择加入路径
2. **递归**：进入下一层决策树
3. **撤销选择**：将当前选择从路径中移除（恢复现场）

```python
for choice in choices:
    path.append(choice)       # 1. 做选择
    backtrack(path, ...)       # 2. 递归
    path.pop()                # 3. 撤销选择
```

### 技巧二：剪枝（Pruning）

在递归过程中**提前终止不可能产生正确答案的分支**，减少不必要的搜索。

```python
def backtrack(path, choices):
    if not is_valid(path):    # 剪枝：当前路径已不合法
        return

    if len(path) == target:
        result.append(path[:])
        return

    for choice in choices:
        if should_prune(choice):  # 剪枝：跳过无效选择
            continue
        path.append(choice)
        backtrack(path, choices)
        path.pop()
```

**常见剪枝策略**：
- 排序后跳过重复元素（去重）
- 提前判断剩余元素是否足够
- 利用上下界约束排除不可能的分支

### 技巧三：状态标记与去重

**方法 A**：使用 `visited` 数组/集合
```python
visited = set()
```

**方法 B**：原地修改（网格题常用）
```python
grid[i][j] = '#'  # 标记为已访问
# ... 递归 ...
grid[i][j] = original_value  # 恢复
```

**方法 C**：排序 + 跳过重复（组合/子集题常用）
```python
nums.sort()
for i in range(len(nums)):
    if i > 0 and nums[i] == nums[i-1] and not used[i-1]:
        continue  # 跳过重复
```

### 技巧四：递归深度控制

```python
import sys
sys.setrecursionlimit(100000)  # 增大递归深度限制
```

> ⚠️ Python 默认递归深度约 1000，处理大规模数据时需要调大或改用迭代。

---

## 1.5 DFS 的应用场景

| 应用领域 | 典型问题 | 说明 |
|---------|---------|------|
| **排列组合** | 全排列、组合、子集 | 回溯法是 DFS 的核心应用 |
| **连通性** | 连通分量、岛屿数量 | 判断图中节点是否连通 |
| **路径搜索** | 迷宫问题、路径是否存在 | 在图中寻找从起点到终点的路径 |
| **拓扑排序** | 课程安排、任务依赖 | 有向无环图的线性排序 |
| **环检测** | 判断图中是否有环 | 有向图/无向图的环检测 |
| **树的问题** | 树的遍历、最近公共祖先 | 二叉树/多叉树的各种操作 |
| **博弈论** | Nim 游戏、石子游戏 | 状态空间搜索 |
| **动态规划** | 记忆化搜索 | DFS + 缓存 = 自顶向下 DP |

---

## 1.6 DFS 经典题目分类

### 🟢 入门级

| 题目 | 平台 | 核心考点 |
|------|------|---------|
| 二叉树的前序遍历 | LeetCode 144 | 递归/迭代实现前序遍历 |
| 二叉树的中序遍历 | LeetCode 94 | 递归/迭代实现中序遍历 |
| 二叉树的后序遍历 | LeetCode 145 | 递归/迭代实现后序遍历 |
| 二叉树的最大深度 | LeetCode 104 | 递归求深度 |
| 相同的树 | LeetCode 100 | 递归比较两棵树 |
| 对称二叉树 | LeetCode 101 | 递归判断对称性 |
| 路径总和 | LeetCode 112 | 递归判断路径和 |

### 🟡 中级

| 题目 | 平台 | 核心考点 |
|------|------|---------|
| 全排列 | LeetCode 46 | 回溯法基础 |
| 子集 | LeetCode 78 | 回溯法 |
| 组合 | LeetCode 77 | 回溯法 + 组合数 |
| 电话号码的字母组合 | LeetCode 94 | 回溯 + 映射 |
| 岛屿数量 | LeetCode 200 | 网格 DFS + 连通分量 |
| 岛屿的最大面积 | LeetCode 695 | 网格 DFS + 面积计算 |
| 被围绕的区域 | LeetCode 130 | 网格 DFS + 边界处理 |
| 单词搜索 | LeetCode 79 | 网格 DFS + 回溯 |
| 二叉树的右视图 | LeetCode 199 | DFS + 层级信息 |
| 路径总和 II | LeetCode 113 | DFS + 路径记录 |
| 课程表 II | LeetCode 210 | DFS 拓扑排序 |
| 图中是否有环 | LeetCode 207 | DFS 环检测 |

### 🔴 高级

| 题目 | 平台 | 核心考点 |
|------|------|---------|
| N 皇后问题 | LeetCode 51 | 回溯 + 剪枝 |
| 解数独 | LeetCode 37 | 回溯 + 多维剪枝 |
| 单词搜索 II | LeetCode 212 | 回溯 + 前缀树（Trie） |
| 二叉树中的最大路径和 | LeetCode 124 | DFS + 后序遍历 |
| 课程表 IV | LeetCode 1462 | DFS + 传递闭包 |
| 删除无效的括号 | LeetCode 301 | 回溯 + 复杂剪枝 |
| 恢复二叉搜索树 | LeetCode 99 | DFS + 中序遍历性质 |

---

# 第二部分：广度优先搜索（BFS）

## 2.1 核心概念

### 什么是 BFS？

广度优先搜索（Breadth-First Search）是一种**按层次逐层向外扩展**的图/树遍历算法。

### 核心思想

> **"一层一层地探索，像水波纹一样扩散"**

- 策略：**优先向广度方向扩展**，先访问所有距离为 1 的节点，再访问距离为 2 的节点...
- 数据结构：**队列**（Queue）—— 先进先出（FIFO）
- 本质：**无权图的最短路径算法**

### BFS 的遍历过程（以树为例）

```
        1          ← 第 1 层
       / \
      2   3        ← 第 2 层
     / \   \
    4   5   6      ← 第 3 层

BFS 遍历顺序：1 → 2 → 3 → 4 → 5 → 6
```

### DFS vs BFS 遍历对比

```
        1
       / \
      2   3
     / \   \
    4   5   6

DFS（前序）：1 → 2 → 4 → 5 → 3 → 6    （一条路走到底）
BFS：       1 → 2 → 3 → 4 → 5 → 6    （逐层扩展）
```

---

## 2.2 BFS 的实现方式

### 方式一：使用队列（标准实现）

```python
from collections import deque

def bfs(start):
    queue = deque([start])
    visited = {start}

    while queue:
        node = queue.popleft()    # 队头出队
        visit(node)              # 处理当前节点

        for neighbor in node.neighbors:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)  # 邻居入队
```

### 方式二：分层 BFS（记录层级）

```python
def bfs_level(start):
    queue = deque([(start, 0)])  # (节点, 层级)
    visited = {start}

    while queue:
        node, level = queue.popleft()
        print(f"节点 {node} 在第 {level} 层")

        for neighbor in node.neighbors:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, level + 1))
```

### 方式三：逐层遍历（最常用写法）

```python
def bfs_by_level(root):
    queue = deque([root])

    while queue:
        level_size = len(queue)  # 当前层的节点数

        for _ in range(level_size):
            node = queue.popleft()
            visit(node)

            for child in node.children:
                if child:
                    queue.append(child)
```

---

## 2.3 BFS 代码模板

### 模板一：无权图最短路径

```python
from collections import deque

def shortest_path(graph, start, end):
    queue = deque([(start, 0)])  # (节点, 距离)
    visited = {start}

    while queue:
        node, dist = queue.popleft()

        if node == end:
            return dist  # 找到最短距离

        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, dist + 1))

    return -1  # 不可达
```

### 模板二：网格 BFS（最短路径）

```python
from collections import deque

def grid_bfs(grid, start, end):
    rows, cols = len(grid), len(grid[0])
    queue = deque([(start[0], start[1], 0)])  # (行, 列, 步数)
    visited = set()
    visited.add((start[0], start[1]))
    directions = [(0, 1), (0, -1), (1, 0), (-1, 0)]

    while queue:
        x, y, steps = queue.popleft()

        if (x, y) == end:
            return steps

        for dx, dy in directions:
            nx, ny = x + dx, y + dy
            if (0 <= nx < rows and 0 <= ny < cols
                and (nx, ny) not in visited
                and grid[nx][ny] == 0):  # 可通行
                visited.add((nx, ny))
                queue.append((nx, ny, steps + 1))

    return -1
```

### 模板三：多源 BFS

```python
from collections import deque

def multi_source_bfs(grid):
    rows, cols = len(grid), len(grid[0])
    queue = deque()
    dist = [[-1] * cols for _ in range(rows)]

    # 将所有起点同时入队
    for i in range(rows):
        for j in range(cols):
            if grid[i][j] == 0:  # 所有值为0的格子都是起点
                queue.append((i, j))
                dist[i][j] = 0

    directions = [(0, 1), (0, -1), (1, 0), (-1, 0)]

    while queue:
        x, y = queue.popleft()

        for dx, dy in directions:
            nx, ny = x + dx, y + dy
            if (0 <= nx < rows and 0 <= ny < cols
                and dist[nx][ny] == -1):
                dist[nx][ny] = dist[x][y] + 1
                queue.append((nx, ny))

    return dist
```

### 模板四：双向 BFS（优化最短路径搜索）

```python
from collections import deque

def bidirectional_bfs(graph, start, end):
    if start == end:
        return 0

    queue_start = deque([start])
    queue_end = deque([end])
    visited_start = {start: 0}
    visited_end = {end: 0}

    while queue_start and queue_end:
        # 每次扩展节点较少的一端
        if len(queue_start) <= len(queue_end):
            steps = bfs_expand(queue_start, visited_start, visited_end, graph)
        else:
            steps = bfs_expand(queue_end, visited_end, visited_start, graph)

        if steps is not None:
            return steps

    return -1
```

---

## 2.4 BFS 的关键技巧

### 技巧一：分层遍历

利用 `level_size = len(queue)` 在每一层开始时记录当前层的节点数，实现逐层处理。

```python
while queue:
    level_size = len(queue)
    for _ in range(level_size):
        node = queue.popleft()
        # 处理当前层的节点
```

> **适用场景**：二叉树的层序遍历、求最短路径的步数、按层统计等。

### 技巧二：多源 BFS

将**多个起点同时放入队列**，一次 BFS 就能求出所有点到最近起点的距离。

```python
# 初始化：所有起点入队
for source in sources:
    queue.append(source)
    dist[source] = 0
```

> **适用场景**：腐烂的橘子、01 矩阵、地图分析等。

### 技巧三：状态压缩 BFS

将复杂状态编码为整数或元组，作为 BFS 的节点。

```python
# 例如：用位掩码表示钥匙持有状态
# state = (x, y, keys_bitmask)
queue.append((start_x, start_y, 0))  # 0 表示没有钥匙
```

> **适用场景**：最短路径中钥匙和锁、复杂状态转移等问题。

### 技巧四：双向 BFS

从起点和终点**同时开始搜索**，当两端相遇时得到最短路径。

```python
# start_queue 和 end_queue 同时扩展
# 当某一端扩展到的节点在另一端的 visited 中时，找到答案
```

> **适用场景**：大规模状态空间的最短路径问题，可显著减少搜索空间。

### 技巧五：BFS 中记录路径

使用 `parent` 字典记录每个节点的前驱，搜索结束后回溯得到路径。

```python
parent = {start: None}
# ...
parent[neighbor] = node  # 记录前驱

# 回溯路径
path = []
node = end
while node:
    path.append(node)
    node = parent[node]
path.reverse()
```

---

## 2.5 BFS 的应用场景

| 应用领域 | 典型问题 | 说明 |
|---------|---------|------|
| **最短路径** | 无权图最短路径、迷宫最短步数 | BFS 天然求无权图最短路径 |
| **层级遍历** | 二叉树层序遍历、图的层次划分 | 逐层处理节点 |
| **连通性** | 连通分量、岛屿数量 | 与 DFS 类似，但按层扩展 |
| **多源扩散** | 腐烂橘子、病毒传播 | 多个起点同时扩散 |
| **拓扑排序** | Kahn 算法 | BFS 版拓扑排序（入度法） |
| **状态搜索** | 滑动拼图、密码锁 | 状态空间中的最短操作序列 |
| **棋盘/网格** | 骑士最短路径、蛇梯问题 | 网格上的最短步数 |

---

## 2.6 BFS 经典题目分类

### 🟢 入门级

| 题目 | 平台 | 核心考点 |
|------|------|---------|
| 二叉树的层序遍历 | LeetCode 102 | 分层 BFS 基础 |
| 二叉树的最大深度 | LeetCode 104 | BFS 求层数 |
| 二叉树的最小深度 | LeetCode 111 | BFS 求最小深度 |
| 完全二叉树的节点个数 | LeetCode 222 | BFS 遍历计数 |
| 用队列实现栈 | LeetCode 225 | 队列基本操作 |

### 🟡 中级

| 题目 | 平台 | 核心考点 |
|------|------|---------|
| 岛屿数量 | LeetCode 200 | BFS/DFS 均可 |
| 腐烂的橘子 | LeetCode 994 | **多源 BFS** |
| 01 矩阵 | LeetCode 542 | **多源 BFS** |
| 单词接龙 | LeetCode 127 | BFS 最短路径（图变换） |
| 被围绕的区域 | LeetCode 130 | BFS + 边界处理 |
| 课程表 | LeetCode 207 | **BFS 拓扑排序（Kahn 算法）** |
| 课程表 II | LeetCode 210 | BFS 拓扑排序 + 序列 |
| 二叉树的锯齿形层序遍历 | LeetCode 103 | 分层 BFS + 方向控制 |
| 二叉树的右视图 | LeetCode 199 | BFS 每层最后一个节点 |
| 钥匙和房间 | LeetCode 841 | BFS 连通性判断 |
| 地图分析 | LeetCode 1162 | **多源 BFS** |

### 🔴 高级

| 题目 | 平台 | 核心考点 |
|------|------|---------|
| 滑动拼图 | LeetCode 773 | **状态压缩 BFS** |
| 打开转盘锁 | LeetCode 752 | BFS + 状态枚举 |
| 单词接龙 II | LeetCode 126 | BFS + DFS 回溯路径 |
| 最短路径访问所有节点 | LeetCode 847 | **状态压缩 BFS** |
| 跳跃游戏 IV | LeetCode 1345 | BFS + 同值节点优化 |
| 离开迷宫的最少步数 | LeetCode 1293 | BFS + 状态（位置+消除次数） |

---

# 第三部分：DFS vs BFS 全面对比

## 3.1 核心区别

| 对比维度 | DFS（深度优先） | BFS（广度优先） |
|---------|----------------|----------------|
| **数据结构** | 栈（Stack） | 队列（Queue） |
| **搜索方向** | 纵向（一条路走到底） | 横向（逐层扩展） |
| **空间复杂度** | O(h)，h 为深度 | O(w)，w 为最大宽度 |
| **最短路径** | ❌ 不保证 | ✅ 无权图最短路径 |
| **实现方式** | 递归 / 显式栈 | 队列 |
| **适用场景** | 路径搜索、排列组合、拓扑排序 | 最短路径、层级遍历、多源扩散 |
| **记忆化** | 容易加缓存（记忆化搜索） | 一般不需要 |
| **不连通图** | 需要外层循环遍历所有起点 | 同样需要外层循环 |

## 3.2 复杂度分析

### 时间复杂度

| 场景 | DFS | BFS |
|------|-----|-----|
| 邻接矩阵 | O(V²) | O(V²) |
| 邻接表 | O(V + E) | O(V + E) |
| 网格（R×C） | O(R × C) | O(R × C) |

> V = 顶点数，E = 边数，R = 行数，C = 列数

### 空间复杂度

| 场景 | DFS | BFS |
|------|-----|-----|
| 一般图 | O(V)（最坏情况） | O(V)（最坏情况） |
| 树（平衡） | O(log V) | O(V)（最底层最多） |
| 树（退化成链） | O(V) | O(1) |
| 网格（R×C） | O(R × C)（最坏） | O(min(R, C))（宽度优先） |

### 关键结论

- **DFS 空间**取决于**递归深度**（树的高度）
- **BFS 空间**取决于**最大宽度**（某一层的最多节点数）
- 宽而浅的树：DFS 更省空间
- 窄而深的树：BFS 更省空间

## 3.3 如何选择 DFS 还是 BFS

### 选择 DFS 的情况

✅ 需要找到**所有解**（如全排列、所有路径）
✅ 需要判断**是否存在路径**（不需要最短）
✅ **排列组合、子集、回溯**类问题
✅ **拓扑排序**（DFS 更自然）
✅ **树的遍历**（前/中/后序）
✅ **连通分量**检测
✅ **记忆化搜索**（动态规划）

### 选择 BFS 的情况

✅ 需要**最短路径**（无权图）
✅ 需要**逐层处理**（层序遍历）
✅ **多源扩散**问题
✅ **迷宫/网格**最短步数
✅ **拓扑排序**（Kahn 算法 / 入度法）
✅ **状态空间搜索**求最少操作步数

### 决策流程图

```
需要最短路径？
├── 是 → BFS
└── 否
    ├── 需要所有解/排列组合？
    │   ├── 是 → DFS（回溯）
    │   └── 否
    │       ├── 需要逐层处理？
    │       │   ├── 是 → BFS
    │       │   └── 否 → DFS 或 BFS 均可
    │       └── 多源扩散？
    │           ├── 是 → 多源 BFS
    │           └── 否 → DFS
```

## 3.4 常见陷阱与易错点

### DFS 常见陷阱

| 陷阱 | 说明 | 解决方法 |
|------|------|---------|
| **忘记回溯（撤销选择）** | `path.append()` 后忘记 `path.pop()` | 严格遵循"做选择→递归→撤销选择"三步 |
| **visited 标记时机错误** | 在递归调用后标记 visited，导致重复访问 | 在递归调用**前**标记 visited |
| **递归深度过大** | Python 默认递归深度 ~1000 | `sys.setrecursionlimit()` 或改用迭代 |
| **引用传递问题** | `result.append(path)` 应为 `result.append(path[:])` | 使用 `path[:]` 或 `list(path)` 复制 |
| **重复子集/排列** | 未处理重复元素 | 排序 + 跳过重复 |
| **全局变量污染** | 在递归中使用全局变量导致状态混乱 | 使用参数传递或局部变量 |

### BFS 常见陷阱

| 陷阱 | 说明 | 解决方法 |
|------|------|---------|
| **混淆 popleft 和 pop** | `pop()` 是栈操作（O(1)），`popleft()` 才是队列 | 始终使用 `deque.popleft()` |
| **分层处理错误** | 直接遍历队列而未按层处理 | 使用 `level_size = len(queue)` |
| **visited 标记时机** | 在出队时标记 visited，导致重复入队 | 在**入队时**就标记 visited |
| **多源 BFS 初始化** | 只放入一个起点 | 将所有起点同时入队 |
| **状态遗漏** | BFS 状态不完整（如忘记记录步数/钥匙） | 将完整状态作为队列元素 |
| **死循环** | 无 visited 标记或状态转移有环 | 确保 visited 正确标记所有已访问状态 |

### ⚠️ 经典 Bug 示例

```python
# ❌ 错误：visited 在出队时标记
while queue:
    node = queue.popleft()
    visited.add(node)  # 太晚了！同一节点可能已被多次入队
    for neighbor in graph[node]:
        if neighbor not in visited:
            queue.append(neighbor)

# ✅ 正确：visited 在入队时标记
while queue:
    node = queue.popleft()
    for neighbor in graph[node]:
        if neighbor not in visited:
            visited.add(neighbor)  # 入队时就标记
            queue.append(neighbor)
```

---

# 第四部分：分阶段学习计划

## 学习路线总览

```
第 1-3 天：基础入门
    ↓
第 4-7 天：进阶应用
    ↓
第 8-10 天：综合提升
    ↓
第 11-14 天：实战冲刺
```

---

## 第一阶段：基础入门（第 1-3 天）

### 📅 第 1 天：DFS 基础

**学习目标**：理解 DFS 核心思想，掌握递归实现

| 时间段 | 内容 | 具体任务 |
|-------|------|---------|
| 上午 | 理论学习 | 阅读本指南 1.1-1.2 节，理解 DFS 的遍历过程 |
| 上午 | 动手实践 | 在纸上画出 DFS 遍历一棵二叉树的过程 |
| 下午 | 编码练习 | 手写二叉树前/中/后序遍历（递归版） |
| 下午 | 刷题 | LeetCode 144、94、145（二叉树三种遍历） |
| 晚上 | 复习总结 | 总结递归 DFS 的代码框架，记录易错点 |

**必做题目**：
- [ ] LeetCode 144 - 二叉树的前序遍历
- [ ] LeetCode 94 - 二叉树的中序遍历
- [ ] LeetCode 145 - 二叉树的后序遍历
- [ ] LeetCode 104 - 二叉树的最大深度

### 📅 第 2 天：BFS 基础

**学习目标**：理解 BFS 核心思想，掌握队列实现

| 时间段 | 内容 | 具体任务 |
|-------|------|---------|
| 上午 | 理论学习 | 阅读本指南 2.1-2.2 节，理解 BFS 的逐层扩展 |
| 上午 | 动手实践 | 在纸上画出 BFS 遍历一棵二叉树的过程 |
| 下午 | 编码练习 | 手写二叉树层序遍历，掌握分层遍历技巧 |
| 下午 | 刷题 | LeetCode 102、111（层序遍历 + 最小深度） |
| 晚上 | 对比总结 | 对比 DFS 和 BFS 的遍历顺序差异 |

**必做题目**：
- [ ] LeetCode 102 - 二叉树的层序遍历
- [ ] LeetCode 111 - 二叉树的最小深度
- [ ] LeetCode 103 - 二叉树的锯齿形层序遍历
- [ ] LeetCode 199 - 二叉树的右视图

### 📅 第 3 天：DFS vs BFS 对比 + 网格入门

**学习目标**：理解两者区别，学会在网格中使用 DFS/BFS

| 时间段 | 内容 | 具体任务 |
|-------|------|---------|
| 上午 | 理论学习 | 阅读本指南第三部分，掌握选择策略 |
| 上午 | 编码练习 | 手写网格 DFS 模板和 BFS 模板 |
| 下午 | 刷题 | LeetCode 200（岛屿数量），分别用 DFS 和 BFS 实现 |
| 下午 | 对比 | 比较同一题 DFS 和 BFS 的代码差异和效率 |
| 晚上 | 复习 | 回顾前三天的内容，整理笔记 |

**必做题目**：
- [ ] LeetCode 200 - 岛屿数量（DFS 解法）
- [ ] LeetCode 200 - 岛屿数量（BFS 解法）
- [ ] LeetCode 733 - 图像渲染（Flood Fill）

---

## 第二阶段：进阶应用（第 4-7 天）

### 📅 第 4 天：回溯法（排列组合）

**学习目标**：掌握 DFS 回溯法解决排列组合问题

| 时间段 | 内容 | 具体任务 |
|-------|------|---------|
| 上午 | 理论学习 | 阅读本指南 1.3 回溯模板，理解"做选择→递归→撤销" |
| 上午 | 编码练习 | 手写全排列代码，在纸上模拟回溯过程 |
| 下午 | 刷题 | LeetCode 46、78、77 |
| 晚上 | 总结 | 整理排列/组合/子集的代码模板差异 |

**必做题目**：
- [ ] LeetCode 46 - 全排列
- [ ] LeetCode 78 - 子集
- [ ] LeetCode 77 - 组合
- [ ] LeetCode 17 - 电话号码的字母组合

### 📅 第 5 天：回溯法进阶（剪枝与去重）

**学习目标**：掌握剪枝技巧和重复元素处理

| 时间段 | 内容 | 具体任务 |
|-------|------|---------|
| 上午 | 理论学习 | 阅读本指南 1.4 剪枝技巧 |
| 下午 | 刷题 | LeetCode 47、39、40（含重复元素的排列组合） |
| 晚上 | 总结 | 整理剪枝策略清单 |

**必做题目**：
- [ ] LeetCode 47 - 全排列 II（含重复元素）
- [ ] LeetCode 39 - 组合总和
- [ ] LeetCode 40 - 组合总和 II
- [ ] LeetCode 216 - 组合总和 III

### 📅 第 6 天：BFS 最短路径 + 多源 BFS

**学习目标**：掌握 BFS 求最短路径，学会多源 BFS

| 时间段 | 内容 | 具体任务 |
|-------|------|---------|
| 上午 | 理论学习 | 阅读本指南 2.3 最短路径模板和多源 BFS 模板 |
| 下午 | 刷题 | LeetCode 994、542、1091 |
| 晚上 | 总结 | 对比单源 BFS 和多源 BFS 的区别 |

**必做题目**：
- [ ] LeetCode 994 - 腐烂的橘子（多源 BFS）
- [ ] LeetCode 542 - 01 矩阵（多源 BFS）
- [ ] LeetCode 1091 - 二进制矩阵中的最短路径
- [ ] LeetCode 1162 - 地图分析（多源 BFS）

### 📅 第 7 天：网格 DFS 进阶

**学习目标**：掌握网格中的复杂 DFS 问题

| 时间段 | 内容 | 具体任务 |
|-------|------|---------|
| 上午 | 理论学习 | 复习网格 DFS 模板，学习边界处理技巧 |
| 下午 | 刷题 | LeetCode 79、130、695 |
| 晚上 | 总结 | 整理网格 DFS 的通用框架 |

**必做题目**：
- [ ] LeetCode 79 - 单词搜索
- [ ] LeetCode 130 - 被围绕的区域
- [ ] LeetCode 695 - 岛屿的最大面积
- [ ] LeetCode 417 - 太平洋大西洋水流问题

---

## 第三阶段：综合提升（第 8-10 天）

### 📅 第 8 天：拓扑排序 + 环检测

**学习目标**：掌握 DFS 和 BFS 两种拓扑排序方法

| 时间段 | 内容 | 具体任务 |
|-------|------|---------|
| 上午 | 理论学习 | 学习 DFS 拓扑排序（后序遍历逆序）和 BFS 拓扑排序（Kahn 算法） |
| 下午 | 刷题 | LeetCode 207、210 |
| 晚上 | 对比 | 总结两种拓扑排序的适用场景 |

**必做题目**：
- [ ] LeetCode 207 - 课程表（DFS 环检测）
- [ ] LeetCode 210 - 课程表 II（BFS 拓扑排序）
- [ ] LeetCode 329 - 矩阵中的最长递增路径
- [ ] LeetCode 802 - 找到最终的安全状态

### 📅 第 9 天：状态搜索 BFS

**学习目标**：掌握将复杂问题转化为 BFS 状态搜索

| 时间段 | 内容 | 具体任务 |
|-------|------|---------|
| 上午 | 理论学习 | 阅读本指南 2.4 状态压缩 BFS 技巧 |
| 下午 | 刷题 | LeetCode 752、773 |
| 晚上 | 总结 | 整理状态表示和转移的方法 |

**必做题目**：
- [ ] LeetCode 752 - 打开转盘锁
- [ ] LeetCode 773 - 滑动拼图
- [ ] LeetCode 127 - 单词接龙
- [ ] LeetCode 847 - 最短路径访问所有节点

### 📅 第 10 天：记忆化搜索（DFS + DP）

**学习目标**：掌握记忆化搜索，理解 DFS 与动态规划的联系

| 时间段 | 内容 | 具体任务 |
|-------|------|---------|
| 上午 | 理论学习 | 阅读本指南 1.3 记忆化搜索模板 |
| 下午 | 刷题 | LeetCode 329、62、63 |
| 晚上 | 总结 | 理解"记忆化搜索 = 自顶向下 DP" |

**必做题目**：
- [ ] LeetCode 62 - 不同路径（记忆化搜索）
- [ ] LeetCode 63 - 不同路径 II（记忆化搜索）
- [ ] LeetCode 329 - 矩阵中的最长递增路径
- [ ] LeetCode 494 - 目标和

---

## 第四阶段：实战冲刺（第 11-14 天）

### 📅 第 11-12 天：经典难题攻坚

**学习目标**：挑战高难度 DFS/BFS 综合题

**必做题目**：
- [ ] LeetCode 51 - N 皇后（DFS + 剪枝）
- [ ] LeetCode 37 - 解数独（DFS + 多维剪枝）
- [ ] LeetCode 212 - 单词搜索 II（DFS + Trie）
- [ ] LeetCode 124 - 二叉树中的最大路径和
- [ ] LeetCode 301 - 删除无效的括号
- [ ] LeetCode 126 - 单词接龙 II（BFS + DFS）

### 📅 第 13 天：综合模拟测试

**学习目标**：限时完成一套 DFS/BFS 专题测试

| 题目 | 时间限制 | 难度 |
|------|---------|------|
| LeetCode 200 - 岛屿数量 | 10 分钟 | 中等 |
| LeetCode 46 - 全排列 | 15 分钟 | 中等 |
| LeetCode 994 - 腐烂的橘子 | 20 分钟 | 中等 |
| LeetCode 79 - 单词搜索 | 20 分钟 | 中等 |
| LeetCode 752 - 打开转盘锁 | 25 分钟 | 中等 |
| LeetCode 51 - N 皇后 | 30 分钟 | 困难 |

**评分标准**：
- 5/6 题通过：优秀 ✅
- 3-4/6 题通过：良好 👍
- 1-2/6 题通过：需要加强薄弱环节 📝

### 📅 第 14 天：查漏补缺 + 总结

**学习目标**：回顾所有知识点，整理个人笔记

| 时间段 | 内容 |
|-------|------|
| 上午 | 重做之前做错的题目 |
| 下午 | 整理 DFS/BFS 代码模板速查表 |
| 下午 | 总结每种题型的识别方法和解题套路 |
| 晚上 | 制定后续练习计划（每周 2-3 题 DFS/BFS 保持手感） |

---

## 每日学习建议

### 时间安排

| 阶段 | 建议用时 | 说明 |
|------|---------|------|
| 理论学习 | 1-2 小时 | 先理解概念再看代码 |
| 编码练习 | 2-3 小时 | 先手写再对照模板 |
| 刷题 | 2-3 小时 | 先独立思考至少 20 分钟 |
| 总结复习 | 30 分钟 | 记录心得和易错点 |

### 刷题方法论

1. **先独立思考** 20-30 分钟，不要急于看题解
2. **画图辅助**：在纸上画出递归树或 BFS 扩展过程
3. **先写伪代码**，再翻译为具体语言
4. **写完后调试**：用简单测试用例手动走一遍
5. **看题解对比**：学习更优解法和技巧
6. **隔天重做**：第二天不看题解再写一遍

### 代码模板速记

```
DFS 递归框架：
  def dfs(参数):
      if 终止条件: return
      处理当前层
      dfs(下一层)
      撤销选择（回溯时）

DFS 回溯框架：
  for 选择 in 选择列表:
      做选择
      backtrack()
      撤销选择

BFS 框架：
  queue = deque([起点])
  visited = {起点}
  while queue:
      node = queue.popleft()
      for neighbor in node.neighbors:
          if neighbor not in visited:
              visited.add(neighbor)
              queue.append(neighbor)

BFS 分层框架：
  while queue:
      level_size = len(queue)
      for _ in range(level_size):
          node = queue.popleft()
          ...
```

---

# 附录：推荐刷题清单

## 按难度分级

### 🟢 基础（10 题）

| # | 题目 | LeetCode | 类型 |
|---|------|-----------|------|
| 1 | 二叉树的前序遍历 | 144 | DFS |
| 2 | 二叉树的中序遍历 | 94 | DFS |
| 3 | 二叉树的后序遍历 | 145 | DFS |
| 4 | 二叉树的最大深度 | 104 | DFS |
| 5 | 二叉树的层序遍历 | 102 | BFS |
| 6 | 二叉树的最小深度 | 111 | BFS |
| 7 | 岛屿数量 | 200 | DFS/BFS |
| 8 | 相同的树 | 100 | DFS |
| 9 | 路径总和 | 112 | DFS |
| 10 | 图像渲染 | 733 | DFS/BFS |

### 🟡 中级（15 题）

| # | 题目 | LeetCode | 类型 |
|---|------|-----------|------|
| 1 | 全排列 | 46 | DFS 回溯 |
| 2 | 子集 | 78 | DFS 回溯 |
| 3 | 组合 | 77 | DFS 回溯 |
| 4 | 电话号码的字母组合 | 17 | DFS 回溯 |
| 5 | 单词搜索 | 79 | DFS 网格 |
| 6 | 被围绕的区域 | 130 | DFS/BFS |
| 7 | 岛屿的最大面积 | 695 | DFS |
| 8 | 腐烂的橘子 | 994 | 多源 BFS |
| 9 | 01 矩阵 | 542 | 多源 BFS |
| 10 | 单词接龙 | 127 | BFS 最短路径 |
| 11 | 课程表 | 207 | DFS 环检测 |
| 12 | 课程表 II | 210 | BFS 拓扑排序 |
| 13 | 打开转盘锁 | 752 | BFS 状态搜索 |
| 14 | 组合总和 | 39 | DFS 回溯 + 剪枝 |
| 15 | 全排列 II | 47 | DFS 回溯 + 去重 |

### 🔴 高级（10 题）

| # | 题目 | LeetCode | 类型 |
|---|------|-----------|------|
| 1 | N 皇后 | 51 | DFS + 剪枝 |
| 2 | 解数独 | 37 | DFS + 多维剪枝 |
| 3 | 单词搜索 II | 212 | DFS + Trie |
| 4 | 滑动拼图 | 773 | 状态压缩 BFS |
| 5 | 最短路径访问所有节点 | 847 | 状态压缩 BFS |
| 6 | 单词接龙 II | 126 | BFS + DFS |
| 7 | 二叉树中的最大路径和 | 124 | DFS 后序 |
| 8 | 删除无效的括号 | 301 | DFS + 复杂剪枝 |
| 9 | 矩阵中的最长递增路径 | 329 | 记忆化搜索 |
| 10 | 太平洋大西洋水流问题 | 417 | DFS/BFS |

---

## 学习进度跟踪表

| 阶段 | 天数 | 日期 | 完成题目数 | 掌握程度 | 备注 |
|------|------|------|-----------|---------|------|
| 基础入门 | Day 1 | ____ | /4 | ⬜⬜⬜⬜⬜ | |
| 基础入门 | Day 2 | ____ | /4 | ⬜⬜⬜⬜⬜ | |
| 基础入门 | Day 3 | ____ | /3 | ⬜⬜⬜⬜⬜ | |
| 进阶应用 | Day 4 | ____ | /4 | ⬜⬜⬜⬜⬜ | |
| 进阶应用 | Day 5 | ____ | /4 | ⬜⬜⬜⬜⬜ | |
| 进阶应用 | Day 6 | ____ | /4 | ⬜⬜⬜⬜⬜ | |
| 进阶应用 | Day 7 | ____ | /4 | ⬜⬜⬜⬜⬜ | |
| 综合提升 | Day 8 | ____ | /4 | ⬜⬜⬜⬜⬜ | |
| 综合提升 | Day 9 | ____ | /4 | ⬜⬜⬜⬜⬜ | |
| 综合提升 | Day 10 | ____ | /4 | ⬜⬜⬜⬜⬜ | |
| 实战冲刺 | Day 11-12 | ____ | /6 | ⬜⬜⬜⬜⬜ | |
| 实战冲刺 | Day 13 | ____ | /6 | ⬜⬜⬜⬜⬜ | |
| 实战冲刺 | Day 14 | ____ | 复习 | ⬜⬜⬜⬜⬜ | |

> 掌握程度：⬜ = 未掌握，每格代表 20%，涂满 5 格 = 完全掌握

---

*祝你学习顺利！DFS 和 BFS 是算法学习的基石，掌握后将为后续学习动态规划、图论等高级主题打下坚实基础。*
