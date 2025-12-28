### 一、MEX 的核心定义

MEX 是 **Minimum Excluded Value** 的缩写，中文通常译作「最小未出现值」。它的严格定义是：对于一个由**非负整数**组成的集合 / 数组，MEX 是**最小的、未在该集合 / 数组中出现过的非负整数**。

关键点强调：

- 只关注**非负整数**（0,1,2,3,...），不考虑负数；
- 核心是「最小」+「未出现」，优先从 0 开始依次检查。

### 二、直观示例

通过几个例子能快速理解 MEX 的计算逻辑：

1. 集合 `{0,1,2,3}` → MEX = 4（0-3 都出现了，最小未出现的是 4）
2. 集合 `{1,2,3}` → MEX = 0（0 是第一个没出现的非负整数）
3. 集合 `{0,2,4,5}` → MEX = 1（0 出现了，1 没出现，优先级最高）
4. 空集 `{}` → MEX = 0（没有任何数，最小的非负整数 0 就是答案）
5. 数组 `[0,0,1,1]` → MEX = 2（重复元素不影响，只要出现过就算）

### 三、Python 代码实现 MEX 计算

下面提供一个简洁且符合最佳实践的 MEX 计算函数，新手可以直接复制使用：

```python
def calculate_mex(nums):
    """
    计算非负整数数组的MEX（最小未出现值）
    :param nums: 输入的非负整数数组
    :return: 该数组的MEX值
    """
    # 先将数组转为集合，方便快速查找（时间复杂度O(1)）
    num_set = set(nums)
    # 从0开始依次检查，找到第一个不在集合中的数
    mex = 0
    while mex in num_set:
        mex += 1
    return mex

# 测试用例
if __name__ == "__main__":
    test_cases = [
        [0,1,2,3],    # 预期4
        [1,2,3],      # 预期0
        [0,2,4,5],    # 预期1
        [],           # 预期0
        [0,0,1,1]     # 预期2
    ]
    for case in test_cases:
        print(f"数组 {case} 的MEX = {calculate_mex(case)}")
```

**代码解释**：

1. 将数组转为集合：避免重复检查相同元素，提升查找效率（集合的`in`操作比列表快得多）；
2. 从 0 开始遍历：严格遵循 MEX「从最小非负整数开始找」的规则；
3. 循环终止条件：找到第一个不在集合中的数，即为 MEX。

### 四、MEX 的常见应用场景

MEX 不是单纯的理论概念，在算法题中非常实用，常见场景包括：

1. **博弈论**：比如 Nim 游戏的变种（如取石子游戏），MEX 是判断胜负的核心指标；
2. **数组 / 集合类算法题**：很多竞赛题会要求计算子数组的 MEX、修改数组后求 MEX 等；
3. **图论**：部分图的染色问题、路径问题会用到 MEX 的思想；
4. **数据结构设计**：比如设计一个支持动态增删元素、并能快速查询 MEX 的结构。

### 总结

1. MEX 的核心是「非负整数集合中最小的未出现值」，必须从 0 开始依次检查；
2. 计算 MEX 的关键是快速判断元素是否存在（用集合优化效率）；
3. MEX 是算法竞赛中的高频考点，尤其在博弈论和数组类题目中应用广泛。

### 一、C++ 实现 MEX 的核心思路

和 Python 思路一致，但结合 C++ 特性做了优化：

1. 使用 `unordered_set`（哈希集合）存储数组元素，实现 **O(1)** 时间复杂度的查找（比 `set` 更快，也远优于直接遍历数组查找）；
2. 从 0 开始依次检查，找到第一个不在集合中的非负整数，即为 MEX；
3. 兼容空数组、重复元素、无 0 等所有边界场景。

### 二、完整 C++ 代码实现

```cpp
#include <iostream>
#include <vector>
#include <unordered_set>

/**
 * 计算非负整数数组的 MEX（最小未出现值）
 * @param nums 输入的非负整数数组
 * @return 该数组的 MEX 值
 */
int calculateMEX(const std::vector<int>& nums) {
    // 用哈希集合存储数组元素，快速判断元素是否存在
    std::unordered_set<int> numSet;
    for (int num : nums) {
        // 仅存储非负整数（MEX 只关注非负整数，过滤负数不影响结果）
        if (num >= 0) {
            numSet.insert(num);
        }
    }

    // 从 0 开始依次检查，找到第一个不在集合中的数
    int mex = 0;
    while (numSet.find(mex) != numSet.end()) {
        mex++;
    }
    return mex;
}

// 测试用例
int main() {
    // 覆盖典型场景：全连续、无0、缺中间数、空数组、重复元素
    std::vector<std::vector<int>> testCases = {
        {0, 1, 2, 3},   // 预期 4
        {1, 2, 3},      // 预期 0
        {0, 2, 4, 5},   // 预期 1
        {},             // 预期 0
        {0, 0, 1, 1}    // 预期 2
    };

    for (const auto& caseVec : testCases) {
        // 打印数组（方便查看）
        std::cout << "数组 [";
        for (size_t i = 0; i < caseVec.size(); ++i) {
            std::cout << caseVec[i];
            if (i != caseVec.size() - 1) {
                std::cout << ", ";
            }
        }
        std::cout << "] 的 MEX = " << calculateMEX(caseVec) << std::endl;
    }

    return 0;
}
```

### 三、代码关键部分解释

1. **容器选择**：
    
    - 使用 `std::unordered_set` 而非 `std::set`：前者基于哈希表，查找 / 插入的平均时间复杂度是 O (1)；后者基于红黑树，时间复杂度是 O (logn)，对于大数据量，`unordered_set` 效率更高。
    - 过滤负数：MEX 只关注非负整数，即使输入数组含负数，也不影响结果（比如数组 `{-1, -2, 1}` 的 MEX 仍是 0）。
2. **核心循环**：
    
    - 从 `mex = 0` 开始，只要 `mex` 存在于集合中，就自增；
    - 当 `mex` 不在集合中时，循环终止，此时的 `mex` 就是最小未出现值。
3. **测试用例**：
    
    - 覆盖了所有典型场景，运行代码后会输出：
        
        ```plaintext
        数组 [0, 1, 2, 3] 的 MEX = 4
        数组 [1, 2, 3] 的 MEX = 0
        数组 [0, 2, 4, 5] 的 MEX = 1
        数组 [] 的 MEX = 0
        数组 [0, 0, 1, 1] 的 MEX = 2
        ```
    
### 四、进阶优化（可选）

如果输入数组的元素范围已知（比如所有元素都 ≤ n），可以用 `bool` 数组替代 `unordered_set`，进一步降低开销：

```cpp
int calculateMEX_Optimized(const std::vector<int>& nums) {
    // 假设元素最大值不超过数组长度（算法题常见约束）
    int n = nums.size();
    std::vector<bool> exists(n + 1, false); // 大小 n+1 足够覆盖 MEX 可能值

    for (int num : nums) {
        if (num >= 0 && num <= n) { // 只标记有效范围的数
            exists[num] = true;
        }
    }

    int mex = 0;
    while (mex <= n && exists[mex]) {
        mex++;
    }
    return mex;
}
```

### 总结

1. C++ 中实现 MEX 的核心是**快速查找元素是否存在**，优先用 `unordered_set`（通用场景）或 `bool` 数组（元素范围已知时）；
2. 逻辑上必须从 `0` 开始依次检查，直到找到第一个未出现的非负整数；
3. 代码需兼容空数组、重复元素、负数等边界场景，保证通用性。