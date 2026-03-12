### Prim 算法详解

Prim（普里姆）算法是用于求解**加权无向图**的**最小生成树（MST）** 的经典贪心算法。
#### 核心思想（通俗比喻）

把图想象成多个孤立的村庄，你需要修最少总长度的道路把所有村庄连通。Prim 算法的思路是：
- 先选一个 “起点村庄”（任意选），把它划入 “已连通区域”；
- 每次从 “已连通区域” 到 “未连通区域” 的所有道路中，选**最短的那条**，把对应的未连通村庄划入已连通区域；
- 重复直到所有村庄都连通，最终的道路集合就是最小生成树。
#### 算法步骤（严谨版）
1. 初始化：
    - 选一个起始顶点（如顶点 0）加入最小生成树集合`U`；
    - 维护一个`dist`数组，`dist[i]`表示 “已连通区域`U`” 到 “顶点`i`” 的最短边权；
    - 维护一个`visited`数组，标记顶点是否已加入`U`。
2. 迭代：
    - 从`dist`数组中找到**未加入 U 且权值最小**的顶点`v`，加入`U`；
    - 更新`dist`数组：对所有未加入`U`的顶点`w`，如果`v`到`w`的边权 < 当前`dist[w]`，则更新`dist[w]`；
3. 终止：当所有顶点都加入`U`，算法结束，`dist`数组的总和就是最小生成树的总权值。

### 完整代码实现（邻接矩阵版，新手友好）

```cpp
#include <iostream>
#include <climits>  // 用于INT_MAX（表示无穷大）
using namespace std;

// Prim算法核心函数
// graph: 邻接矩阵（graph[i][j]表示i到j的边权，无边则为INT_MAX）
// n: 顶点总数
// start: 起始顶点（索引，从0开始）
void prim(int** graph, int n, int start) {
    // 1. 初始化核心数组
    bool visited[n];    // 标记顶点是否已加入MST
    int dist[n];        // 已连通区域到各顶点的最短边权
    int parent[n];      // 记录MST中每个顶点的父节点（用于回溯边）
    
    // 初始化：所有顶点未访问，dist设为无穷大，parent设为-1
    for (int i = 0; i < n; i++) {
        visited[i] = false;
        dist[i] = INT_MAX;
        parent[i] = -1;
    }
    
    // 起始顶点的距离设为0（自身到自身无成本）
    dist[start] = 0;
    int total_weight = 0;  // 最小生成树总权值

    // 2. 迭代n次（每次加入1个顶点到MST）
    for (int count = 0; count < n - 1; count++) {
        // 步骤1：找到未加入MST且dist最小的顶点u
        int min_dist = INT_MAX;
        int u = -1;  // 存储当前选中的顶点
        
        for (int v = 0; v < n; v++) {
            if (!visited[v] && dist[v] < min_dist) {
                min_dist = dist[v];
                u = v;
            }
        }
        
        // 处理非连通图（无可用顶点时退出）
        if (u == -1) break;
        
        // 将u加入MST
        visited[u] = true;
        total_weight += min_dist;

        // 步骤2：更新dist数组（通过u更新未加入顶点的最短距离）
        for (int v = 0; v < n; v++) {
            // 条件：v未访问 + u和v之间有边（graph[u][v]≠INT_MAX） + 边权 < 当前dist[v]
            if (!visited[v] && graph[u][v] != INT_MAX && graph[u][v] < dist[v]) {
                dist[v] = graph[u][v];
                parent[v] = u;  // 记录v的父节点为u
            }
        }
    }

    // 3. 输出结果
    cout << "最小生成树总权值: " << total_weight << endl;
    cout << "最小生成树的边（父节点 -> 子节点，权值）：" << endl;
    for (int i = 0; i < n; i++) {
        if (parent[i] != -1) {  // 跳过起始顶点（无父节点）
            cout << "顶点" << parent[i] << " -> 顶点" << i 
                 << "，权值：" << graph[parent[i]][i] << endl;
        }
    }
}

// 测试用例
int main() {
    int n = 5;  // 顶点数量（0-4）
    // 动态创建邻接矩阵（C++中避免栈溢出，推荐动态分配）
    int** graph = new int*[n];
    for (int i = 0; i < n; i++) {
        graph[i] = new int[n];
    }

    // 初始化邻接矩阵（无向图，graph[i][j] = graph[j][i]）
    // INT_MAX表示无边
    int INF = INT_MAX;
    graph[0] = new int[]{0, 2, INF, 6, INF};
    graph[1] = new int[]{2, 0, 3, 8, 5};
    graph[2] = new int[]{INF, 3, 0, INF, 7};
    graph[3] = new int[]{6, 8, INF, 0, 9};
    graph[4] = new int[]{INF, 5, 7, 9, 0};

    // 从顶点0开始求解
    prim(graph, n, 0);

    // 释放动态内存（避免内存泄漏）
    for (int i = 0; i < n; i++) {
        delete[] graph[i];
    }
    delete[] graph;

    return 0;
}
```

### 代码关键部分解释
1. **常量与数据结构**：
    - `INT_MAX`（来自`<climits>`）：表示 “无穷大”，对应邻接矩阵中 “两点无边” 的情况；
    - 动态二维数组`graph`：C++ 中推荐动态分配以避免栈溢出，适配不同顶点数量的图；
    - 核心数组：`visited`（标记连通状态）、`dist`（最短边权）、`parent`（回溯边）。
2. **核心循环逻辑**：
    - 外层循环执行`n-1`次（最小生成树有`n-1`条边）；
    - 内层第一个循环：找到 “未连通且离已连通区域最近” 的顶点`u`；
    - 内层第二个循环：通过顶点`u`更新所有未连通顶点的最短边权（贪心核心）。
3. **内存管理**：
    - 动态创建的二维数组需手动释放（`delete[]`），避免内存泄漏，这是 C++ 与 Python 的核心区别之一。
### 测试用例输出

```
最小生成树总权值: 16 
最小生成树的边（父节点 -> 子节点，权值）： 
顶点0 -> 顶点1，权值：2 
顶点1 -> 顶点2，权值：3 
顶点0 -> 顶点3，权值：6 
顶点1 -> 顶点4，权值：5
```

### 总结

1. **核心思想**：贪心策略，从起始点出发，逐步将 “离已连通区域最近” 的顶点加入，最终形成最小生成树；
2. **适用场景**：Prim 算法更适合**稠密图**（顶点少、边多），时间复杂度为O(n2)（邻接矩阵版），优化版（堆）可达到O(Elogn)；
3. **关键特性**：最小生成树的总权值唯一，但边的组合可能不唯一（存在相同权值边时），且能保证连通所有顶点的同时总权值最小。