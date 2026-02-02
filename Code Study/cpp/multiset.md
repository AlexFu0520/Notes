### 一、multiset 基础定义与核心特性

`multiset`是 C++ STL 中的**关联式容器**，底层和`set`一样基于**红黑树**实现，核心定位是**有序、可重复存储的集合**—— 这也是它和`set`的唯一核心区别（`set`要求元素唯一，`multiset`允许重复）。

它的核心特性总结：

1. **有序性**：元素会自动按**默认升序**排列（可自定义排序规则，比如降序），插入时无需手动排序；
2. **可重复性**：支持插入多个相同值的元素，无唯一性限制；
3. **底层红黑树**：插入、删除、查找操作的时间复杂度均为**O(logn)**，效率远高于普通序列容器（如 vector）；
4. **元素不可直接修改**：红黑树的有序性由元素值维护，直接修改元素值会破坏容器结构，若要修改需**先删除旧值，再插入新值**；
5. **双向迭代器**：支持`++`/`--`遍历，不支持随机访问（如`[]`下标、`at()`）；
6. **无下标访问**：因为是关联容器，并非按索引存储，所以没有`operator[]`和`at()`成员函数。

### 二、multiset 头文件与命名空间

使用`multiset`必须包含专属头文件，且它属于`std`命名空间，两种使用方式：

```cpp
// 1. 包含头文件（必须）
#include <set>
// 2. 命名空间：二选一
using namespace std; // 直接使用，适合新手
// 或 每次加std::，如std::multiset<int> ms;
```

### 三、multiset 常用构造与初始化

默认支持**空构造、初始值构造、拷贝构造、自定义排序构造**，以最常用的`int`类型为例，示例简洁易懂：

```cpp
#include <set>
#include <iostream>
using namespace std;

int main() {
    // 1. 空构造：默认升序
    multiset<int> ms1;

    // 2. 初始值构造：插入多个元素，自动升序
    multiset<int> ms2{3, 1, 4, 1, 5, 1, 5}; // 最终存储：1,1,1,3,4,5,5

    // 3. 拷贝构造：复制另一个multiset
    multiset<int> ms3(ms2); // ms3和ms2内容完全一致

    // 4. 自定义排序：降序排列（使用greater<int>，需包含头文件<functional>，部分编译器可省略）
    multiset<int, greater<int>> ms4{3, 1, 4, 1, 5}; // 最终存储：5,4,3,1,1

    return 0;
}
```

**自定义排序拓展**：若存储自定义类型（如结构体），需重载`<`运算符或自定义比较器，这个后续实际使用时再深入，基础阶段先掌握内置类型（int/string 等）即可。

### 四、multiset 核心成员函数（重点）

`multiset`的成员函数和`set`高度重合，仅针对**重复元素**新增了专属的实用用法，以下按**增、删、查、容量、遍历辅助**分类，列出最常用的函数，附使用示例，贴合实际开发需求。

#### （一）增：插入元素 `insert()`

支持**插入单个元素、插入多个元素、插入区间**，插入后自动维持有序，重复元素会追加存储，无报错：

```cpp
multiset<int> ms;
// 1. 插入单个元素
ms.insert(1);
ms.insert(3);
ms.insert(1); // 重复插入，允许
// 此时ms：1,1,3

// 2. 一次性插入多个元素（C++11及以上）
ms.insert({2, 3, 5});
// 此时ms：1,1,2,3,3,5

// 3. 插入另一个容器的区间（迭代器）
vector<int> v{4, 2};
ms.insert(v.begin(), v.end());
// 此时ms：1,1,2,2,3,3,4,5
```

#### （二）删：删除元素 `erase()`

**重点注意**：`erase()`有 3 种重载形式，**删除值时会删除所有匹配的元素**（这是和`set`的关键区别，set 中值唯一，删一次即可），是新手最容易踩坑的点！

```cpp
multiset<int> ms{1,1,2,3,3,5};

// 形式1：按迭代器删除（删除单个元素，精准）
auto it = ms.find(3); // 找到第一个3的迭代器
if (it != ms.end()) {
    ms.erase(it); // 仅删除第一个3，ms变为{1,1,2,3,5}
}

// 形式2：按值删除（删除所有匹配的元素，返回删除的个数）
int delNum = ms.erase(1); // 删除所有1，返回2（共删了2个）
// 此时ms：{2,3,5}

// 形式3：按迭代器区间删除（删除[first, last)的所有元素）
ms.erase(ms.begin(), ms.find(5)); // 删除从开头到第一个5之前的元素，ms变为{5}

// 清空所有元素（两种方式）
ms.clear(); // 推荐
// ms.erase(ms.begin(), ms.end());
```

#### （三）查：查找元素（multiset 专属重点）

因为允许重复元素，所以除了`find()`（找第一个匹配元素），还提供了**统计个数、查找重复元素区间**的专属函数，这是最实用的部分：

```cpp
multiset<int> ms{1,1,2,3,3,3,5};

// 1. find(val)：找到第一个值为val的迭代器，找不到返回end()
auto it = ms.find(3);
if (it != ms.end()) {
    cout << "找到第一个3：" << *it << endl; // 输出3
}

// 2. count(val)：统计val在容器中的出现次数（multiset特有，set中count仅0/1）
int cnt1 = ms.count(1); // 2
int cnt3 = ms.count(3); // 3
int cnt4 = ms.count(4); // 0（不存在）
cout << "1的个数：" << cnt1 << " 3的个数：" << cnt3 << endl;

// 3. lower_bound(val)：找到第一个>=val的迭代器
auto it_low = ms.lower_bound(3); // 指向第一个3
// 4. upper_bound(val)：找到第一个>val的迭代器
auto it_up = ms.upper_bound(3); // 指向5
// 【实用】遍历[lower_bound, upper_bound)，即可获取所有val的重复元素
cout << "所有3的元素：";
for (auto i = it_low; i != it_up; ++i) {
    cout << *i << " "; // 输出3 3 3
}
cout << endl;

// 5. equal_range(val)：一次性返回lower_bound和upper_bound，返回值是pair迭代器
auto pair_it = ms.equal_range(3);
cout << "equal_range遍历所有3：";
for (auto i = pair_it.first; i != pair_it.second; ++i) {
    cout << *i << " "; // 输出3 3 3
}
cout << endl;
```

**核心技巧**：遍历所有重复元素时，用`lower_bound/upper_bound`或`equal_range`是最高效的方式，比遍历整个容器判断值快得多。

#### （四）容量相关：判断空、获取大小

简单易用，和其他 STL 容器一致：

```cpp
multiset<int> ms{1,1,2};
// 1. empty()：判断是否为空，空返回true，否则false
if (ms.empty()) {
    cout << "容器为空" << endl;
} else {
    cout << "容器非空" << endl;
}

// 2. size()：获取当前元素的总个数（包含重复）
cout << "元素总数：" << ms.size() << endl; // 输出3

// 3. max_size()：获取容器理论最大存储个数（一般不用，受内存限制）
cout << "最大存储个数：" << ms.max_size() << endl;
```

#### （五）其他常用：交换、判等

```cpp
multiset<int> ms1{1,2}, ms2{3,3,4};
// 1. swap()：交换两个multiset的内容（同类型）
ms1.swap(ms2); // ms1变为{3,3,4}，ms2变为{1,2}

// 2. 判等：==/!=，按顺序和值比较（因为multiset有序，所以直接逐元素比）
if (ms1 == ms2) {
    cout << "相等" << endl;
} else {
    cout << "不相等" << endl;
}
```

### 五、multiset 的遍历方式

因为是**双向迭代器**，不支持随机访问，所以常用的遍历方式有 3 种：**普通迭代器、const 迭代器（只读）、范围 for（C++11 及以上）**，示例如下：

```cpp
#include <set>
#include <iostream>
using namespace std;

int main() {
    multiset<int> ms{1,1,2,3,3,5};

    // 方式1：普通迭代器（可用于遍历，不可修改元素）
    cout << "普通迭代器遍历：";
    for (multiset<int>::iterator it = ms.begin(); it != ms.end(); ++it) {
        cout << *it << " "; // 输出1 1 2 3 3 5
    }
    cout << endl;

    // 方式2：const迭代器（只读，更安全，推荐无需修改时使用）
    cout << "const迭代器遍历：";
    for (multiset<int>::const_iterator it = ms.cbegin(); it != ms.cend(); ++it) {
        cout << *it << " "; // 输出1 1 2 3 3 5
    }
    cout << endl;

    // 方式3：范围for（C++11及以上，最简洁，推荐日常使用）
    cout << "范围for遍历：";
    for (int num : ms) {
        cout << num << " "; // 输出1 1 2 3 3 5
    }
    cout << endl;

    return 0;
}
```

**注意**：即使使用普通迭代器，也**不能修改**解引用后的值（如`*it = 10;`会编译报错），这是由 multiset 的特性决定的。

### 六、multiset 与 set 的核心区别（必记）

两者底层、有序性、迭代器等完全一致，仅围绕**元素唯一性**有差异，用表格总结，一目了然：

|特性|set|multiset|
|---|---|---|
|元素唯一性|严格唯一，不允许重复|允许重复元素|
|`insert(val)`|重复插入无效果|重复插入会追加存储|
|`erase(val)`|删除唯一的 val（若有）|删除**所有**val|
|`count(val)`|仅返回 0 或 1|返回 val 的出现次数|
|`find(val)`|找到返回迭代器，否则 end ()|找到**第一个**val 的迭代器，否则 end ()|

### 七、multiset 的适用场景

`multiset`的核心优势是**有序 + 可重复 + 高效的增删查**，适合以下业务场景：

1. 需要**有序存储**且**允许重复**的场景（如统计班级学生成绩、存储多个相同的日志级别）；
2. 需要**快速统计某个元素出现次数**的场景（比 vector 遍历统计效率高得多）；
3. 需要**频繁插入、删除、查找**重复元素，且对效率有要求的场景；
4. 需获取**某一值的所有重复元素**的场景（通过`equal_range`快速定位区间）。

**反场景**：如果不需要有序，仅需存储重复元素，用`vector`/`list`更合适；如果需要元素唯一且有序，用`set`更合适。

### 八、简单示例：multiset 实际使用

结合以上所有知识点，写一个完整的可运行示例，覆盖**插入、删除、查找、统计、遍历**：

```cpp
#include <set>
#include <iostream>
using namespace std;

int main() {
    // 1. 初始化并插入元素
    multiset<int> score{85, 90, 85, 95, 90, 85, 100};
    cout << "全班成绩（有序）：";
    for (int s : score) {
        cout << s << " "; // 85 85 85 90 90 95 100
    }
    cout << endl;

    // 2. 统计成绩出现次数
    int target = 85;
    cout << target << "分的人数：" << score.count(target) << endl; // 3

    // 3. 查找并遍历所有85分
    auto [low, up] = score.equal_range(target); // C++17结构化绑定，也可用pair_it.first/second
    cout << "所有" << target << "分：";
    for (auto it = low; it != up; ++it) {
        cout << *it << " "; // 85 85 85
    }
    cout << endl;

    // 4. 删除单个90分（第一个）
    auto it = score.find(90);
    if (it != score.end()) score.erase(it);
    cout << "删除第一个90分后：";
    for (int s : score) {
        cout << s << " "; // 85 85 85 90 95 100
    }
    cout << endl;

    // 5. 删除所有85分
    score.erase(85);
    cout << "删除所有85分后：";
    for (int s : score) {
        cout << s << " "; // 90 95 100
    }
    cout << endl;

    // 6. 判空并输出大小
    cout << "当前成绩个数：" << score.size() << endl; // 3
    score.clear();
    if (score.empty()) cout << "成绩已清空" << endl;

    return 0;
}
```

---

### 总结

1. `multiset`是**有序、可重复**的关联式容器，底层红黑树，增删查效率 O (logn)，与`set`仅差**元素唯一性**；
2. 核心坑点：`erase(val)`会删除**所有**匹配元素，修改元素需**先删后插**，无下标访问；
3. 核心实用函数：`count(val)`统计次数、`equal_range(val)`获取重复元素区间，是处理重复元素的最优方式；
4. 遍历方式：推荐**范围 for**（简洁）或**迭代器**（灵活），不支持随机访问；
5. 适用场景：有序存储重复元素、快速统计 / 查找重复元素，非有序场景优先选 vector/list。