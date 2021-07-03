---
title:  "[Algorithm_Problem_Graph] 순열"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/data-structure.jpg

categories:
  - graph_problem
tags:
  - data_structure
last_modified_at: 2021-05-02
---  

## 문제  
리트코드 46. Permutations  
난이도: ⭐⭐  
```
Given an array nums of distinct integers, return all the possible permutations. You can return the answer in any order.
(서로 다른 정수를 입력받아 가능한 모든 순열을 리턴하라.)
```

예시 1  

```
Input: nums = [1,2,3]
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

예시 2  
```
Input: nums = [0,1]
Output: [[0,1],[1,0]]
```

## 풀이 1: DFS를 활용한 순열 생성

```python
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        results = []
        prev_elements = []
        
        def dfs(elements):
            if len(elements) == 0:
                results.append(prev_elements[:])
                
            for e in elements:
                next_elements = elements[:]
                next_elements.remove(e)
                
                prev_elements.append(e)
                dfs(next_elements)
                prev_elements.pop()
        
        dfs(nums)
        
        return results
```