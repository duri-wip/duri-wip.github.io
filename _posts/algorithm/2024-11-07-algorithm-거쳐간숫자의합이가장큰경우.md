---
title: "거쳐간 숫자의 합이 가장 큰 경우"
excerpt: "Programmers level 2"

categories:
  - algorithm
tags:
  - [Algorithm]

permalink: /algorithm/max-sum

toc: true
toc_sticky: true

date: 2024-11-07
last_modified_at: 2024-11-07
---
## 문제

위와 같은 삼각형의 꼭대기에서 바닥까지 이어지는 경로 중, 거쳐간 숫자의 합이 가장 큰 경우를 찾아보려고 합니다. 아래 칸으로 이동할 때는 대각선 방향으로 한 칸 오른쪽 또는 왼쪽으로만 이동 가능합니다. 예를 들어 3에서는 그 아래칸의 8 또는 1로만 이동이 가능합니다.

삼각형의 정보가 담긴 배열 triangle이 매개변수로 주어질 때, 거쳐간 숫자의 최댓값을 return 하도록 solution 함수를 완성하세요.

## 풀이

```python
def solution(triangle):
    result, stack = [], []
    
    for i in range(len(triangle)):
        n = len(triangle[i])
        if n == 1:
            line = triangle[i]
        else:
            line = []
            for j in range(n):
                if j == 0:
                    line.append(stack[i-1][j]+triangle[i][j])
                elif j == n-1:
                    line.append(stack[i-1][j-1]+triangle[i][j])
                else:
                    line.append(max(stack[i-1][j-1], stack[i-1][j]) + triangle[i][j])

        stack.append(line)
    return max(stack[-1])
```

## 알게된 점
* 끝값을 처리할때 항상 n 그대로인지, n-1인지 체크할것
* 꼭 값을 2개 넣는게 아니라 둘 중 최대 값을 넣는 처리를 할 수 있다. 
