描述
--

小苯平时很喜欢使用”颜文字“："$\rm ovo$" 来表达惊讶。  
  
这天他在键盘上输入了若干 $\rm o$, $\rm v$ 和 $\rm ?$ 字符，字符们组成了一个字符串 $S$。  
  
他想将 $S$ 中所有的 $\rm ?$ 都改成 $\rm o$ 或者 $\rm v$ 中的一种，以最大化 $\rm ovo$ 子序列（不一定连续）的个数，请你帮他算一算，$\rm ovo$ 子序列的个数最多可以达到多少吧。

### 输入描述：

本题每个测试文件中均包含多组测试数据。  
输入的第一行包含一个正整数 $T\ (1 \leq T \leq 100)$，表示数据组数。  
接下来包含 $T$ 组数据，每组数据的格式如下：  
第一行一个字符串 $S\ (1 \leq |S| \leq 500)$，表示题目所述的字符串。  
（保证 $S$ 仅由 '$\rm o$', '$\rm v$', '$\rm ?$' 三种字符构成。）  
（保证同一个测试文件中测试数据中 $|S|$ 的总和不超过 $500$。）

### 输出描述：

对于每组测试数据：  
输出一行一个整数，表示 "$\rm ovo$" 子序列的最多个数。

示例1
---

输入：
```txt
2
ov??ovoov
?????
```

输出：
```txt
16
4
```

说明：对于第一组测试数据，修改为：$\rm ovovovoov$ 即可。

对于第二组测试数据，修改为：$\rm oovoo$ 即可。
## 思路：
1. 对于一个确定的字符串，其 `ovo` 子序列的总数可以通过遍历所有字符 `v` 并计算其左右两侧 `o` 的数量乘积来获得
2. 设最终字符串中 `o` 的总个数为 $N_{0}$，在位置i处的字符为`v` 且在左侧有 $pre\_o[i]$ 个 `o` ，则该`v` 对答案的贡献为$$Contribution(v_{i})=pre\_o[i] \times (N_{0}-pre\_o[i])$$总数量为：$$\sum_{i:S[i]=v}pre\_o[i]\times (N_{0}-pre\_{o}[i])$$
3. 算法：动态规划
由于每个 ？ 的决策会影响去全局的 $N_{0}$ ，以及后续位置的$pre\_o$，且决策之间存在依赖关系，此问题具有明显的最优子结构和重叠子问题性质

**难点分析：** 贡献函数 $f(x) = x(N_{0}-x)$ 依赖全局变量 $N_{0}$ ，这意味着在进行局部决策时，如果不知道最终字符串中有多少个 `o`，我们就无法准确计算当前位置填入 `v`的贡献

**对策：** 通过枚举全局状态（枚举法+dp）。既然 $|S| \leq 500$ ，我们可以枚举最终字符串中的 `o`的总数 $M$ （$M$的范围是从原有的 `o`的数量到原有`o`加 `?` 的总数），一旦 $M$ 固定，每个位置的贡献计算式就变得确定了

**第一阶段：** 枚举与初始化
1. 统计字符串中已有 `o`的数量 $count\_o$ 和 `?`的数量 $count\_?$
2. 初始化全局最大值 $ans = 0$
3. 遍历所有可能的 `o`的总数 $M$ ，其中$M \in[count\_{o},count\_{o} +count\_{?}]$
**第二阶段：** 状态定义与转移（针对固定的 $M$ ）
对于每一个确定的 $M$，执行一次线性dp
- 状态定义：$dp[j]$ 表示当前已处理的字符中，包含 $j$ 个 `o`时能获得的最大 `ovo`子序列的数量
- 状态转移：
	- 若当前子都 $S[i]= 'o'$，则必须作为 `o`处理，当前 `o` 数量增加, $dp[j]=dp[j -1]$ （需逆向遍历 $j$ 以防止重复使用同一位置）
	- 若当前字符 $S[i]='v'$，则该位置产生贡献 $j\times (M-j)$,状态转移为： $dp[j]=dp[j] +j \times(M-j)$
	- 若当前字符 $S{i}='?'$,则面临分支决策：
		a.填入 `o`：对应状态 $dp[j-1]$
		b.填入 `v`：对应状态 $dp[j]+ j\times(M-j)$取两者最大值：$$dp[j]=max(dp[j-1],dp[j]+j\times(M-j))$$
**第三阶段：** 结果汇总
每次枚举完 $M$，更新 $ans=max(ans,dp[M])$

## ACcode:
```cpp
#include <iostream>
#include <vector>
#include <set>
#include <algorithm>
typedef long long ll;
using namespace std;

void solve() {
    string s;
    cin >> s;
    int n = s.length();
    int co = 0 ,cx = 0; //o的数量和 ？的数量
    for (char c : s) {
        if (c == 'o') {
            co++;
        }
        else if (c == '?') {
            cx++;
        }
    }

    ll ans = 0;
    for (int M = co ; M <= co + cx ; ++M) { //遍历M
        vector<ll> dp(M + 1,-1); 
        dp[0] = 0;

        for (char c : s) {
            if  (c == 'o') {
                //逆序遍历j 防止重复使用同一个位置
                for (int j = M ; j >= 0 ; --j) {
                    if (j > 0 && dp[j - 1] != -1) {
                        dp[j] = dp[j - 1];
                    }
                }
            }
            else if (c == 'v') {
                for (int j = M ; j >= 0 ; --j) {
                    if (dp[j] != -1) {
                        dp[j] += 1ll * j * (M-  j); //该位置产生贡献
                    }
                }
            }
            else if (c == '?') {
                for (int j = M ; j >= 0 ; --j) {
                    //存储分别填入v和o的对应状态
                    ll res_v = -1,res_o = -1;
                    
                    //该位置使用过
                    if (dp[j] != -1) {
                        res_v = dp[j] + 1ll * j * (M - j);
                    }
                    if  (j > 0 && dp[j - 1] != -1) {
                        res_o = dp[j - 1];
                    }

                    dp[j] = max(res_v,res_o);
                }
            }
            if (dp[M] != -1) {
                ans = max(ans,dp[M]);
            }
        }
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

## 第二种写法Code:
```cpp
#include <iostream>
#include <vector>
#include <set>
#include <algorithm>
typedef long long ll;
using namespace std;


int count(const string & s) {
    int a = 0,b = 0;
    int ans = 0;
    for (char c : s) {
        if (c == 'o') {
            ans += b;
            a++;
        }
        else {
            b += a;
        }
    }
    return ans;
}
void solve() {
    string s;
    cin >> s;
    int n = s.length();
    
    //存储所有?的位置
    vector<int> pos; 
    for (int i = 0 ; i < n ; ++i) {
        if (s[i] == '?') {
            pos.push_back(i);
            s[i] = 'o'; //先全部换为o
        }
    }

    int ans = count(s); //计算初始方案全o的ovo数

    //从第i个 ? 开始换
    for (int i = 0 ; i < pos.size() ; ++i) {
        //把第 i-j 个？换为v
        for (int j = i ; j < pos.size() ; ++j) {
            s[pos[j]] = 'v';
            ans = max(ans,count(s)); //更新最大值
        }
        //重置：把 i-j 的？换回 o,恢复初始状态
        for (int j = i ; j < pos.size() ; ++j) {
            s[pos[j]] = 'o';
        }
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