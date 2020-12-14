---
title: leetcode入门题目第二弹
date: 2020-12-07 16:03:38
categories: leetcode    
tags: leetcode
---

# leetcode 回顾 从2020-12-7起

## 18四数之和
给定一个数组求n个数组成不重复的排列且这几个数的和为定值
**思路**
首先将数组 numsnums 升序排序，并把答案四元组中没确定的个数设为 nn

我把剪枝分为了 4 类，括号内的是用什么完成剪枝

1. 如果剩余可选的数字数量少于 n，则剪掉（递归返回）；
2. 每层递归的 for 循环中，从第二轮循环开始，如果数组中当前数字和前一个相同，则剪掉（进行下一次循环，这条的任务就是去重）；
3. 如果 当前数字 + 已确定数字的和 + (n - 1) * 排序后数组中当前数字的下一个数字 > target,则说明后面的数无论怎么选，加起来都一定大于 target，所以剪掉（递归返回）；
4. 如果 当前数字 + 已确定数字的和 + (n - 1) * 排序后数组最后一个数字 小于target，则说明后面的数无论怎么选，加起来都一定小于 targettarget，所以剪掉（进行下一次循环）。





**dfs模板解法**
```

class Solution 
{
private:
    vector<vector<int>> ans;
    vector<int> myNums, subans;
    int tar, numSize;
    void DFS(int low, int sum)
    {
        if (sum == tar && subans.size() == 4)
        {
            ans.emplace_back(subans);
            return;
        }
        for (int i = low; i < numSize; ++i)
        {
            if (numSize - i < int(4 - subans.size())) //剪枝
                return;
            if (i > low && myNums[i] == myNums[i - 1]) //去重
                continue; 
            if (i < numSize - 1 && sum + myNums[i] + int(3 - subans.size()) * myNums[i + 1] > tar) //剪枝
                return;
            if (i < numSize - 1 && sum + myNums[i] + int(3 - subans.size()) * myNums[numSize - 1] < tar) //剪枝
                continue;
            subans.emplace_back(myNums[i]);
            DFS(i + 1, myNums[i] + sum);
            subans.pop_back();
        }
        return;
    }
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) 
    {
        sort(nums.begin(), nums.end());
        myNums = nums;
        tar = target;
        numSize = nums.size();
        if (numSize < 4)
            return ans;
        DFS(0, 0);
        return ans;    
    }
};


```
