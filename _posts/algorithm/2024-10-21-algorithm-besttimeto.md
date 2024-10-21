---
title: "Best time to buy and sell stock"
excerpt: "Grind75 :   week 1"

categories:
  - algorithm
tags:
  - [Algorithm]

permalink: /algorithm/buy-and-sell

toc: true
toc_sticky: true

date: 2024-10-21
last_modified_at: 2024-10-21
---

## 문제

가격이 시간순으로 나열된 prices 리스트가 주어졌을때 최대의 이익을 보기 위해서 언제 주식을 사고 팔아야 하는지를 계산해서 그 최대 이익값을 반환한다. 만약 이익을 볼 수 없는 리스트가 주어진 경우 0을 반환한다. 

prices = [7,1,5,3,6,4]
-> output = 5:(6-1이라서)

## 시행착오

이중포인터를 사용해서 최대 값을 받으려고 했다.

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        new_prices = [[i,v] for i,v in enumerate(prices)]
        new_prices.sort(key=lambda x:x[1])
        
        max_profit = 0
        profits =[]
        left = 0
        for i in range(len(prices)):
            left = i
            right = len(prices)-1
            while left < right:
                if new_prices[right][0] < new_prices[left][0]:
                    right -= 1
                else:
                    max_profit = new_prices[right][1] - new_prices[left][1]
                    right -= 1
                    profits.append(max_profit)
        if not profits:
            return 0
        else:
            return max(profits)
```

시간초과가 난다.
정렬하고 반복문을 모든 시작점에 대해서 돌려야 하기 때문이다. 따라서 이중포인터로 푸는 방식에서 효율 이득을 얻지 못하는 문제다.

다른 방식으로 풀어보았다. 
어차피 시간순서대로 최대값을 구해야하므로 순차적으로 한번은 돌아야 한다. 최대 한번만 도는 방식으로 재구성해본다. 

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        max_profit = 0
        for i in range(0,len(prices)-1):
            rest = prices[i+1:]
            max_price = max(rest)
            if prices[i] < max_price:
                if max_profit < max_price - prices[i]:
                    max_profit = max_price - prices[i]
        return max_profit
```

이렇게 해서 시간 복잡도를 줄이기는 했지만 그래도 시간 초과가 발생했다.
다른 방법으로 해결해야 한다. 
아무래도 문제가 있는 부분은 max_price를 구하기 위해 rest를 구하는 과정에서인것 같다.
이렇게 하지 않고 결국 뒷부분의 최대값을 구하는 것이 핵심이므로 리스트를 뒤집어 뒤에서부터 최대값을 찾는 방식으로 개선해보려고 한다. 



```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        max_profit = 0
        max_price = 0

        for price in reversed(prices):
            if max_price < price:
                max_price = price
                print(max_price)
            max_profit = max(max_profit, max_price - price)
            print(max_profit)
        return max_profit
```







## 