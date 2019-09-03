---
title: 最大子数组求解以及逐步优化
date: '2019-05-30T22:55:33.000Z'
categories:
  - 算法
tags:
  - 动态规划
  - 最大子数组
  - kanane
---

# 最大子数组求解以及逐步优化

[leetcode 传送门](https://leetcode.com/problems/maximum-subarray/)

## 暴力解法

求出所有子数组的和，numS\[i,,j\]（意味着子数组 下标i到j）,所以i从0开始到数组最后一位，j从i开始到数组最后一位，算出所有的子数组和 temp，max取temp的最大值即可

> 时间复杂度 O\(n2\)

```java
    public int maxSubArrayByForce(int[] nums) {
        int max = nums[0];
        for (int i = 0; i < nums.length; i++) {
            int temp = 0;
            for (int j = i; j < nums.length; j++) {
                temp += nums[j];
                max = temp > max ? temp : max;
            }
        }
        return max;
    }
```

## 动态规划（算法）

算法思路：用一个dp数组来记录当前位置的子数组和dp\[i\]，dp\[i\]的和依赖于dp\[i-1\]+nums\[i\]的和 sum， 1. 如果sum小于0，说明这一次的计算不符合要求，dp\[i\] = 0，此时相当于 dp\[i+1\] 需要从新计算子数组和 2. 只要sum大于0，那么 dp\[i\] = sum，一直往下加

而 max 最大值等于 dp\[0,length\) 的 最大值

```java
    public int maxSubArray(int[] nums) {
        int[] dp = new int[nums.length];
        int max = nums[0];
        dp[0] = nums[0];
        for (int index = 1; index < nums.length; index++) {
            dp[index] = nums[index] + (dp[index - 1] > 0 ? dp[index - 1] : 0);
            max = dp[index] > max ? dp[index] : max;
        }
        return max;
    }
```

> 时间复杂度 O\(n\) 空间复杂度 O\(n\)

## 最节省内存的动态规划

在上面一步你可以看到，没必要使用整个dp数组来记录临时的最大值，仅仅需要的变量便是dp\[i-1\]（dp\[i\]需要dp\[i-1\]），这样在空间优化到了O\(1\)

```java
    public int maxSubArray(int[] nums) {
        int max = nums[0];
        int temp = nums[0];
        for (int index = 1; index < nums.length; index++) {
            temp = nums[index] + (temp > 0 ? temp : 0);
            max = temp > max ? temp : max;
        }
        return max;

    }
```

> 时间复杂度 O\(n\) 空间复杂度 O\(1\)

下面的题型和最大子数组是一个类型

相关链接：股票买入卖出时间 [leetcode传动门](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

todo 待补充 原算法链接

