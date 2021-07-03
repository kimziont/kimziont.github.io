---
title:  "[Algorithm_Problem_Graph] 합이 같은 부분 집합"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/data-structure.jpg

categories:
  - graph_problem
tags:
  - data_structure
last_modified_at: 2021-05-06
---  

## 문제  
인프런 문제
난이도: ⭐⭐  
```
집합을 두 개의 부분집합으로 나누었을 때 두 부분집합의 원소의 합이 서로 같은 경우가 존재하면 “YES"를 출력하고, 그렇지 않으면 ”NO"를 출력하는 프로그램을 작성하세요.  
둘로 나뉘는 두 부분집합은 서로소 집합이며, 두 부분집합을 합하면 입력으로 주어진 원래의 집합이 되어 합니다.  
예를 들어 {1, 3, 5, 6, 7, 10}이 입력되면 {1, 3, 5, 7} = {6, 10} 으로 두 부분집합의 합이 16으로 같은 경우가 존재하는 것을 알 수 있다.
```

예시 1  

```
Input: nums = [1, 3, 5, 6, 7, 10]
Output: YES
```


## 풀이 1: DFS  

```python

def main_f(list_1):
    N = len(list_1)
    list_1 = list_1
    tot = sum(list_1)
    half_tot = tot//2
    
    if tot % 2 != 0:
        print("NO")
        return
    
    def DFS(i, sum):
        global switch
        if switch:
            return
        if sum == half_tot:
            print("YES")
            switch = 1
        elif sum < half_tot:
            if i == N:
                return
            DFS(i+1, sum + list_1[i])
            DFS(i+1, sum)
        else:
            return
    
    DFS(0, 0)
    if switch == 0:
        print("NO")

switch = 0
nums = [1, 3, 5, 6, 7, 10]
main_f(nums)
```