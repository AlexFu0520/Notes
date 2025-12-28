### 一、priority_queue 基本概念

`priority_queue` 是 C++ STL 中的**容器适配器**（不是独立容器），底层默认基于 `vector` 实现，核心逻辑是**堆（heap）** 结构：

- 默认是**大顶堆**：优先级最高的元素（数值最大）始终在队首，出队时先弹出最大元素。
- 插入 / 删除操作的时间复杂度为 \(O(logn)\)，访问队首为 \(O(1)\)。
- 使用前必须包含头文件：`#include <queue>`。
### 二、基本使用方法

#### 1. 基础声明与操作

先看一个最简单的 `int` 类型优先队列示例，包含核心成员函数：

```cpp
#include <iostream>
#include <queue>  // 必须包含的头文件
using namespace std;

int main() {
    // 1. 声明默认的大顶堆优先队列
    priority_queue<int> pq;

    // 2. 插入元素（push）：底层会自动调整堆结构
    pq.push(3);
    pq.push(1);
    pq.push(5);
    pq.push(2);

    // 3. 访问队首元素（top）：返回优先级最高的元素（大顶堆即最大值）
    // 注意：top() 是只读的，不能修改，且队列空时调用会崩溃
    cout << "队首元素：" << pq.top() << endl;  // 输出 5

    // 4. 弹出队首元素（pop）：删除堆顶元素，无返回值
    pq.pop();
    cout << "弹出后队首：" << pq.top() << endl;  // 输出 3

    // 5. 判断是否为空（empty）、获取元素个数（size）
    cout << "队列是否为空：" << (pq.empty() ? "是" : "否") << endl;  // 否
    cout << "队列元素个数：" << pq.size() << endl;  // 3

    // 遍历（注意：优先队列不支持迭代器，只能通过弹出方式遍历）
    cout << "遍历队列（弹出所有元素）：";
    while (!pq.empty()) {
        cout << pq.top() << " ";  // 依次输出 3 2 1
        pq.pop();
    }
    cout << endl;

    return 0;
}
```

**输出结果**：

```plaintext
队首元素：5
弹出后队首：3
队列是否为空：否
队列元素个数：3
遍历队列（弹出所有元素）：3 2 1 
```

#### 2. 自定义优先级（小顶堆）

如果需要**小顶堆**（数值最小的元素优先出队），需要显式指定模板参数和比较函数：

```cpp
#include <iostream>
#include <queue>
#include <vector>  // 底层容器显式声明时需要
using namespace std;

int main() {
    // 声明小顶堆：模板参数依次为 元素类型、底层容器类型、比较函数
    // greater<int> 表示 "更小的元素优先级更高"
    priority_queue<int, vector<int>, greater<int>> pq;

    pq.push(3);
    pq.push(1);
    pq.push(5);
    pq.push(2);

    cout << "小顶堆队首：" << pq.top() << endl;  // 输出 1

    cout << "遍历小顶堆：";
    while (!pq.empty()) {
        cout << pq.top() << " ";  // 依次输出 1 2 3 5
        pq.pop();
    }
    cout << endl;

    return 0;
}
```

#### 3. 自定义类型的优先级队列

如果优先队列存储自定义结构体（如学生、商品），需要自定义比较规则，有两种方式：
##### 方式 1：重载 < 运算符（适用于大顶堆）

```cpp
#include <iostream>
#include <queue>
#include <string>
using namespace std;

// 自定义结构体：学生（姓名+分数）
struct Student {
    string name;
    int score;

    // 重载 < 运算符：定义 "分数更高的学生优先级更高"
    // 注意：priority_queue 默认用 < 比较，且只重载 < 即可
    bool operator<(const Student& other) const {
        // 大顶堆：当前对象分数 < 其他对象 → 其他对象优先级更高
        return this->score < other.score;
    }
};

int main() {
    priority_queue<Student> pq;

    // 插入学生对象
    pq.push({"小明", 85});
    pq.push({"小红", 92});
    pq.push({"小刚", 78});

    // 遍历：分数从高到低输出
    cout << "按分数优先级输出：" << endl;
    while (!pq.empty()) {
        Student s = pq.top();
        cout << "姓名：" << s.name << "，分数：" << s.score << endl;
        pq.pop();
    }

    return 0;
}
```

**输出结果**：

```plaintext
按分数优先级输出：
姓名：小红，分数：92
姓名：小明，分数：85
姓名：小刚，分数：78
```

##### 方式 2：自定义比较函数（适用于小顶堆 / 复杂规则）

```cpp
#include <iostream>
#include <queue>
#include <string>
using namespace std;

struct Student {
    string name;
    int score;
};

// 自定义比较结构体：分数更低的学生优先级更高（小顶堆）
struct CompareStudent {
    bool operator()(const Student& a, const Student& b) {
        // 返回 true 表示 a 的优先级低于 b（b 会排在前面）
        return a.score > b.score;
    }
};

int main() {
    // 声明时指定自定义比较函数
    priority_queue<Student, vector<Student>, CompareStudent> pq;

    pq.push({"小明", 85});
    pq.push({"小红", 92});
    pq.push({"小刚", 78});

    cout << "按分数升序输出（小顶堆）：" << endl;
    while (!pq.empty()) {
        Student s = pq.top();
        cout << "姓名：" << s.name << "，分数：" << s.score << endl;
        pq.pop();
    }

    return 0;
}
```

**输出结果**：

```plaintext
按分数升序输出（小顶堆）：
姓名：小刚，分数：78
姓名：小明，分数：85
姓名：小红，分数：92
```

### 三、核心注意事项

1. `priority_queue` 不支持迭代器，无法直接遍历，只能通过 `top()` + `pop()` 方式遍历（遍历后队列为空）。
2. `top()` 函数在队列为空时调用会导致程序崩溃，使用前务必用 `empty()` 判断。
3. `pop()` 仅删除堆顶元素，无返回值；若需要获取并删除，需先调用 `top()` 再调用 `pop()`。
4. 底层容器可以替换（如 `deque`），但通常用默认的 `vector` 即可。

### 总结

1. `priority_queue` 是基于堆实现的优先队列，默认**大顶堆**，核心操作是 `push()`/`top()`/`pop()`。
2. 实现小顶堆需指定模板参数 `greater<T>`，自定义类型需重载 `<` 或自定义比较函数。
3. 无迭代器，遍历需弹出元素，使用前务必检查队列是否为空。