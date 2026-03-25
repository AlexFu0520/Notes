描述
--

*氧气少年*现在有一个长度为 $n$ 的序列 $a$ 和一个空的矩阵，矩阵的行数不限，但列数为 $m$。  
  
每次操作他可以从下面的操作中任选其一：  
  

-    任选序列的某个位置，将此位置的数字修改为任意的数字；
-    将矩阵的列数增加 $1$；
-    将矩阵的列数减小 $1$（如果当前矩阵的列数大于 $1$）。

  
操作完成后，*氧气少年*将序列中的每个元素依次按照从上到下、从左到右的顺序填到矩阵中。（即：先填第 $1$ 行第 $1$ 列，再填第 $1$ 行第 $2$ 列，$\dots$ 填第 $1$ 行第 $m$ 列，填第 $2$ 行第 $1$ 列，填第 $2$ 行第 $2$ 列，$\dots$ 填第 $2$ 行第 $m$ 列，以此类推。）  
  
*氧气少年*想要让矩阵至少一列的所有数字均为目标数字 $k$，请求出他需要做的最少的操作次数。

例如，当 $a=[1,2,3,4,5,1,6,7,8,9,1,2,3,4,5,8,9],m=4,k=1$ 时，如果不执行任何操作，填数后的矩阵如下图所示：

$\def\arraystretch{1.5} \begin{array}{c:c:c:c} \hdashline 1 & 2 & 3 & 4 \\ \hline 5 & 1 & 6 & 7 \\ \hline 8 & 9 & 1 & 2 \\ \hline 3 & 4 & 5 & 8 \\ \hline 9 & \end{array}$  
  
如果在填数之前，先将 $a_{16}$ 改为 $1$，再将矩阵的列数增加为 $5$，那么填数后的矩阵如下图所示：  

$\def\arraystretch{1.5} \begin{array}{c:c:c:c:c} \hdashline 1 & 2 & 3 & 4 & 5 \\ \hline 1 & 6 & 7 & 8 & 9 \\ \hline 1 & 2 & 3 & 4 & 5 \\ \hline 1 & 9 & \end{array}$  

  
注意到此时第一列的所有数字均为目标数字 $1$，符合要求，并且没有比这耗费次数更少的操作方案。因此答案为 $2$。  
  
例如，当 $a=[1,2,3,4,5],m=3,k=3$ 时，如果不执行任何操作，填数后的矩阵如下图所示：  
  
$\def\arraystretch{1.5} \begin{array}{c:c:c} \hdashline 1 & 2 & 3 \\ \hline 4 & 5 \end{array}$  
  
注意到此时第三列的所有数字均为目标数字 $3$，符合要求。因此答案为 $0$。  

### 输入描述：

第一行包含一个整数 $T(1\leq T \leq 10^5)$，表示测试用例的组数。  
  
对于每组测试用例：  
  
第一行包含三个整数 $n(1\leq n\leq 2\cdot 10^5),m(1\leq m\leq 10^9),k(0\leq k\leq 10^9)$，分别表示序列的长度，初始矩阵的列数和目标数字。  
  
第二行包含 $n$ 个整数 $a_1\dots a_n(0\leq a_i\leq 10^9)$，表示该序列。  
  
保证对于所有的测试用例，$n$ 的总和不超过 $2\cdot 10^5$。

### 输出描述：

对于每组测试用例：  
  
仅输出一行，包含一个整数，表示答案。

示例1
---

输入：
```txt
2
17 4 1
1 2 3 4 5 1 6 7 8 9 1 2 3 4 5 8 9
5 3 3
1 2 3 4 5
```

输出：
```txt
2
0
```

说明：样例解释见题目描述。


## ACcode:
```cpp

vector<ll> a(2e5);
ll n,m,k;

ll cost(ll m) {
    ll ans = inf;
    for (ll i = 0 ; i < m ; ++i) {
        ll cnt = 0;
        for (ll j = i ; j < n ; j += m) { //遍历长度为m的这一列
            if (a[j] != k) ++cnt;
        }
        ans = min(ans,cnt); //计算如果列数为m时，最小的更换次数
    }
    return ans;
}

void solve() {
    ll ans = inf;
    cin >> n >> m >> k;
    for (int i = 0 ; i < n ; ++i) cin >> a[i];
	
	//情况1（jia lie）:
    for (ll i = m ; i <= n && i - m < ans ; ++i) {
        ans = min(ans,cost(i) + i - m);
    }

    for (ll i = m - 1 ; i > 0 && m - i < ans ; --i) {
        ans = min(ans,cost(i) + m - i);
    }
    cout << ans << '\n';
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