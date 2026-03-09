*Algorithm :* [[图论]]，[[动态规划]]
## 描述

Rinne  最近了解了如何快速维护可支持插入边删除边的图，并且高效的回答一下奇妙的询问。

她现在拿到了一个 $n$ 个节点 $m$ 条边的无向连通图，每条边有一个边权 $w_{i}$​

现在她想玩一个游戏：选取一个 “重要点” $S$，然后选择性删除一些边，使得原图中所有除 $S$ 之外度为 1 的点都不能到达 $S$。

定义删除一条边的代价为这条边的边权，现在 Rinne 想知道完成这个游戏的最小的代价，这样她就能轻松到达 rk1 了！作为回报，她会让你的排名上升一定的数量。
### 输入描述：

第一行三个整数 $N,M,S$ ，意义如「题目描述」所述。  
  
接下来 $M$ 行，每行三个整数 $u,v,w$  代表点 $u$ 到点 $v$ 之间有一条长度为 $w$ 的无向边。
### 输出描述：

一个整数表示答案。
## 示例1

输入：
```
4 3 1 
1 2 1 
1 3 1 
1 4 1
```

输出：
```
3
```

说明：

需要使得点 2,3,4 不能到达点 1，显然只能删除所有的边，答案为 3

## 示例2

输入：
```
4 3 1 
1 2 3 
2 3 1 
3 4 2
```

输出：
```
1
```

说明：

需要使得点 4 不能到达点 1，显然删除边 2↔32↔3是最优的。

## 备注：

$2\leq S\leq N\leq 10^5,M=N-1$ ，保证答案在 C++ long long 范围内。

### 思路：
1. 注意有一个很强的条件 $M=N-1$ ,这说明该无向图是一棵树，度为 1 的是叶子节点（一个点连几条边就是几度）
2. ![[牛客  Rinne Loves Edges.svg]]
3. 题目就是要让4，5，6，8，9这些点到不了1，**我们要用最小的代价，使得当前根节点和所有叶子节点不相连** 
4. 需要用树形dp，设dp\[ i ]为底面的点都到不了i 点的最小代价
5. 所以可以得到转移方程：$f[u]=\sum min(w,f[v])$ （w表示u到某一分支的权值）
6. 树形dp本质上是个搜索，遍历这棵树，在返回的时候维护相关的值
### code:
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
typedef long long ll;
using namespace std;
const int N = 1e5 + 5;

struct node {
    ll son,val; //保存子节点和边权
};
ll dp[N]; //dp[x]=让x的子树中所有子叶无法到达x的最小代价
vector<node> grid[N]; //邻接表，存储所有邻居
int n,m,s; //s是根节点

ll dfs(ll x,ll fa) {
    if (grid[x].size() == 1 && x != s) return inf; //递归出口，这个点不是根s，且是叶子节点 那么return inf
    for (ll i = 0 ; i < grid[x].size() ; ++i) {
        ll tmp = grid[x][i].son;
        if (tmp == fa) continue; //跳过父节点

        ll va = grid[x][i].val;
        //核心转移方程：累加每个子分支的最小代价
        dp[x] += min(va,dfs(tmp,x));
    }
    return dp[x]; //返回当前节点的dp值，给父节点使用
}

void solve() {
    cin >> n >> m >> s;
    for (ll i = 1 ; i <= m ; ++i) {
        ll u,v,w;
        cin >> u >> v >> w;
        grid[u].push_back(node{v,w});
        grid[v].push_back(node{u,w});
    }
    dfs(s,0); //从根s开始递归，父节点设为0（s没有父节点）
    cout << dp[s] << '\n';
}               

int main(){
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);

    int T = 1;
    // cin >> T;
    while (T--) {
        solve();   
    }
    return 0;
}
```