---
title: Leetcode Biweekly Contest 174
date: 2026-01-17 21:58:14
tags: [leetcode, Biweekly]
mathjax: true
---
## Result
### Solve: 4/4
### Rank: 718
### Rating: 1614
### [link]((https://leetcode.com/contest/biweekly-contest-174/)
![result](https://meee.com.tw/ka8yuAa.png)
---
## Problems and Solution
### Q1. Best Reachable Tower
#### Keywords: 
1. `Manhattan Distance` -> 曼哈頓距離公式：$|x_1 - x_2| + |y_1 - y_2|$
2. `Reachable` -> 條件篩選，判斷距離是否在 `radius` 範圍內。
3. `Maximum Quality` -> `Max Tracking`，維護當前的最大品質因子。
4. `Lexicographically Smallest` -> `字典序比較`，當quality相同時，優先選擇 $x$ 較小，次要選擇 $y$ 較小的座標。

#### Approach:
1. **距離計算**：使用曼哈頓距離公式判斷塔是否可達。
2. **條件更新**：
   - 如果當前塔的quality比 `maxQuality` 高，則直接更新。
   - 如果qualtiy相等，則比對座標：當前的 $x$ 更小，或者 $x$ 相等但 $y$ 更小時進行更新。
3. **初始化**：預設結果為 `[-1, -1]`，品質為 `-1`。

#### Implementation
```cpp
class Solution {
public:
    vector<int> bestTower(vector<vector<int>>& towers, vector<int>& center, int radius) {
        vector<int> bestPos = {-1, -1};
        int maxQuality = -1;

        int cx = center[0];
        int cy = center[1];

        for (const auto& tower : towers) {
            int tx = tower[0];
            int ty = tower[1];
            int tq = tower[2];

            // 1. 計算曼哈頓距離
            int dist = abs(tx - cx) + abs(ty - cy);

            // 2. 篩選可達的塔
            if (dist <= radius) {
                // 3. 更新條件：品質更高，或是品質相同但座標字典序更小
                if (tq > maxQuality) {
                    maxQuality = tq;
                    bestPos = {tx, ty};
                } else if (tq == maxQuality) {
                    // 字典序比較：x 較小，或 x 相同時 y 較小
                    if (tx < bestPos[0] || (tx == bestPos[0] && ty < bestPos[1])) {
                        bestPos = {tx, ty};
                    }
                }
            }
        }
        return bestPos;
    }
};
```

### Q2.Minimum Operations to Reach Target Array
#### Keywords:
1. **Maximal Contiguous Segments** -> 當選擇數值 $x$ 時，陣列中所有值為 $x$ 的區間會同時被更新。
2. **Simultaneous Update** -> 更新是同步的，這意味著操作一次 $x$，所有需要修正的 $x$ 都會被處理。
3. **Greedy Strategy** -> 只需要找出所有「處於錯誤位置」的原始數值 $x$，每個相異的 $x$ 執行一次操作即可。
4. **Set Data Structure** -> 使用 `unordered_set` 來統計需要被操作的相異數值數量。

#### Approach:
這題的核心在於理解操作的範圍。題目規定：當你選擇一個值 $x$，**所有**值為 $x$ 的連續段都會被更新為 `target` 中的對應值。

1. **關鍵觀察**：如果某個索引 $i$ 滿足 `nums[i] != target[i]`，那麼 `nums[i]` 這個值 **至少需要被選擇一次** 作為操作對象 $x$。
2. **連鎖效應**：一旦我們選擇了 $x$，陣列中所有值為 $x$ 的地方都會變更。即使某些地方原本已經等於 `target`，重新更新為 `target` 也不會產生負面影響。
3. **最佳化解法**：我們只需要遍歷陣列，找出所有 `nums[i] != target[i]` 的位置，並紀錄這些位置上的原始數值 `nums[i]`。最後，這些「原始數值」的總數（去重後）即為最少操作次數。

#### Implementation
```cpp
class Solution {
public:
    int minOperations(vector<int>& nums, vector<int>& target) {
        // 使用 Set 紀錄所有需要被變更的「原始數值」
        unordered_set<int> ops;

        int n = nums.size();
        for(int i = 0; i < n; i++){
            // 如果當前位置的值不符合目標
            if(nums[i] != target[i]){
                ops.insert(nums[i]); // 該數值必須被操作一次
            }
        }
        
        // 相異數值的總數即為答案
        return ops.size();
    }
};
```
#### note(why it works):
由於 **「更新所有值為 $x$ 的段」** 這個條件極其強大，它本質上消除了空間位置的限制。只要數字相同，不論它們在哪裡，都會被這一波操作帶走。因此，我們只需要數一數有多少種「壞掉的數字」即可。

### Q3.Number of Alternating XOR Partitions
#### Keywords:
1. **Prefix XOR** -> 利用 $P[i] \oplus P[j] = Target$ 的特性快速尋找切分點。
2. **Dynamic Programming** -> 維護交替狀態（奇數塊與偶數塊）的方案數。
3. **Hash Map Optimization** -> 使用 `unordered_map` 紀錄特定 XOR 值對應的方案總數。
4. **Modulo Arithmetic** -> 處理 $10^9 + 7$ 的大數取mod。
#### Approach
本題要求將陣列切分為連續塊，且區塊的 XOR 值須滿足 $target1, target2, target1, \dots$ 的交替順序。

1. **State Definition**:
   - `even[prefix_xor]`: 以「偶數塊」結尾且當前前綴 XOR 為 `prefix_xor` 的方案數。
   - `odd[prefix_xor]`: 以「奇數塊」結尾且當前前綴 XOR 為 `prefix_xor` 的方案數。
2. **Transition**:
   - 若要形成一個新的 **奇數塊**（XOR 需為 `target1`），我們需要前一個狀態是 **偶數塊**。
     - 條件：$cur\_xor \oplus prev\_xor = target1 \implies prev\_xor = cur\_xor \oplus target1$。
   - 若要形成一個新的 **偶數塊**（XOR 需為 `target2`），我們需要前一個狀態是 **奇數塊**。
     - 條件：$cur\_xor \oplus prev\_xor = target2 \implies prev\_xor = cur\_xor \oplus target2$。
3. **Base Case**: 
   - `even[0] = 1`，代表在索引 -1 處有一個虛擬的偶數塊結尾。
#### Implementation
```cpp
class Solution {
public:
    int alternatingXOR(vector<int>& nums, int target1, int target2) {
        int n = nums.size();
        long long MOD = 1e9 + 7;
        
        // 分別儲存以「偶數區塊」或「奇數區塊」結尾的方案數
        unordered_map<int, long long> even;
        unordered_map<int, long long> odd;
        
        int cur = 0;
        even[0] = 1; // 初始狀態：第 0 塊（視為偶數塊）

        long long wayOdd = 0;
        long long wayEven = 0;

        for(int i = 0; i < n; ++i){
            cur ^= nums[i];
        
            // 嘗試作為「奇數塊」的結尾 (需找前一個是偶數塊且 XOR 差值為 target1)
            wayOdd = 0;
            int lookOdd = cur ^ target1;
            if(even.count(lookOdd)){
                wayOdd = even[lookOdd];
            }
        
            // 嘗試作為「偶數塊」的結尾 (需找前一個是奇數塊且 XOR 差值為 target2)
            wayEven = 0;
            int lookEven = cur ^ target2;
            if(odd.count(lookEven)){
                wayEven = odd[lookEven];
            }

            // 更新 DP 狀態
            if(wayOdd > 0){
                odd[cur] = (odd[cur] + wayOdd) % MOD;
            }
            if(wayEven > 0){
                even[cur] = (even[cur] + wayEven) % MOD;
            }
        }
        
        // 最後一個區塊可能是奇數塊（總塊數為奇）或偶數塊（總塊數為偶）
        return (int)((wayOdd + wayEven) % MOD);
    }
};
```

### Q4.Minimum Edge Toggles on a Tree
#### Keywords:
1. **Tree Edge Toggling** -> 操作一條邊同時翻轉兩個端點。
2. **Bottom-up Greedy** -> 從葉子向根處理，因為葉子的狀態只能由唯一的一條邊決定。
3. **Parity Constraint** -> 總差異數必須為偶數，否則無法成對抵消。
4. **Post-order DFS** -> 先處理子節點，再根據當前節點狀態決定是否操作父邊。

#### Approach
1. **Initial Check**: 計算 `start[i] != target[i]` 的總數。若為奇數，直接判斷為不可能（`-1`）。
2. **State Transfer**: 想像每個節點 $u$ 如果需要翻轉（`diff[u] = 1`），它必須依賴它的「父邊」來修正自己。一旦操作父邊，`diff[u]` 變為 0，而 `diff[parent]` 則會翻轉（1 變 0，或 0 變 1）。
3. **DFS Execution**:
   - 使用 DFS 遍歷到最深處。
   - 在回溯（Post-order）時，若 `diff[u] == 1`，則將該節點與父節點相連的邊加入結果集 `res`，並更新兩者的 `diff` 狀態。
   - 最終回到根節點時，若根節點 `diff[0]` 仍為 1，代表無法完成轉換。
4. **Minimality**: 由於每條邊的操作是唯一的（操作兩次等於沒操作），且每個決策都是為了滿足葉子節點的唯一需求，因此得到的解即為最小長度。

#### Implementation
```cpp
class Solution {
    struct edge {
        int to;
        int id;
    };

    vector<vector<edge>> adj;
    vector<int> diff;
    vector<int> res;
    
    void dfs(int u, int p, int edge_to_parent) {
        // 先向下遞迴至葉子
        for (auto& ed : adj[u]) {
            if (ed.to != p) {
                dfs(ed.to, u, ed.id);
            }
        }

        // 回溯時檢查：如果當前節點狀態不對，必須操作父邊
        if (diff[u] == 1) {
            if (edge_to_parent == -1) return; // 根節點無父邊可操作
            
            res.push_back(edge_to_parent);
            diff[u] ^= 1;          // 修復目前節點
            if (p != -1) diff[p] ^= 1; // 影響傳導至父節點
        }
    }

public:
    vector<int> minimumFlips(int n, vector<vector<int>>& edges, string start, string target) {
        adj.assign(n, vector<edge>());
        diff.resize(n);
        int total_diff = 0;

        for (int i = 0; i < n; ++i) {
            diff[i] = (start[i] != target[i] ? 1 : 0);
            total_diff += diff[i];
        }

        // 奇偶性檢查
        if (total_diff % 2 != 0) return {-1};

        for (int i = 0; i < edges.size(); i++) {
            int u = edges[i][0], v = edges[i][1];
            adj[u].push_back({v, i});
            adj[v].push_back({u, i});
        }

        res.clear();
        dfs(0, -1, -1);

        // 最後檢查根節點是否也被修復
        if (diff[0] != 0) return {-1};

        sort(res.begin(), res.end());
        return res;
    }
};
```

### Conclusion:
* **Q1** 考驗基礎模擬。
* **Q2** 考驗對題目特性的觀察（找出不變量）。
* **Q3** 考驗 Prefix XOR 與 DP 的結合。
* **Q4** 則是經典的樹上貪心，利用 DFS 的回溯特性解決問題。


### note:
這篇題解找了Gemini幫我修飾跟思考，因為這篇題解是打完比賽後一段時間才寫的，
標題的時間是比賽時間，我當下只有把code丟進markdown檔裡。
