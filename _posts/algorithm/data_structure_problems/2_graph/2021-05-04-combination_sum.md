---
title:  "[Algorithm_Problem_Graph] 조합의 합"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/data-structure.jpg

categories:
  - graph_problem
tags:
  - data_structure
last_modified_at: 2021-05-04
---  

## 문제  
리트코드 39. Combunation Sum  
난이도: ⭐⭐  
```
Given an array of distinct integers candidates and a target integer target, return a list of all unique combinations of candidates where the chosen numbers sum to target. You may return the combinations in any order.

The same number may be chosen from candidates an unlimited number of times. Two combinations are unique if the frequency of at least one of the chosen numbers is different.
(숫자 집합 candidates를 조합해 합이 target이 되는 원소를 나열하라. 각 원소는 중복으로 나열 가능하다.)
```

예시 1  

```
Input: candidates = [2,3,6,7], target = 7
Output: [[2,2,3],[7]]
```

예시 2  
```
Input: candidates = [2], target = 1
Output: []
```

## 풀이 1: DFS로 중복 조합 그래프 탐색

```python
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        result = []
        
        def dfs(csum, index, path):
            if csum < 0:
                return
            if csum == 0:
                result.append(path)
                return
            
            for i in range(index, len(candidates)):
                dfs(csum - candidates[i], i, path+[candidates[i]])
        
        dfs(target, 0, [])
        
        return result
```