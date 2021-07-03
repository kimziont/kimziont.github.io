---
title:  "[Algorithm_Problem_String] 그룹 애너그램"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/data-structure.jpg

categories:
  - string_problem
tags:
  - data_structure
last_modified_at: 2021-04-09
---  

## 문제  
리트코드 49. Group Anagrams  
난이도: ⭐⭐  

```
Given an array of strings strs, group the anagrams together. You can return the answer in any order.

An Anagram is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.
(문자열 배열을 받아 애너그램 단위로 그루핑하라)
```  

예시 1   
```
Input: strs = ["eat","tea","tan","ate","nat","bat"]
Output: [["bat"],["nat","tan"],["ate","eat","tea"]]
```  

예시 2  
```
Input: strs = [""]
Output: [[""]]
```

### 나의 풀이:  
```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        group_list = [[strs[0]]]
        for word in strs[1:]:
            for group in group_list:
                if sorted(word) == sorted(group[0]):
                    group.append(word)
                    break
                else:
                    continue
            else:
                group_list.append([word])
        return group_list
------------------------------------------------------------------------------------
결과: 통과, 48ms
```

### 풀이 2: 정렬해서 딕셔너리에 추가
```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        anagrams = collections.defaultdict(list)
        
        for word in strs:
            anagrams[''.join(sorted(word))].append(word)
        return list(anagrams.values())
--------------------------------------------------------------------------------------
결과: 통과, 40ms
```

