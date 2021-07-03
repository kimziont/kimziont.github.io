---
title:  "[Algorithm_Problem_Array] 빗물 트래핑"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/data-structure.jpg

categories:
  - array_problem
tags:
  - data_structure
last_modified_at: 2021-05-02
---  

## 문제  
리트코드 42. Trapping Rain Water   
난이도: ⭐⭐⭐  
```
Given n non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it can trap after raining.
(높이를 입력으로 받아 비가 온 후 얼마나 많은 물이 쌓일 수 있는지 계산하라.)
```

예시 1  

```
Input: height = [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
Explanation: The above elevation map (black section) is represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. In this case, 6 units of rain water (blue section) are being trapped.
```

예시 2  

```
Input: height = [4,2,0,3,2,5]
Output: 9
```  
------------------------------------------------------------------------  
### 나의 풀이: 투 포인터를 이용
```python
class Solution:
    def trap(self, height: List[int]) -> int:
        init_sum = sum(height)
        left = 0; right = len(height) - 1; cur = 0

        while left < right:
            if height[left] <= cur:
                left += 1
            if height[right] <= cur:
                right -= 1

            for i in range(left, right+1):
                if height[i] < min(height[left], height[right]):
                    height[i] = min(height[left], height[right])
            cur = min(height[left], height[right])
        return (sum(height) - init_sum)
------------------------------------------------
결과: 통과, 런타임 40ms  

-------------------------------------------------------------------------
더 나은 풀이
class Solution:
    def trap(self, height: List[int]) -> int:
        if not height:
            return 0
        
        volume = 0
        left, right = 0, len(height)-1
        left_max, right_max = height[left], height[right]

        while left < right:
            left_max, right_max = max(height[left], left_max), max(height[right], right_max)

            if left_max <= right_max:
                volume += left_max - height[left]
                left += 1
            else:
                volume += right_max - height[right]
                right -= 1
        return volume
```


### 풀이 2: 스택 쌓기  

```python

```  