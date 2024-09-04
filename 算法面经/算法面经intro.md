# 算法工程师面经

> 算法工程师面试问题及相关资料搜集

## 参考资料

1. [Awesome-algorithm-interview](https://github.com/lcylmhlcy/Awesome-algorithm-interview/tree/master)
2. [代码随想录](https://programmercarl.com/)
3. [DeepLearing-Interview-Awesome-2024
](https://github.com/315386775/DeepLearing-Interview-Awesome-2024)
4. [AIGC_Interview
](https://github.com/EmbraceAGI/AIGC_Interview)

## 刷题记录





### 双指针
1. [Leetcode: 167. 两数之和 II - 输入有序数组](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted/description/) - 20240903

#### 题目描述

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/B7k1v5.png)

#### 解题思路：**双指针法**
> - 从左右两边开始遍历，如果和大于target，将右指针减一；如果和小于> target，左指针+1；如果和等于target，则返回两个指针的位置。
> - time complexity: O(n)
> - space complexity: O(1)

```python
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        left, right = 0, len(nums)-1
        while left < right:
            s = nums[left] + nums[right]
            if s == target:
                break
            elif s > target:
                right -= 1
            else:
                left +=1
        return [left+1, right+1]
```

## 算法面经