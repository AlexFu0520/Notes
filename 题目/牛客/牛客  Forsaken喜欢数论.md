*Algorithm:* [[数学算法]]，[[线性筛（Euler筛）]]
描述
--

Forsaken有一个有趣的数论函数。对于任意一个数$x$，$f(x)$会返回$x$的最小质因子。如果这个数没有最小质因子，那么就返回0。

现在给定任意一个$n$，Forsaken想知道$\sum_{i = 1}^{n}{f(i)}$的值。  

### 输入描述：

一个整数$n$。

### 输出描述：

一个整数代表上面的求和式的值。

示例1
---

输入：
```txt
4
```

输出：
```txt
7
```

备注：
---

$1 \leq n \leq 3e7$

## 思路：
1. 利用质数筛求最小质因子
2. 利用欧拉筛对每个质因子的倍数进行更新
3. 将每个数找到最小的质因子
4. 最后求和即可
## ACcode：
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
typedef long long ll;

const int N = 3e7 + 10;
int prime[N];
int minifactor[N];
int primepos;

//欧拉筛的核心思想是：让每个质数只被它的最小质因数筛一次，从而达到 O(n)的复杂度
void init() {
    int tmp;
    //从2开始遍历每个数（1没有质因数）
    for (int i = 2 ; i < N ; ++i) {
        //如果最小质因子未被赋值
        //说明i没有杯更小的质数筛过，因此i本身就是质数
        if (!minifactor[i]) {
            prime[primepos++] = i;
            minifactor[i] = i;
        }

        //用当前的i去筛掉更大的质数
        //遍历已经找到的质数 prime[j] 
        for (int j = 0 ; (tmp = i * prime[j]) < N ; ++j) {
            
            //核心：tmp 的最小质因子就是prime[j]
            //因为 prime[j]是从小到大遍历的，并且是第一个能整除tmp的质数
            minifactor[tmp] = prime[j];

            //欧拉筛的灵魂：如果i能被prime[j]整除，就停止内层循环
            if (!(i % prime[j])) break;
        }
    }
}

void solve() {
    int n;
    ll sum = 0;
    cin >> n;
    for (int i = 1 ; i <= n ; ++i) {
        sum += minifactor[i];
    }
    cout << sum << '\n';
}
int main(){
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);
	
	init();
    int T = 1;
    // cin >> T;
    while (T--) {
        solve();   
    }
    return 0;
}

```