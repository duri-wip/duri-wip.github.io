---
title: "DFS : Depth-first search"
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
### 코드 구현

```python
def dfs(cur_v):
    # 방문
    visited[cur_v] = True
    print(cur_v, end='->')
    # 다음 노드 방문
    for next_v in graph[cur_v]:
        if next_v not in visited:
            dfs(next_v)

# visited dictionary version
visited = {}
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
dfs(0)
```