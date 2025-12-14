#用于查询区间最大/最小值
# 1.函数写法 （闭区间查询）

```cpp
#include <bits/stdc++.h>
using namespace std;

const int MAXN = 500010;
const int LOGN = 20;

int a[MAXN];
int st_max[LOGN][MAXN];
int st_min[LOGN][MAXN];

void build(int n) {
	for (int i = 0 ; i < n ; ++i) {
		st_max[0][i] = a[i];
		st_min[0][i] = a[i];
	}
	
	for (int i = 1 ; i < LOGN ; ++i) {
		for (int j = 0 ; j + (1 << i) <= n ; ++j) {
			st_min[i][j] = min(st_min[i - 1][j],st_min[i - 1][j + (1 << (i - 1))]);
			st_max[i][j] = max(st_max[i - 1][j],st_max[i - 1][j + (1 << (i - 1))]);
		}
	}
}

int query_min(int l,int r) {
	int k = __lg(r - l + 1);
	return min(st_min[k][l],st_min[k][r - (1 << k) + 1]);	
}

int query_max(int l,int r) {
	int k = __lg(r - l + 1); 
	return max(st_max[k][l],st_max[k][r - (1 << k) + 1]);
}
```

## 函数写法的调用

```cpp
int main() {
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	cout.tie(nullptr);
	
	int n,q;
	cin >> n >> q;
	
	for (int i = 0 ; i < n ; ++i) {
		cin >> a[i];
	}

	build(n);
	
	while (q--) {
		int op,l,r;
		cin >> op >> l >> r;
		l--;
		r--;
		
		if (op == 1) {
			cout << query_min(l,r) << '\n';
		}
		else {
			cout << query_max(l,r) << '\n';
		}
	}
	return 0;
}
```

# 2.类写法

```cpp
#include <bits/stdc++.h>
using namespace std;

class SparseTable {
    vector<vector<int>> st_min;
    vector<vector<int>> st_max;

public:
    // 时间复杂度 O(n * log n)
    SparseTable(const vector<int>& nums) {
        size_t n = nums.size();
        int w = __lg(n) + 1;
        st_min.resize(w, vector<int>(n));
        st_max.resize(w, vector<int>(n));

        for (int j = 0; j < n; j++) {
            st_min[0][j] = nums[j];
            st_max[0][j] = nums[j];
        }

        for (int i = 1; i < w; i++) {
            for (int j = 0; j + (1 << i) <= n; j++) {
                st_min[i][j] = min(st_min[i - 1][j], st_min[i - 1][j + (1 << (i - 1))]);
                st_max[i][j] = max(st_max[i - 1][j], st_max[i - 1][j + (1 << (i - 1))]);
            }
        }
    }

    // [l, r] 闭区间，下标从 0 开始
    // 时间复杂度 O(1)
    int query_min(int l, int r) const {
        int k = __lg(r - l + 1);
        return min(st_min[k][l], st_min[k][r - (1 << k) + 1]);
    }

    // [l, r] 闭区间，下标从 0 开始
    // 时间复杂度 O(1)
    int query_max(int l, int r) const {
        int k = __lg(r - l + 1);
        return max(st_max[k][l], st_max[k][r - (1 << k) + 1]);
    }
};

```

## 类写法的调用

```cpp
int main() {
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	cout.tie(nullptr);
	
	int n,q;
	cin >> n >> q;
	vector<int> nums(n);
	for (int i = 0 ; i < n ; ++i) {
		cin >> nums[i];
	}
	
	SparseTable st(nums);
	while (q--) {
		int op,l,r;
		cin >> op >> l >> r;
		
		//从0开始的，需要减1
		l--;
		r--;
		
		if (op == 1) {
			cout << st.query_min(l,r) << '\n';
		}
		else {
			cout << st.query_max(l,r) << '\n';
		}
	}
	return 0;
}
```