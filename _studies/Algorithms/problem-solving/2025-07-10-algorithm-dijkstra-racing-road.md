---
title: "경주로 건설"
excerpt: "다익스트라 알고리즘 기초: 최단 경로 구하기, 가중치"

category:  algorithm
tags:
  - [Algorithm, 다익스트라]

permalink: /algorithm/racing_road
toc: true
toc_sticky: true

date: 2025-07-10
last_modified_at: 2025-07-10
---

## 문제

경주로를 건설한다. 경주로의 출발점은 (0,0)이고 도착점은 (N-1, N-1)이다. 크기는 N\*N
중간에 끊기지 않는 경주로를 건설해야한다.
상하좌우로 인접한 두 빈칸을 연결해 건설할 수 있고, 벽이 있는 칸에는 건설할 수 없다.
직선도로를 만들때는 100원, 코너를 만들때는 500원이 추가로 든다.

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
