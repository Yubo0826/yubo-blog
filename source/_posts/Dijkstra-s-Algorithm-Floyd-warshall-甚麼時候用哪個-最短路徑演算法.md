---
title: Dijkstra's Algorithm & Floyd warshall 甚麼時候用哪個? | 最短路徑演算法
date: 2024-08-14 10:50:17
tags: 
- LeetCode
- Algorithm
- Dijkstra
- Floyd warshall
- 最短路徑
---
leetcode 題目: [**2976. Minimum Cost to Convert String I**](https://leetcode.com/problems/minimum-cost-to-convert-string-i/description/?envType=daily-question&envId=2024-07-29)

## **解法一: Dijkstra's Algorithm**

一開始是用Dijkstra's Algorithm做，但發現會Time Limit Exceeded，留言區有些人說會TLE，但也有些人不會，寫法可能有加強空間。

更: editPath 迴圈忘記break，以及新增記憶hashmap，ex: 算出a→b最小路徑為3，紀錄以防下次再遇到，就可以成功submit，但效率很慘。

完整的code:

```jsx
/**
 * @param {string} source
 * @param {string} target
 * @param {character[]} original
 * @param {character[]} changed
 * @param {number[]} cost
 * @return {number}
 */
var minimumCost = function(source, target, original, changed, cost) {
    // Build graph
    class Graph {
        constructor() {
            this.Nodelist = {}
        }

        addNode(node) {
            if (!this.Nodelist[node]) this.Nodelist[node] = []
        }

        addEdge(node1, node2, weight) {
            this.Nodelist[node1].push({node: node2, weight})
            // 因為這題是有向圖，所以這行註解
            // this.Nodelist[node2].push({node: node1, weight})
        }

        checkNodeExist(node) {
            if (this.Nodelist[node]) {
                return true
            } else {
                return false
            }
        }

        dijkstra(start, end) {
            if (!this.checkNodeExist(start) && !this.checkNodeExist(end)) return -1
            const queue = new Queue()
            let smallest

            // Build initial state
            for (let node in this.Nodelist) {
                if (node === start) {
                    queue.enqueue(node, 0)
                } else {
                    queue.enqueue(node, Infinity)
                }
            }

            while (queue.values.length) {
                // {val: 'A', path: 0}
                smallest = queue.dequeue()

                if (smallest.val === end) {
                    if (smallest.path === Infinity) {
                        return -1
                    }
                    return smallest.path
                }
                
                if (smallest.val) {
                    for (let {node, weight} of this.Nodelist[smallest.val]) {
                        queue.editPath(node, weight + smallest.path)
                    }
                }

            }
        }
    }

    class Queue {
        constructor() {
            this.values = []
        }

        enqueue(val, path) {
            this.values.push({val, path})
        }

        dequeue() {
		        // 從陣列取出path最小的obj
            if (this.values.length === 0) return null

            let minValueObj = this.values.reduce((min, current) => {
                return current.path < min.path ? current : min
            })
            let index = this.values.findIndex(item => item.path === minValueObj.path)
            if (index !== -1) {
                this.values.splice(index, 1)
            }
            return minValueObj
        }

        editPath(node, path) {
            for (let i = 0; i < this.values.length; i++) {
               if (this.values[i].val === node) {
                this.values[i].path = Math.min(path, this.values[i].path)
                break
               }
            }
        }
        
    }

    let graph = new Graph()
    // Build graph
    for (let i = 0; i < original.length; i++) {
        graph.addNode(original[i])
        graph.addNode(changed[i])
        graph.addEdge(original[i], changed[i], cost[i])
    }
    // Calculate total cost
    let totalCost = 0
    let _cost
    for (let i = 0; i < source.length; i++) {
        let pathStr = source[i] + target[i]
        if (costMem.has(pathStr)) {
            _cost = costMem.get(pathStr)
        } else {
            _cost = graph.dijkstra(source[i], target[i])
            costMem.set(pathStr, _cost)
        }
        if (_cost === -1) return -1
        totalCost += _cost
    }
    return totalCost
};
```

![Untitled](1.png)

## 解法二: **Floyd-warshall Algorithm**

後來發現留言區更多人使用Floyd warshall，因為他用來處理有向圖的兩點最短路徑問題，比較符合這題情況

```jsx
class Solution {
    minimumCost(source, target, original, changed, cost) {
        let total_cost = 0;

        let min_cost = Array.from({ length: 26 }, () => Array(26).fill(Infinity));

        // 初始化距離矩陣
        for (let i = 0; i < original.length; i++) {
            let start_char = original[i].charCodeAt(0) - 'a'.charCodeAt(0);
            let end_char = changed[i].charCodeAt(0) - 'a'.charCodeAt(0);
            min_cost[start_char][end_char] = Math.min(min_cost[start_char][end_char], cost[i]);
        }

        // Floyd-Warshall算法的主要邏輯
        for (let k = 0; k < 26; k++) {
            for (let i = 0; i < 26; i++) {
                for (let j = 0; j < 26; j++) {
                    min_cost[i][j] = Math.min(min_cost[i][j], min_cost[i][k] + min_cost[k][j]);
                }
            }
        }

        // 計算total_cost
        for (let i = 0; i < source.length; i++) {
            let src = source[i];
            let tgt = target[i];
            if (src === tgt) continue;

            let source_char = src.charCodeAt(0) - 'a'.charCodeAt(0);
            let target_char = tgt.charCodeAt(0) - 'a'.charCodeAt(0);

            // If the transformation is not possible, return -1
            if (min_cost[source_char][target_char] === Infinity) {
                return -1;
            }
            total_cost += min_cost[source_char][target_char];
        }

        return total_cost;
    }
}

```

![Untitled](2.png)

## 結論

如果節點數量是已知的，那Floyd-Warshall是個不錯的方法，像是這題因為字母有26個，節點的最大數量就是26，所以時間複雜度是O(26 ^ 3)，遠比dijkstra好。