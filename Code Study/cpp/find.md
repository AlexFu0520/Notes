在 C++ 中，`find` 是一个常用的算法，用于在容器或序列中查找指定元素的位置。它可以通过两种方式使用：**容器的成员函数**（如关联容器）或**标准库算法**（如 `<algorithm>` 中的 `std::find`）。

### **1. 标准库算法 `std::find`**

`std::find` 定义在 `<algorithm>` 头文件中，适用于**所有序列容器**（如 `std::vector`、`std::list`、`std::array` 等），也可用于原生数组。

#### 功能

在指定范围内查找第一个与目标值相等的元素，返回其迭代器；若未找到，返回范围的**尾迭代器**（如 `end()`）
```cpp
#include <iostream> 
#include <vector> 
#include <algorithm> 
// 引入 std::find 
int main() { 
	std::vector<int> vec = {10, 20, 30, 40, 50}; 
	// 查找元素 30 auto it = std::find(vec.begin(), vec.end(), 30); 
	if (it != vec.end()) { 
	// 找到：输出位置和值（迭代器解引用） 
		std::cout << "找到元素，位置：" << (it - vec.begin()) << "，值：" << *it << std::endl; 
		// 位置：2，值：30 
	} 
	else { 
		std::cout << "未找到元素" << std::endl; 
	} 
	return 0; 
}
```
#### 注意

- 时间复杂度为 **O(n)**（线性查找），适用于小规模数据或无序序列。
- 支持原生数组：
```cpp
    int arr[] = {5, 3, 8, 1};
    int* it = std::find(std::begin(arr), std::end(arr), 8);  // 指向 arr[2]
```
### **2. 关联容器的 `find` 成员函数**

对于**关联容器**（如 `std::set`、`std::map`、`std::unordered_set`、`std::unordered_map` 等），`find` 是其**成员函数**，效率更高。
#### 功能

- 对于 `std::set`/`std::unordered_set`：查找元素是否存在，返回指向该元素的迭代器。
- 对于 `std::map`/`std::unordered_map`：查找**键（key）** 是否存在，返回指向键值对（`std::pair`）的迭代器。
#### 示例

```cpp
#include <iostream>
#include <set>
#include <map>

int main() {
    // 1. std::set
    std::set<std::string> fruits = {"apple", "banana", "orange"};
    auto it_set = fruits.find("banana");
    if (it_set != fruits.end()) {
        std::cout << "set 中找到：" << *it_set << std::endl;  // banana
    }
    
    // 2. std::map（查找键）
    std::map<int, std::string> id_name = {{1, "Alice"}, {2, "Bob"}};
    auto it_map = id_name.find(2);
    if (it_map != id_name.end()) {
        // 键值对的 first 是键，second 是值
        std::cout << "map 中找到：id=" << it_map->first 
                  << ", name=" << it_map->second << std::endl;  // id=2, name=Bob
    }
    
    return 0;
}
```
#### 优势

- 有序关联容器（`set`/`map`）：时间复杂度 **O(log n)**（基于红黑树）。
- 无序关联容器（`unordered_set`/`unordered_map`）：平均时间复杂度 **O(1)**（基于哈希表）。
### **3. 字符串的 `find` 成员函数**

`std::string` 和 `std::string_view` 也提供 `find` 成员函数，用于查找子串或字符。
#### 功能
查找子串 / 字符在字符串中**首次出现的位置**，返回索引；若未找到，返回 `std::string::npos`（一个特殊值，通常为 `-1` 的无符号等价物）。
#### 示例

```cpp
#include <iostream>
#include <string>

int main() {
    std::string s = "hello world";
    
    // 1. 查找字符 'o'
    size_t pos1 = s.find('o');
    if (pos1 != std::string::npos) {
        std::cout << "字符 'o' 在位置：" << pos1 << std::endl;  // 4
    }
    
    // 2. 查找子串 "world"
    size_t pos2 = s.find("world");
    if (pos2 != std::string::npos) {
        std::cout << "子串 'world' 在位置：" << pos2 << std::endl;  // 6
    }
    
    // 3. 从指定位置开始查找（从索引 5 开始找 'o'）
    size_t pos3 = s.find('o', 5);
    std::cout << "从位置 5 开始找 'o'：" << pos3 << std::endl;  // 7
    
    return 0;
}
```
### **4. 总结**

| 场景             | 用法                           | 时间复杂度                     | 头文件 / 容器要求        |
| -------------- | ---------------------------- | ------------------------- | ----------------- |
| 序列容器（vector 等） | `std::find(begin, end, val)` | O(n)                      | 需包含 `<algorithm>` |
| 关联容器（set/map）  | `container.find(val)`        | O (log n) 或 O (1)         | 容器自带成员函数          |
| 字符串（string）    | `str.find(substr/char)`      | O (n*m)（n 为字符串长度，m 为子串长度） | 字符串自带成员函数         |


使用时需根据容器类型选择合适的 `find` 方式，关联容器的成员函数 `find` 通常效率更高，而 `std::find` 通用性更强。