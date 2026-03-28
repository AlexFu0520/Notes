```cpp
const int N = 1e6 + 1;
vector<int> pri;
bool not_prime[N];

void pre(int n) {
	for (int i = 2; i <= n; ++i) {
		if (!not_prime[i]) {
			pri.push_back(i);
		}
		for (int pri_j : pri) {
			if (i * pri_j > n) break;
			not_prime[i * pri_j] = true;
			if (i % pri_j == 0) {
				// i % pri_j == 0
				// 换言之，i 之前被 pri_j 筛过了
				// 由于 pri 里面质数是从小到大的，所以 i 乘上其他的质数的结果一定会被
				// pri_j 的倍数筛掉，就不需要在这里先筛一次，所以这里直接 break
				// 掉就好了
				break;
			}
		}
	}
}
```

## 第二种写法：
```cpp
const int N = 1e5 + 5;
vector<int> prime;
bool not_prime[N];

void init() {
    for (int i = 2 ; i < N ; ++i) {
        if (!not_prime[i]) {
            prime.push_back(i);
        }
        for (int j = 0 ; i * prime[j] < N ; ++j) {
            int tmp = i * prime[j];
            not_prime[i * prime[j]] = true;
            if (i % prime[j] == 0) {
                break;
            }
        }
    }
}
```

## 问：内层循环中的j会超过prime的长度吗？
### 答：**不会的，j 永远不会超过 `prime` 的长度。**

这是欧拉筛代码设计中非常精妙的一个地方，虽然没有显式写 `j < prime.size()`，但逻辑上保证了绝对不会数组越界。我们可以通过**分情况讨论**来证明这一点：

### 证明：j 不会越界的两个逻辑保障

我们需要观察 `prime` 数组和 `i` 的关系，以及 `break` 触发的时机。
#### 情况 1：当 `i` 是合数时
如果 `i` 是一个合数，那么它一定有一个**最小质因数**，假设为 `p`。
- 因为 `p` 是 `i` 的最小质因数，所以 `p` 一定小于 `i`，并且在之前的循环中已经被筛出来了，**必然存在于 `prime` 数组中**。
- 当内层循环的 `j` 遍历到 `prime[j] == p` 时：
    1. 先执行标记：`not_prime[i * p] = true;`
    2. 然后判断：`if (i % p == 0)`，这显然成立。
    3. **立即触发 `break`**。
**结论：** 当 `i` 是合数时，`j` 会在遇到 `i` 的最小质因数时停下，此时 `j` 肯定在 `prime` 数组范围内。
#### 合数的定义：
##### 核心定义

合数是指**大于 1 的自然数**中，除了能被 1 和它自身整除外，还能被其他非 0 自然数整除的数。

与之相对的是质数（素数）：大于 1 的自然数中，只能被 1 和它自身整除的数。

**特别规定：1 既不是质数，也不是合数**。

---

#### 情况 2：当 `i` 是质数时

如果 `i` 是一个质数，流程是这样的：

1. **先执行** `if (!not_prime[i]) { prime.push_back(i); }`。
    
    - 此时，`i` 被刚刚加入到 `prime` 数组的末尾。
    - `prime` 数组的最后一个元素就是 `i` 本身。
    
2. **后进入** 内层 `for` 循环。

现在看内层循环的过程：

`j` 从 0 开始递增，`prime[j]` 依次是之前发现的质数。

直到 `j` 增加到 `prime.size() - 1` 时（也就是刚刚加入 `i` 的那个位置）：

- 此时 `prime[j]` 等于 `i`。
- 先执行标记：`not_prime[i * i] = true;`
- 然后判断：`if (i % i == 0)`，这成立。
- **立即触发 `break`**。

**结论：** 当 `i` 是质数时，`j` 最多走到 `prime` 数组的最后一位（即 `i` 自己），然后就会 break，绝不会多走一步。

---
### 总结
内层循环有一个**强制停止机制**：

无论 `i` 是质数还是合数，`j` 都会在 `prime` 数组范围内遇到一个能整除 `i` 的质数（要么是 `i` 的最小质因数，要么是 `i` 本身），从而触发 `break`。

因此，**完全不用担心数组越界**，这也是为什么标准写法通常只写 `i * prime[j] < N` 而不用写 `j < prime.size()` 的原因。