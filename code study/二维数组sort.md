在 C++ 中排序二维 `vector`（如 `vector<vector<int>>`），可以使用标准库的 `std::sort` 函数，关键是通过自定义排序规则（比较函数或 lambda 表达式）来指定排序逻辑。以下是常见的排序场景和实现方法：
### 1. 按每行的第一个元素排序（默认升序）

最常见的需求是按二维数组每行的第一个元素升序排列。

```cpp
#include <iostream>
#include <vector>
#include <algorithm> // 包含 std::sort

using namespace std;

int main() {
    vector<vector<int>> vec = {{3, 1}, {1, 4}, {2, 5}};

    // 按每行第一个元素升序排序
    sort(vec.begin(), vec.end(), [](const vector<int>& a, const vector<int>& b) {
        return a[0] < b[0]; // 比较每行的第一个元素
    });

    // 输出结果
    for (const auto& row : vec) {
        cout << row[0] << " " << row[1] << endl;
    }
    // 输出：
    // 1 4
    // 2 5
    // 3 1
    return 0;
}
```

### 2. 按每行的某个特定列排序

如果需要按第 `n` 列（如第 2 列）排序，只需修改比较逻辑中的索引即可。

```cpp
// 按每行第二个元素降序排序
sort(vec.begin(), vec.end(), [](const vector<int>& a, const vector<int>& b) {
    return a[1] > b[1]; // 注意：> 表示降序
});
```

### 3. 按每行的元素总和排序

如果需要按每行所有元素的总和排序，可在比较时计算每行的和。

```cpp
// 按每行元素的总和升序排序
sort(vec.begin(), vec.end(), [](const vector<int>& a, const vector<int>& b) {
    int sum_a = 0, sum_b = 0;
    for (int num : a) sum_a += num;
    for (int num : b) sum_b += num;
    return sum_a < sum_b;
});
```

### 4. 按行的长度排序

如果二维数组的行长度不一致，可按行的长度排序。

```cpp
vector<vector<int>> vec = {{1}, {3, 4, 5}, {2, 3}};

// 按行的长度升序排序
sort(vec.begin(), vec.end(), [](const vector<int>& a, const vector<int>& b) {
    return a.size() < b.size();
});

// 输出结果：
// 1 
// 2 3 
// 3 4 5 
```
### 注意事项

- **排序稳定性**：`std::sort` 是不稳定排序（相等元素的相对顺序可能改变），若需要稳定排序，可使用 `std::stable_sort`。
- **空行处理**：如果二维数组中存在空行（`size() == 0`），需在比较函数中避免访问空行的元素（否则会越界），例如：

```cpp
sort(vec.begin(), vec.end(), [](const vector<int>& a, const vector<int>& b) {
    if (a.empty()) return true;  // 空行放前面
    if (b.empty()) return false;
    return a[0] < b[0]; // 非空行按首元素排序
});
```

通过自定义比较函数，可灵活实现二维 `vector` 的各种排序需求，核心是明确 “按什么规则比较两行的大小”。