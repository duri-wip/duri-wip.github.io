---
title: "삼각달팽이"
excerpt: "Programmers level 2"

category:  algorithm
tags:
  - [알고리즘, 프로그래머스]

permalink: /algorithm/triangle

toc: true
toc_sticky: true

date: 2024-11-07
last_modified_at: 2024-11-07
---
## 문제

정수 n이 매개변수로 주어녔을때 밑변의 길이와 높이가 n 인 삼각형에서 맨 위꼭지점부터 반시계방향으로 달팽이 채우기

## 풀이
```python
def solution(n):
    #목표
    target = sum(range(1, n+1))
    
    # 삼각형 초기화
    trian = [[0]*i for i in range(1,n+1)]

    # 다른 변수 초기화
    num = 1
    x,y = 0,0
    d = 0

    # 방향
    dirs = [(1,0),(0,1),(-1,-1)]

    #이동
    while num <= target:
        trian[x][y] = num
        num += 1

        # 다음방향으로 이동
        nx, ny = x + dirs[d][0], y + dirs[d][1]

        # 다음 위치가 유효한지 확인
        if nx < 0 or ny <0 or nx >= n or ny >= len(trian[nx]) or trian[nx][ny] != 0:
            d = (d + 1) % 3
            nx, ny = x + dirs[d][0], y + dirs[d][1]
        
        x, y = nx, ny
    
    result = []
    for row in trian:
        result.extend(row)
    return result

```

## 알게된 점
* 삼각형으로 움직일때도 방향 리스트를 주고 그 안에서 이동하는게 가능하구나
* 다음위치가 유효한지 확인하는 방법에 신경쓸것
* list out of index error -> 그리드 초기화 부분을 반드시 먼저 확인할것!