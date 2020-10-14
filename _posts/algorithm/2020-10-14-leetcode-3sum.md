---
title: "[LeetCode]3sum"
date: 2020-10-14 23:11:00 +0800
categories: [algorithm]
tags: [algorithm, leetcode, 3sum]
toc: true
---

# Description
>Given an array nums of n integers, are there elements a, b, c in >nums such that a + b + c = 0? Find all unique triplets in the >array which gives the sum of zero.
>
>Notice that the solution set must not contain duplicate triplets.

주어진 array에서 합이 0이 되는 3개의 숫자를 모두 찾는 문제   
   
# Example
>Input: nums = [-1,0,1,2,-1,-4]  
>Output: [[-1,-1,2],[-1,0,1]]
  

# Solution

1. list를 정렬하여 two pointer 문제로 해결  
2. i, left, right가 동일한 숫자를 만날때 중복해서 처리하지 않도록 처리

```python
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        
        triplets = []
        nums.sort()
        
        for i in range(len(nums) - 2):
            if i > 0 and nums[i-1] == nums[i]:
                continue
                
            left, right = i+1, len(nums)-1
            
            while left < right:
                sum_value = nums[i] + nums[left] + nums[right]
                if sum_value > 0:
                    right -= 1
                elif sum_value < 0:
                    left += 1
                else:
                    triplets.append([nums[i], nums[left], nums[right]])
                    
                    while left < right and nums[left] == nums[left+1]:
                        left += 1
                    while left < right and nums[right] == nums[right-1]:
                        right -= 1
                    left += 1
                    right -= 1
            
        return triplets
```

# Link
<https://leetcode.com/problems/3sum/>