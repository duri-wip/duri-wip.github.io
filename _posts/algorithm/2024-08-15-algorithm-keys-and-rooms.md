---
title: "keys and rooms"
excerpt: "이어드림 코딩테스트 특강 5주차"

categories:
  - algorithm
tags:
  - [Algorithm]

permalink: /algorithm/keys-and-rooms

toc: true
toc_sticky: true

date: 2024-08-15
last_modified_at: 2024-08-15
---

(https://leetcode.com/problems/keys-and-rooms/description/)

## 문제

0번방의 키만 가지고 있고 방에 들어가면 다른 방의 키가 있다. 방을 돌면서 얻은 키로 모든 방을 방문할 수 있는 지 확인하기.

## 해결

```python
class Solution:
    def canVisitAllRooms(self, rooms: List[List[int]]) -> bool:
        visited = [False]*len(rooms)

        def dfs(cur_v, rooms):
            visited[cur_v]=True
            for next_v in rooms[cur_v]:
                if not visited[next_v]:
                    dfs(next_v,rooms)
        
        dfs(0, rooms)       
        if sum(visited) < len(rooms):
            return False
        else: return True
```
