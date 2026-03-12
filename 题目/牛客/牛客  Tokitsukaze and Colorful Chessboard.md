*Algorithm :* [[二分算法]]
## 描述

Tokitsukaze 要在一个 $n \times n$ 的棋盘上摆放棋子。她有 $a$ 个红色的棋子和 $b$ 个蓝色的棋子，要求任意两个相同颜色的棋子上下左右四个方向不相邻，允许棋盘上留有不放棋子的空格子。  
  
Tokitsukaze 想知道棋盘的边长 $n$ 至少多大，才能摆放下 $a$ 个红色的棋子和 $b$ 个蓝色的棋子。

### 输入描述：

第一行包含一个整数 $T$ ($1 \leq T \leq 10^5$) --- 测试数据的组数。  
  
对于每组测试数据：  
  
第一行包含两个整数 $a$, $b$ ($0 \leq a,b \leq 10^9$, $a+b > 0$) --- 两种颜色的棋子数。

### 输出描述：

对于每组测试数据，输出一行，每行包含一个整数表示答案。

## 示例1

输入：
```txt
2
4 5
0 6
```

输出：
```txt
3
4
```

说明：第一组测试数据，我们只能这样摆：第二组测试数据，我们可以这样摆：

### 思路：
1. 要满足摆放条件，需同时满足两个核心约束：
	- 总容量约束：$n^2 \geq a + b$ (棋盘的总格子数需能放下所有棋子)
	- 单颜色约束：$\left\lceil  \frac{n^2}{2}  \right\rceil \geq max(a,b)$ (棋盘最多能放的同色棋子数为棋盘格子数的一半向上取整，需能放下数量更多的那个颜色)
2. 采用二分算法寻找最小的n
	- 确定二分查找的范围：左边界 $l = 0$，右边界 $r =2e5$ （足够覆盖1e9级别的棋子数）
	- 对每个中间值mid，检查是否满足两个核心约束
	- 若满足，尝试缩小右边界寻找更小的n，若不满足，扩大左边界
3. check函数，翻译两个约束条件
### ACcode：
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
typedef long long ll;

void solve() {
    ll a,b;
    cin >> a >> b;

    ll l = 0,r = 2e5;
    while (l <= r) {
        ll mid = l + (r - l) / 2;
        if (mid * mid >= a + b && ceil(1.0 * mid * mid / 2) >= max(a,b)) {
            r = mid - 1;
        }
        else {
            l = mid + 1;
        }
    }
    cout << l << '\n';
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