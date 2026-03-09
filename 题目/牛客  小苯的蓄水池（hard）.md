## 描述

小苯有一个大的蓄水场，其中有恰好 $n$ 个蓄水池排成一排，编号分别为 $1$ 到 $n$，其中第 $i$ 个蓄水池中的水量为 $a_{i}$。  
  
起初每两个相邻蓄水池之间都有一个隔板，而现在小苯希望在蓄水场维护一些操作和查询，具体的：  
- 1  $l~~~r$ 表示将第 $l$ 个水池和第 $r$ 个水池之间所有的挡板拿走。（保证：$1\leq l<r\leq n$）  
- 2  $i$ 表示查询第 $i$ 个水池目前的水量。  

（如果挡板被拿走，则原本挡板两侧水会混合在一起，最终对应的水量会变为平均值，具体可以看样例解释。）

请你帮他回答每次查询吧。
（注意：因本题输入输出数据量较大，请选手选择快速的输入输出方式。）
### 输入描述：

输入包含 $m+2$ 行。  
第一行输入两个正整数 $1\leq n,m\leq 2\times 10^5$，分别表示蓄水场中水池的个数，以及操作的次数。  
第二行 $n$ 个整数 $ai (1\leq a_{i}\leq 10^9)$，表示每个水池初始时的水量。  
接下来 $m$ 行，每行第一个整数  $op(1\leq op\leq 2)$ 表示操作。  

-  如果 $op=1$，则表示取掉挡板操作，再输入两个正整数 $l,r (1\leq l<r\leq n)$。

- 如果 $op=2$，则表示查询操作，再输入一个正整数 $i (1\leq i\leq n)$。

（注意：已经拿走的挡板在以后的操作中都是“被拿走”的状态。）

（数据保证至少有一次查询操作。）

### 输出描述：

输出包含若干行，每行一个实数，表示对每个 $op=2$ 的询问做出的回答。

（与正确答案的绝对误差不超过 $10^{-4}$ 则视为正确。）

## 示例1

输入：
```
4 6
1 2 4 5
1 1 3
2 1
2 3
1 1 4
2 1
2 4
```

输出：
```
2.3333333333
2.3333333333
3.0000000000
3.0000000000
```


说明：

打开 1 到 3 号之间所有的挡板，则 1 2 3 号水池中的水会混合，水量为他们的平均值：7/3=2.333...。

再打开 1 到 4 号之间所有的挡板，则 1 2 3 4 号水池中的水都会混合，水量为他们的平均值：12/4 = 3。
### set思路：
1. 首先可以把水池分为 $n$ 坨
2. 操作 1 就是把所有与 \[ l , r ] 有交集的坨坨拿来进行合并，合并结果为总水量 / 总池子数
3. 考虑用 set 维护
### code:
```cpp
#include <iostream>
#include <vector>
#include <set>
#include <algorithm>
#include <iomanip>   //包含setprecision的头文件
typedef long long ll;
using namespace std;

struct node {
    int l,r; //连通块的左右端点
    double w; //连通块的总水量
    bool operator < (const node & t) const {
        return l < t.l; //重载运算符，使set有序
    }
};

void solve() {
    int n,m;
    cin >> n >> m;

    set<node> st;
    for (int i = 1 ; i <= n ; ++i) { //1_based
        double a;
        cin >> a;
        st.insert({i,i,a}); 
    }

    while (m--) {
        int op;
        cin >> op;

        if (op == 1) {
            int l,r;
            cin >> l >> r;

            //定位第一个与[l,r]重叠的连通块
            auto it = st.lower_bound({l,0,0}); //找左端点 >= l的第一个块
            if (it != st.begin()) {
                auto prev_it = prev(it);
                if (prev_it -> r >= l) { //如果前一个块的右端点 >= l，说明重叠
                    it = prev_it; //定位到真正的起始块
                }
            }

            double total = 0; //存储新连通块的总水量
            int left = -1,right = -1; //新连通块的左右端点
            
            //遍历并删除所有与[l,r]重叠的块
            while (it != st.end() && it -> l <= r) {
                if (left == -1) {
                    left = it -> l; //新块的左端点=起始块的左端点
                }
                right = max(right,it -> r); //新块的右端点=所有合并块的最大右端点
                total += it -> w;

                it = st.erase(it); //删除当前块，返回下一个块的迭代器
            }
            
            //插入合并后的新块
            if (left != -1) {
                st.insert({left,right,total});
            }
        }
        else {
            int pos;
            cin >> pos;

            //找到包含pos的连通块
            auto it = st.upper_bound({pos,0,0}); //找左端点>pos的块
            it = prev(it); //回退到左端点<=pos的块 (这块就是包含pos的连通块)

            double ans = (double) it -> w / (it -> r - it -> l + 1);
            cout << fixed << setprecision(10) << ans << '\n';
        }
    }
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