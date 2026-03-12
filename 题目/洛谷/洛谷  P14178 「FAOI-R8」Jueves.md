*Algorithm :* [[位运算]]
## 题目描述

小 A 给了你一张 $n$ 个点的无向完全图，每个点有权值，第 $i$ 个点的权值为 $a_i$。连接 $(u,v)$ 的边的权值为 $(a_u\operatorname{xor}a_v)+(a_u\operatorname{or}a_v)+(a_u\operatorname{and}a_v)$，其中 $\operatorname{xor},\operatorname{or},\operatorname{and}$ 分别是二进制下的按位异或、按位或和按位与。

定义一条路径的权值为经过的边的权值和。给出 $s,t$，求出从 $s$ 出发到 $t$ 的路径的最小权值。

## 输入格式

**本题每测试点内含多组数据。**

第一行一个整数 $T$ 代表数据组数。

对于每组测试数据：
- 第一行三个正整数 $n,s,t$，表示点数、起点与终点。
- 第二行 $n$ 个整数，第 $i$ 个是 $a_i$，表示第 $i$ 个点的权值。

## 输出格式

对于每组测试数据，输出一行一个整数表示答案。

## 输入输出样例 #1

### 输入 #1

```
4
2 1 2
1 2
3 1 3
1 2 3
8 1 7
1 4 0 2 5 8 4 6
7 1 1
2 0 0 4 3 1 1
```

### 输出 #1

```
6
6
10
0
```

## 说明/提示

**【样例解释】**

对于第一组数据，唯一的路径是 $1\to 2$，权值为 $(1\operatorname{xor}2)+(1\operatorname{or}2)+(1\operatorname{and}2)=3+3+0=6$。

对于第二组数据，一种最优的路径是 $1\to 3$，权值为 $(1\operatorname{xor}3)+(1\operatorname{or}3)+(1\operatorname{and}3)=2+3+1=6$。

对于第三组数据，一种最优的路径是 $1\to 7$，权值为 $(1\operatorname{xor}4)+(1\operatorname{or}4)+(1\operatorname{and}4)=5+5+0=10$。

**【数据范围】**

**本题开启子任务捆绑测试。**

- Subtask 1（40 pts）：$n\le 10$，$\sum n\le 10^3$。
- Subtask 2（30 pts）：$a_i\le 10^3$。
- Subtask 3（30 pts）：无特殊限制。

记 $\sum n$ 为单测试点内每组测试数据 $n$ 之和。

对于所有数据，$1\le T\le 10^5$，$1\le n,\sum n\le 5\times 10^5$，$0\le a_i\le 10^{18}$，$1\le s,t\le n$。
### 思路：
1. 注意到 $(x~xor~y) + (x~and~y)=x~or~y$ 
2. 连接两条边的边权即为 $2(x~or~y)$ 
3. 对于一条经过 $p_{1},p_{2},\dots,p_{k}$ 的路径：$$2(a_{p_{1}}~or~a_{p_{2}}+\dots+2(a_{p_{n-1}}~or~a_{p_{n}}))$$$$=2((a_{p_{1}}~or~a_{p_{2}})+\dots+(a_{p_{n-1}}~or~a_{p_{n}}))$$$$\geq((a_{p_{1}}~or~a_{p_{2}})or \dots or(a_{p_{n-1}}ora_{p_{n}}))$$$$=2(a_{p_{1}}~or~a_{p_{2}}~or~\dots or~a_{p_{n}})$$$$\geq 2(a_{p_{1}}~or~a_{p_{n}})$$
4. 故答案为 $2(a_{s}~or~a_{t})$ ，注意特判 $s==t$ ,此时答案为0
### code:
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
typedef long long ll;


void solve() {
    int n,s,t;
    cin >> n >> s >> t;
    vector<ll> a(n + 1);
    for (int i = 1 ; i <= n ; ++i) {
        cin >> a[i];
    }    

    if (s == t) {
        cout << 0 << '\n';
        return;
    }
    cout << 2 * (a[s] | a[t]) << '\n';
}               

int main(){
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);

    int T = 1;
    cin >> T;
    while (T--) {
        solve();   
    }
    return 0;
}
```