---
title: 拓樸排序 Topological Sort
date: 2024-07-14 11:01:16
tags: 
- LeetCode
- Algorithm
- Topological Sort
---
### 認識Topological Sort

https://sites.google.com/view/zsgititit/home/jin-jiec-cheng-shi-she-ji-2/tu-lun-topology-sort

### 演算法

利用「拔點」的方式，推導拓樸排序

- 計算圖中各點 indegree(被連入邊數，又稱「入度」)，即前置工作量
- 將所有圖中 indegree 為 0 的點，即無前置工作者，放入待拔點 queue 中
- 重複以下動作，直到清空 queue:
1. 從 queue 中拔除點i，並將所有與點i 相連的點j indegree 減 1
2. 若該相連點j indegree 減至 0，則放入待拔點 queue 中

### 實戰演練

https://leetcode.com/problems/build-a-matrix-with-conditions/description/

利用拓樸排序來給予1~k指定列&行的位置，如果圖產生cycle，則代表有矛盾發生(如題目第二個範例)

![70882_0.jpg](1.jpg)

```jsx
var buildMatrix = function(k, rowConditions, colConditions) {
    function createOrder(conditions) {
        let theMap = new Map()
        for (let i = 1; i <= k; i++) {
            theMap.set(i, 0)
        }
        for (let [start, end] of conditions) {
            theMap.set(end, theMap.get(end) + 1)
        }

        let queue = []
        let order = []
        for (let [key, value] of theMap) {
            if (value === 0) {
                queue.push(key)
            }
        }

        while (queue.length > 0) {
            let n = queue.shift()
            order.push(n)
            for (let [start, end] of conditions) {
                if (start === n) {
                    theMap.set(end, theMap.get(end) - 1)
                    if (theMap.get(end) === 0) queue.push(end)
                }
            }
        }

        return order
    }

    let rowOrder = createOrder(rowConditions)
    let colOrder = createOrder(colConditions)

    // If cycle
    if (rowOrder.length !== k || colOrder.length !== k) return []

    let res = new Array(k).fill(0)
    for (let i = 0; i < k; i++) {
        res[i] = new Array(k).fill(0)
        res[i][colOrder.indexOf(rowOrder[i])] = rowOrder[i]
    }

    return res

};
```
