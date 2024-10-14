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
https://leetcode.com/problems/keys-and-rooms/description/

## 문제 파악

0번방의 키만 가지고 있고 방에 들어가면 다른 방의 키가 있다. 방을 돌면서 얻은 키로 모든 방을 방문할 수 있는 지 확인하기.

**Example 1:**

```
Input: rooms = [[1],[2],[3],[]]
Output: true
Explanation:
We visit room 0 and pick up key 1.
We then visit room 1 and pick up key 2.
We then visit room 2 and pick up key 3.
We then visit room 3.
Since we were able to visit every room, we return true.

```

**Example 2:**

```
Input: rooms = [[1,3],[3,0,1],[2],[0]]
Output: false
Explanation: We can not enter room number 2 since the only key that unlocks it is in that room.

```

**Constraints:**

- `n == rooms.length`
- `2 <= n <= 1000`
- `0 <= rooms[i].length <= 1000`
- `1 <= sum(rooms[i].length) <= 3000`
- `0 <= rooms[i][j] < n`
- All the values of `rooms[i]` are **unique**.

## 접근 방법

연결된 그래프 문제로 해결하기!

이때 인덱스의 종류가 있는데, 어떤 방의 키가 distinct한 번호로 정해져 있고 있는 키만 있다→ 인접, 모든 방을 list로 주고 잇으면 1 없으면 0의 형식으로 되어잇다 → 그냥

그치만 둘다 푸는 방법은 똑같다.. 이게 참 미스테리

## 코드 구현

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

## 배우게 된 점

## 질문

처음에 할때 

```python
def dfs(cur_v, rooms):
            visited[cur_v]=True
            for next_v in rooms[cur_v]:
                if not visited[next_v]:
                    dfs(next_v,rooms)
           return visited
```

를 붙였는데 두번째 테스트 케이스에서 틀렸다. 

왜그런걸까?

그리고 visited는 밖에 선언한 변수인데 …. 매개변수로 붙여주는거랑 뭐가 다른걸까?

댓글로 또는 이곳에 질문 남겨주세요.