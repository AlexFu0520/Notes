描述
--

**本题分为简单版本和困难版本，二者唯一的区别是：简单版本有序列 $a$ 所有元素乘积 $\leq 10^{18}$ 的限制，困难版本没有。**

氧气少年最近喜欢上了零。

给出一个长度为 $n(1\leq n\leq 2\cdot 10^5)$ 的序列 $a$ $(1\leq a_i\leq 10^9)$，求这个序列中满足如下条件的连续子段 $[a_l\dots a_r]$ 的数量：

-   令 $x=a_l\cdot a_{l+1}\cdot a_{l+2}\dots a_r$，那么 $x$ 的末尾恰好有 $k(0\leq k\leq 10^9)$ 个零。

### 输入描述：

第一行包含一个整数 $T(1\leq T\leq 10^5)$，表示测试用例的组数。  
  
对于每组测试用例：  
  
第一行包含两个整数 $n(1\leq n\leq 2\cdot 10^5)$ 和 $k(0\leq k\leq 10^9)$，表示序列的长度和题目中提到的后导零的数量；  
  
第二行包含 $n$ 个整数 $a_1\dots a_n\ (1\leq a_i\leq 10^9)$，表示该序列。  
  
保证对于所有的测试用例，$n$ 的总和不超过 $2\cdot 10^5$。

### 输出描述：

对于每组测试用例：  
  
仅输出一行，包含一个整数，表示答案。

示例1
---

输入：
```txt
2
5 3
125 1 8 1 1
1 0
6
```

输出：
```txt
3
1
```
## 思路：
尾零只取决于因子2，5的个数，因此我们要计算尾零的个数可以转化为计算两个因子的个数 $$min\left( \sum cnt_{2},\sum cnt_{5} \right)=k$$
问题就转化为以上的式子了，我们可以采用滑动窗口去计算至少有k个零的个数，再根据容斥原理：$$恰好为k个零的个数=至少k个零的数量-至少k+1个零的数量$$
## ACcode:
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
typedef long long ll;

const int N = 2e5  + 5;
ll n,k;
ll cnt2[N],cnt5[N]; //存储第i个位置上因子2，5的个数

ll cal(int k) {
    ll l = 0,ans = 0;
    ll sum2 = 0,sum5 = 0;
    //滑动窗口
    for (ll r = 0 ; r < n ; ++r) {
        sum2 += cnt2[r];
        sum5 += cnt5[r];

        //l会停在第一次不满足有k个尾零的位置
        while (l <= r && sum2 >= k && sum5 >= k) {
            sum2 -= cnt2[l];
            sum5 -= cnt5[l];
            ++l;
        }

        //以r为右端点，[0,l-1]为左端点
        //在这个范围内全都是尾零 >= k的子序列
        ans += l;
    }
    return ans;
}

void solve() {
    cin >> n >> k;
    vector<int> a(n);
    for (int i = 0 ; i < n ; ++i) {
        cin >> a[i];
        cnt2[i] = 0,cnt5[i] = 0;

        //计算第i个位置上因子2和5的个数
        while (a[i] % 2 == 0) {
            cnt2[i]++;
            a[i] /= 2;
        }

        while (a[i] % 5 == 0) {
            cnt5[i]++;
            a[i] /=5;
        }
    }   
    //答案就是尾零 >=k的个数-尾零 >=k+1的个数
    cout << cal(k) - cal(k + 1) << '\n';
}

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);

    int T = 1;
    cin >> T;

    while (T--) {
        solve();
    }
    return 0;
}
```