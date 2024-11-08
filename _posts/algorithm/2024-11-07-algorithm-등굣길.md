## 문제
웅덩이를 피해 학교에 가는 최단 거리의 경로 개수를 구하기

## 풀이
```python
from collections import deque
def solution(m,n, puddles):
    dist = [[0]*m for _ in range(n)]
    
    # 시작점
    dist[0][0] = 1

    # 물웅덩이
    for x, y in puddles:
        dist[y-1][x-1] = -1
    
    # queue 설정
    queue = deque([(0,0)])

    # 유효한지 확인하기
    def isValid(x,y):
        if 0<=x<m and 0<=y<n and dist[y][x] != -1:
            return True
        return False

    # 돌기
    while queue:
        x, y = queue.popleft()
        #물웅덩이 피해가기
        if dist[y][x] == -1 :
            continue
        
        for dx, dy in [(0,1), (1,0)]:
            nx, ny = x+ dx, y+ dy

            if isValid(nx,ny):
                if dist[ny][nx] == 0:
                    queue.append((nx,ny))
                dist[ny][nx] += dist[y][x]
                dist[ny][nx] %= 1000000007
    
    return dist[n-1][m-1]

```


## 알게된 점
* 어떤걸 초기화해서 기록할지를 정해야한다. 거리를 기록할건지(dist), 방문 여부를 기록할건지(visited)
* 큐에서 뽑아서 쓰는 dfs의 경우에 큐 자체가 이터러블 하기 위해서 리스트 안에 튜플을 넣어줘야 한다!!! => non-iterable 오류남
* 그리드의 경우에 x,y로 표현되지 않고 y,x로 표현되는 경우도 있기 때문에 예시를 잘 봐야 한다. 
* 시간 효율 문제가 나는 경우에는 큰수를 방지해 주는걸 붙여보자. 이거 하나로 갈린다. 