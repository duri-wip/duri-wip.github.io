## 문제

남은 일의 작업량을 제곱해서 야근 지수를 구할때, 야근 지수가 최소화 되도록 일하여 최소화된 값을 반환하기.

## 풀이
```python
def solution(n, works):
    if sum(works) <= n:
        return 0

    while n > 0:
        idx = works.index(max(works))
        works[idx] -= 1
        n -= 1
    
    def cal_p(n):
        return n ** 2
    
    return sum(map(cal_p, works))
```

근데 문제는 효율성 테스트에서 넘어가지 않는다는 것이다..
이걸 해결하기 위해서는 힙을 써야한다고 한다. 
힙이란?
