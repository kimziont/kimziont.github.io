---
title:  "[Algorithm_Problem_Graph] 조합"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/data-structure.jpg

categories:
  - graph_problem
tags:
  - data_structure
last_modified_at: 2021-05-03
---  

## 문제  
리트코드 77. Combunations  
난이도: ⭐⭐  
```
Given two integers n and k, return all possible combinations of k numbers out of the range [1, n].

You may return the answer in any order.
(전체 수 n을 입력받아 k개의 조합을 리턴하라.)
```

예시 1  

```
Input: n = 4, k = 2
Output:
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

예시 2  
```
Input: n = 1, k = 1
Output: [[1]]
```

## 풀이 1: DFS로 k개 조합 생성

```python
class Solution:
    def combine(self, n: int, k: int) -> List[List[int]]:
        results = []
        
        def dfs(elements, start, k):
            if k == 0:
                results.append(elements[:])
                return
            
            for i in range(start, n+1):
                elements.append(i)
                dfs(elements, i+1, k-1)
                elements.pop()
                
        dfs([], 1, k)
        
        return results
```