---
title: Dutch National Flag | Sort Alg
date: 2024-07-04 17:42:22
tags: 
- LeetCode
- Algorithm
categories: Algorithm
---
中譯是荷蘭國旗演算法，目的在排序3種不同的元素陣列做排序，例如: [2, 0, 1, 2, 0, 1] ⇒ [0, 0, 1, 1, 2, 2]，當初為了解決一個涉及紅白藍(荷蘭國旗)的問題而提出的。

## 思路

通過三個指針(low, mid, high)對陣列進行遍歷和交換，low用來標記0結束的位置，high用來標記2開始的位置，mid用來遍歷

步驟如下:

1. 初始化low, mid放在陣列開始位置，high放在陣列結束位置
2. 當mid小於high時，執行以下操作:
。當陣列[mid]===0，mid與low值互換，然後mid & low 指針向後一位
。當陣列[mid]===1，mid指針向後一位
。當陣列[mid]===2，mid與high值互換，然後high 指針向前一位
3. 當mid大於high，結束遍歷

## Code

```jsx
var sortColors = function(nums) {
    let low = 0, mid = 0, high = nums.length - 1
    while (mid <= high) {
        if (nums[mid] === 0) {
            swap(low++, mid++)
        } else if (nums[mid] === 1) {
            mid++
        } else {
            swap(mid, high--)
        }
    }

    return nums

    function swap(idx1, idx2) {
        let temp = nums[idx1]
        nums[idx1] = nums[idx2]
        nums[idx2] = temp
    }
};
```

參考資料:

https://www.geeksforgeeks.org/sort-an-array-of-0s-1s-and-2s/https://blog.csdn.net/DevNinja/article/details/132822686