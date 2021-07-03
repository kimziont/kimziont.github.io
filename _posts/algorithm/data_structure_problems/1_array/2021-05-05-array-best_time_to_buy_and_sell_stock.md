---
title:  "[Algorithm_Problem_Array] 주식을 사고팔기 가장 좋은 시점"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/data-structure.jpg

categories:
  - array_problem
tags:
  - data_structure
last_modified_at: 2021-05-05
---  

## 문제  
리트코드 121. Best Time to Buy and Sell Stock  
난이도: ⭐
```
You are given an array prices where prices[i] is the price of a given stock on the ith day.
You want to maximize your profit by choosing a single day to buy one stock and choosing a different day in the future to sell that stock.
Return the maximum profit you can achieve from this transaction. If you cannot achieve any profit, return 0.
(한 번의 거래로 낼 수 있는 최대 이익을 산출하라.)
```

예시 1  

```
Input: prices = [7,1,5,3,6,4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
Note that buying on day 2 and selling on day 1 is not allowed because you must buy before you sell.
```

예시 2  

```
Input: prices = [7,6,4,3,1]
Output: 0
Explanation: In this case, no transactions are done and the max profit = 0.
```  

------------------------------------------------------------------------  
### 나의 풀이: 

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        min_price = sys.maxsize
        max_profit = -sys.maxsize
        
        for i in range(len(prices)):
            if prices[i] <= min_price:
                min_price = prices[i]
                
            if prices[i] - min_price >= max_profit:
                max_profit = prices[i] - min_price
        
        return max_profit

-------------------------------------------------------------------
# 더 나은 코드
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        min_price = sys.maxsize
        profit = 0
        
        for price in prices:
            min_price = min(min_price, price)
            profit = max(price-min_price, profit)
        
        return profit
```
