---
title: "BFS : Breadth-first search"
excerpt: "이어드림 코딩테스트 특강 4주차"

categories:
  - algorithm
tags:
  - [Algorithm]

permalink: /algorithm/bfs

toc: true
toc_sticky: true

date: 2024-08-21
last_modified_at: 2024-08-21
---
### 제공 템플릿

```python
from collections import deque 
def bfs(graph, start_v):
    pass

graph = {
    0: [1, 3, 6],
    1: [0, 3],
    2: [3],
    3: [0, 1, 2, 7],
    4: [5],
    5: [4, 6, 7],
    6: [0, 5],
    7: [3, 5],
}
bfs(graph, start_v=0)
```

### 코드 구현

```python
from collections import deque 
def bfs(graph, start_v):
    # 초기세팅
    n = len(graph)
    visited = [False] * n
    queue = deque()
    
    # 시작점 예약
    queue.append(start_v)
    visited[start_v] = True
    
    while queue:
        # 현재 노드 방문
        cur_v = queue.popleft()
        print(cur_v, end='')
        if cur_v == 7:
            print('찾았다!',end='')

        # 다음 노드 예약
        for next_v in graph[cur_v]:
            if not visited[next_v]:
                queue.append(next_v)
                visited[next_v] = True
        print('->', end='')

graph = {
    0: [1, 3, 6],
    1: [0, 3],
    2: [3],
    3: [0, 1, 2, 7],
    4: [5],
    5: [4, 6, 7],
    6: [0, 5],
    7: [3, 5],
}

bfs(graph, start_v=0)
```