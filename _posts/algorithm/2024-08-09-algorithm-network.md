---
title: "네트워크"
excerpt: "이어드림 코딩테스트 특강 3주차"

categories:
  - algorithm
tags:
  - [Algorithm]

permalink: /algorithm/network

toc: true
toc_sticky: true

date: 2024-08-09
last_modified_at: 2024-08-09
---

(https://school.programmers.co.kr/learn/courses/30/lessons/43162)

## 문제

컴퓨터의 개수 n, 연결에 대한 정보가 담긴 2차원 배열 computers가 매개변수로 주어질 때, 네트워크의 개수를 반환하기

## 해결
### DFS 풀이법

```python
def solution(n, computers):
    answer = 0
    visited = [False]*n
    
    def dfs(cur_v):
        visited[cur_v] = True
        for next_v in range(n):
            # 연결이 안된거니까, skip
            if computers[cur_v][next_v] == 0: continue
            if not visited[next_v]:
                dfs(next_v)
    
    for cur_v in range(n):
        if not visited[cur_v]:
            dfs(cur_v)
            # bfs(cur_v)
            answer += 1
    return answer
```


### BFS 풀이법

```python
from collections import deque

def solution(n, computers):
    answer = 0
    visited = [False] * n

    def bfs(start_v):
        queue = deque([start_v])
        visited[start_v] = True
        while queue:
            cur_v = queue.popleft()
            for next_v in range(n):
                if computers[cur_v][next_v] == 1 and not visited[next_v]:
                    visited[next_v] = True  
                    queue.append(next_v)    

    for i in range(n):
        if not visited[i]:  
            bfs(i)          
            answer += 1     
    return answer
```
