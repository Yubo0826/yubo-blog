---
title: "leetcode 136.\_Single Number | XOR的特性"
date: 2022-02-16 13:46:15
tags: 
- LeetCode
- XOR
categories: LeetCode
---
## 題目

Given a **non-empty** array of integers `nums`, every element appears *twice* except for one. Find that single one.

You must implement a solution with a linear runtime complexity and use only constant extra space.

**Example 1:**

```
Input: nums = [2,2,1]
Output: 1
```

**Example 2:**

```
Input: nums = [4,1,2,1,2]
Output: 4
```

**Example 3:**

```
Input: nums = [1]
Output: 1
```

**Constraints:**

- `1 <= nums.length <= 3 * 104`
- `3 * 104 <= nums[i] <= 3 * 104`
- Each element in the array appears twice except for one element which appears only once.

## 解題思路

題目要求，輸入一個陣列，裡面同一元素皆會出現兩次，只會有一個元素是只有出現一次，而那個元素被要求要輸出出來。

看到題目的當下，很容易本能地使用排序，但排序的時間複雜度上限是O(nlogn)，不符合題目linear runtime complexity的要求。

這題的解題關鍵是邏輯運算符 XOR (exclusive or 異或)，符號為 XOR 或 EOR 或 ⊕，在程式語言中常用 `^`，邏輯定義為:

1 ⊕ 1 = 0

0 ⊕ 0 = 0

1 ⊕ 0 = 1

0 ⊕ 1 = 1

### XOR 有甚麼特性呢?

根據定義可以很容易得出兩個特性:

> 恆等律: `X ^ 0 = X`  歸零律: `X ^ X = 0`
> 

利用真值表可以證明(證明過程不細講):

> 交換律: `A ^ B = B ^ A`
> 

> 結合律: `A ^ (B ^ C) = (A ^ B) ^ C`
> 

有了歸零律和結合律，就可以證明:

> A ^ B ^ B = A ^ 0 = A
> 

有了上面那項特性，就可以輕鬆完成這道題目，假如一個數組 A B C B C D A，使用 XOR判斷哪個元素只出現一次:

```sql
A ^ B ^ C ^ B ^ C ^ D ^ A
= A ^ A ^ B ^ B ^ C ^ C ^ D
= 0 ^ 0 ^ 0 ^ D
= 0 ^ D
= D
```

當然也就符合時間複雜度 O(n)的題目要求。

## 程式碼

```sql
var singleNumber = function(nums) {
    let res = 0
    for(let num of nums){
        res ^= num
    }
    return res
};
```

## 參考

http://lijinma.com/blog/2014/05/29/amazing-xor/

裡面很詳細的 XOR特性證明和應用部分~