---
title: "최대 폭탄 터뜨리기"
excerpt: "bfs"

category:  algorithm
tags:
  - [알고리즘, bfs]

permalink: /algorithm/max_explode
toc: true
toc_sticky: true

date: 2025-07-10
last_modified_at: 2025-07-10
---

## 문제

한 폭탄을 터뜨렸을때 그 반경 안에 들어오는 다른 폭탄은 연쇄적으로 터진다.
이 원리를 계산하여 한 폭탄을 터뜨렸을때 가장 많이 연쇄 반응을 일으키는 개수를 구하여라.

## 풀이

```
from collections import deque
from itertools import combinations

class Solution:
    def maximumDetonation(self, bombs: List[List[int]]) -> int:
        # init
        n = len(bombs)
        graph = [[] for _ in range(n)]

        def is_connected(bomb1, bomb2):
            x1, y1, r1 = bomb1
            x2, y2, r2 = bomb2
            dist = (x1 - x2)**2 + (y1 - y2)**2
            return dist <= r1**2

        # graph
        for i in range(n):
            for j in range(n):
                if i == j:
                    continue
                if is_connected(bombs[i], bombs[j]):
                    graph[i].append(j)

        # queue
        def bfs(start):
            visited = [False]*n
            visited[start] = True
            queue = deque([start])
            cnt = 1

            while queue:
                node = queue.popleft()
                for next_node in graph[node]:
                    if not visited[next_node]:
                        visited[next_node] = True
                        queue.append(next_node)
                        cnt += 1
            return cnt


        return max(bfs(i) for i in range(n))
```

여기서 중요했던건 visited 변수를 매 시도마다 초기화해줘야한다는 점이다.
그래야 각각의 시도마다 어떤 폭탄이 터지는지 개수를 알맞게 셀 수 있다.

그 다음으로는 폭탄의 거리를 계산할때 양방향이 아니라 한방향으로 계산해야한다는 점이다. 연쇄 반응이므로..

그리고 연결 그래프를 만들때, 처음에는 combinations를 사용해서
조합을 만들고 계산했는데, 그러면 1 -> 2, 1 -> 3 의 경우만 되고 2 -> 1 , 3 -> 1의 경우는 안되니까
방향을 언제나 생각해야 함!!
