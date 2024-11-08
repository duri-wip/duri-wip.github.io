
## 풀이

```python
def solution(rows, columns, queries):
    # 그리드 그리기
    grid = [list(range(r*columns+1, r*columns+columns+1)) for r in range(rows)]    
    
    # 시작점부터 순서대로 돌기
    def rotate_store(sx, sy, fx, fy):
        
        stack = []
        t = None
        # 시작점에서 오른쪽으로 이동
        for i in range(sy, fy+1):
            if not stack:
                stack.append(grid[sx][i])
            else:
                t = grid[sx][i]
                grid[sx][i] = stack[-1]
                stack.append(t)
        
        for i in range(sx+1, fx+1):
            t = grid[i][fy]
            grid[i][fy] = stack[-1]
            stack.append(t)

        for i in range(fy-1, sy-1, -1):
            t = grid[fx][i]
            grid[fx][i] = stack[-1]
            stack.append(t)
        
        for i in range(fx-1, sx-1, -1):
            t = grid[i][sy]
            grid[i][sy] = stack[-1]
            stack.append(t)
        return min(stack)
        
    result = []
    for q in queries:
        
        sx, sy = q[0]-1, q[1]-1
        fx, fy = q[2]-1, q[3]-1
        
        min_v = rotate_store(sx, sy, fx, fy)
        result.append(min_v)
    return result

```

## 알게된점
* grid 문제에서 list out of index 에러 나면 row 랑 columns 맞게 넣어서 그리드 그린건지 확인해보기!!
* 꼭지점에서 주의하자.
* range(큰값, 작은값, -1)