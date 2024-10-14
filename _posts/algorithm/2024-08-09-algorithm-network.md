https://school.programmers.co.kr/learn/courses/30/lessons/43162

## 문제 파악

네트워크란 컴퓨터 상호 간에 정보를 교환할 수 있도록 연결된 형태를 의미합니다. 예를 들어, 컴퓨터 A와 컴퓨터 B가 직접적으로 연결되어있고, 컴퓨터 B와 컴퓨터 C가 직접적으로 연결되어 있을 때 컴퓨터 A와 컴퓨터 C도 간접적으로 연결되어 정보를 교환할 수 있습니다. 따라서 컴퓨터 A, B, C는 모두 같은 네트워크 상에 있다고 할 수 있습니다.

컴퓨터의 개수 n, 연결에 대한 정보가 담긴 2차원 배열 computers가 매개변수로 주어질 때, 네트워크의 개수를 return 하도록 solution 함수를 작성하시오.

### 제한사항

- 컴퓨터의 개수 n은 1 이상 200 이하인 자연수입니다.
- 각 컴퓨터는 0부터 `n-1`인 정수로 표현합니다.
- i번 컴퓨터와 j번 컴퓨터가 연결되어 있으면 computers[i][j]를 1로 표현합니다.
- computer[i][i]는 항상 1입니다.

| n | computers | return |
| --- | --- | --- |
| 3 | [[1, 1, 0], [1, 1, 0], [0, 0, 1]] | 2 |
| 3 | [[1, 1, 0], [1, 1, 1], [0, 1, 1]] | 1 |

## 접근 방법

## 코드 구현

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

```python
def solution(n, computers):
    visited = [False]*n
    count = 0
    
    def dfs(n, computers):
        visited[n]=True
        for next in computers[n]:
            if not visited[n]:
                dfs(next, computers)
    
    for i in range(len(computers)):
        dfs(i, computers)
        count += 1
        if sum(visited) == n:
            break
    return count
    
```

### BFS 풀이법

## 배우게 된 점

## 질문

댓글로 또는 이곳에 질문 남겨주세요.