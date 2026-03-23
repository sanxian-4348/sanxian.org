---
title: zerojudge r488彗星撞擊
date: 2025-10-19 23:00:00
tags:
  - 程式解題
cover: /images/zerojudge.webp
urlname: zeerojuge
---

## r488 彗星撞擊

### 題目描述
在一個大小為 $R \times C$ 的平原上，每個格子的初始高度均為 $D$。平原上分佈著 $K$ 隻恐龍。接下來會有 $M$ 次彗星撞擊，每次撞擊範圍為以 $(a, b)$ 為中心、邊長為 $s$ 的正方形區域，撞擊深度為 $d$。

**撞擊規則：**
1. **優先判定恐龍**：若撞擊範圍內有任何「清醒」的恐龍，彗星會將範圍內所有恐龍砸暈（數量歸零），但**地面高度不會改變**。
2. **地形下陷**：若範圍內**完全沒有**清醒的恐龍，則該區域內地表高度全部**下降 $d$**。

**目標**：輸出最終地圖的 **最高高度**、**最低高度** 以及 **剩餘清醒恐龍總數**。

---

### 解題思路

這是一道典型的 **二維陣列模擬題**。解題的核心在於精確處理座標系統以及「兩階段」的範圍判定。

#### 1. 座標系統與陣列宣告
在 C++ 中，建議使用 `vector<vector<int>>` 或將大陣列宣告在全域，以避免 **Stack Overflow**。
* 座標對應：題目給的 $(a, b)$ 通常是 $(x, y)$。在陣列操作中，我們習慣使用 `mapp[y][x]`，即 `mapp[b][a]`。
* 範圍邊界：以 $(a, b)$ 為中心，範圍是 $b \pm s/2$ 與 $a \pm s/2$。必須加上 `if` 判斷，確保座標不超出 $[0, C-1]$ 與 $[0, R-1]$ 的範圍。



#### 2. 模擬撞擊的兩階段邏輯
每次彗星撞擊時，不能「邊檢查邊扣血」，否則會導致判定錯誤。必須拆分為兩步：
* **第一步（掃描）**：跑一次範圍迴圈，檢查是否有任何 `dinosaur[k][l] > 0`。如果有，記錄 `hasDino = true` 並將該格清零。
* **第二步（執行）**：掃描結束後，根據 `hasDino` 的結果決定是否要再跑一次迴圈來更新 `mapp[k][l]` 的高度。

#### 3. 效能優化
* **時間複雜度**：$O(M \times s^2)$。當 $M$ 與 $s$ 較大時，運算量會逼近 $10^8$。
* **I/O 優化**：使用 `ios::sync_with_stdio(false); cin.tie(0);` 加快讀取速度。

---

### 程式C++

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main() {
    
    ios::sync_with_stdio(false);
    cin.tie(0);

    int R, C, D, K;
    if (!(cin >> R >> C >> D >> K)) return 0;

    
    vector<vector<int>> mapp(C, vector<int>(R, D));
    vector<vector<int>> dinosaur(C, vector<int>(R, 0));

    for (int i = 0; i < K; i++) {
        int r, c;
        cin >> r >> c;
        dinosaur[c][r]++;
    }

    int M;
    cin >> M;
    while (M--) {
        int a, b, s, d;
        cin >> a >> b >> s >> d;

        int t = 0; 
        int r_start = b - s / 2, r_end = b + s / 2;
        int c_start = a - s / 2, c_end = a + s / 2;

        for (int i = r_start; i <= r_end; i++) {
            for (int j = c_start; j <= c_end; j++) {
                if (i >= 0 && i < C && j >= 0 && j < R) {
                    if (dinosaur[i][j] > 0) {
                        t += dinosaur[i][j];
                        dinosaur[i][j] = 0; 
                    }
                }
            }
        }

        if (t == 0) {
            for (int i = r_start; i <= r_end; i++) {
                for (int j = c_start; j <= c_end; j++) {
                    if (i >= 0 && i < C && j >= 0 && j < R) {
                        mapp[i][j] -= d;
                    }
                }
            }
        }
    }

    long long maxH = -2e18, minH = 2e18, totalDino = 0;
    for (int i = 0; i < C; i++) {
        for (int j = 0; j < R; j++) {
            if (mapp[i][j] > maxH) maxH = mapp[i][j];
            if (mapp[i][j] < minH) minH = mapp[i][j];
            totalDino += dinosaur[i][j];
        }
    }

    cout << maxH << " " << minH << " " << totalDino << endl;

    return 0;
}
```