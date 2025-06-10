---
title: 滑动窗口 | 无重复字符的最长子串
date: 2025-1-29
comments: false
categories:
    - [算法, LeetCode]
tags: 
    - 数组
    - 哈希表
    - 滑动窗口
description: |
---

## 题目解析

给定一个字符串 `s` ，请找出其中不含重复字符的 **最长** <font color="blue"> 子串 </font> 的长度。

**示例1：**

> **输入：** s = "abcabcbb"
> 
> **输出：** 1
> 
> **解释：** 因为无重复字符的最长子串是 `"abc"`，所以其长度为 3。


**示例 2:**

> **输入:** s = "pwwkew"
> **输出:** 3
> **解释:** 因为无重复字符的最长子串是 `"wke"`，所以其长度为 3。
> 
>     请注意，你的答案必须是 **子串** 的长度，`"pwke"` 是一个*子序列*，不是子串。

**解题思路：**

- 使用 **滑动窗口** 定位 <font color="blue"> 子串 </font> 的范围

- 使用 **集合/哈希表** 记录先前出现过的字符
  
  - 新字符：扩大滑动窗口
  
  - 重复字符：缩小滑动窗口

- 记录 **最大** 的窗口数量

## 滑动窗口

**滑动窗口** 本质上是一种在数组上使用 **双指针** 同向移动的技巧，使用滑动窗口解决的问题通常是暴力破解的优化方案。

初始化时，将两个指针同时指向数组首地址。

![初始化](https://images.rescld.cn/20250129211640.png)

判断集合中是否存在下一个字符，若不存在（子串中未出现该字符），将右指针向右移动。

![扩大窗口](https://images.rescld.cn/20250129213449.png)

若集合中存在下一个字符（出现重复字符），将左指针向右移动，同时将左边的字符从集合中移除。

直到集合中不存在下一个字符为止，继续右移右指针。

![滑动窗口](https://images.rescld.cn/20250129215326.png)

```Java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        Set<Character> set = new HashSet<>();
        int result = 0;
        int len = 0; // 滑动窗口长度
        for(int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            while(set.contains(ch)) {
                // 先前出现过该字符
                char left = s.charAt(i - len);
                set.remove(left);
                len--;
            }
            // 无重复
            len++;
            set.add(ch);
            result = result > len ? result : len;
        }
        return result;
    }
}
```

## 优化

前面的代码使用集合类型 `Set` 实现业务逻辑更方便，但从效率上原生类型速度更快。

ASCII码字符串对应的范围在 $0 \sim 127$ 之间，可以创建一个长度128的数组当哈希表使用。

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        char[] str = s.toCharArray();
        char[] set = new char[128];

        int result = 0;
        int len = 0; // 滑动窗口长度
        for(int i = 0; i < str.length; i++) {
            int n = (int)str[i];
            while(set[n] > 0) {
                // 先前出现过该字符
                int left = (int)str[i-len];
                set[left]--;
                len--;
            }
            // 无重复
            len++;
            set[n]++;
            result = result > len ? result : len;
        }
        return result;
    }
}
```


