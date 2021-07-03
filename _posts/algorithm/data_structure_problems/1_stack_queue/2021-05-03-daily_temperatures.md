---
title:  "[Algorithm_Problem_Stack] 일일 온도"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/data-structure.jpg

categories:
  - stack_queue_problem
tags:
  - data_structure
last_modified_at: 2021-05-04
---  

## 문제  
리트코드 739. Daily Temperatures  
난이도: ⭐⭐
```
Given a list of daily temperatures temperatures, return a list such that, for each day in the input, tells you how many days you would have to wait until a warmer temperature. If there is no future day for which this is possible, put 0 instead.

For example, given the list of temperatures temperatures = [73, 74, 75, 71, 69, 72, 76, 73], your output should be [1, 1, 4, 2, 1, 1, 0, 0].

Note: The length of temperatures will be in the range [1, 30000]. Each temperature will be an integer in the range [30, 100].
(매일의 화씨 온도리스트 T를 입력 받아, 더 따뜻한 날씨를 위해 며칠을 더 기다려야 하는지를 출력하라)
```

예시 1  

```
Input: T =[73, 74, 75, 71, 69, 72, 76, 73]
Output: [1, 1, 4, 2, 1, 1, 0, 0]
```


------------------------------------------------------------------------  
### 나의 풀이: Brute Force
```python
class Solution:
    def dailyTemperatures(self, T: List[int]) -> List[int]:

        answer = []

        for cur_idx in range(len(T)):
            for com_idx in range(cur_idx, len(T)):
                if T[com_idx] <= T[cur_idx]:
                    continue
                else:
                    answer.append(com_idx-cur_idx)
                    break
            else:
                answer.append(0)

        return answer
------------------------------------------------
결과: 런타임 에러  
```

### 풀이 2: 스택을 이용한 풀이

```python
class Solution:
    def dailyTemperatures(self, T: List[int]) -> List[int]:
        answer = [0]*len(T)
        stack = []
        
        for i, cur in enumerate(T):
            
            while stack and cur > T[stack[-1]]:
                last = stack.pop()
                answer[last] = i - last
            stack.append(i)
        
        return answer
------------------------------------------------
결과: 통과 492ms 
```

### 풀이 3: 아주 조금 더 효율적인 풀이

```python
class Solution:
    def dailyTemperatures(self, T: List[int]) -> List[int]:
        n, right_max = len(T), float('-inf')
        res = [0] * n
        for i in range(n-1, -1, -1):
            t = T[i]
            if right_max <= t:
                right_max = t
            else:
                days = 1
                while T[i+days] <= t:
                    days += res[i+days]
                res[i] = days
        return res
------------------------------------------------
결과: 통과 484ms
```

