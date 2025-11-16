
### 核心区别：`emplace` vs 传统插入（`push_back`/`insert`）

传统插入函数（如 `vector::push_back`、`map::insert`）需要先构造一个元素对象（可能是临时对象），再将其**拷贝或移动**到容器中；而 `emplace` 系列函数则直接将构造元素所需的参数传递给元素的构造函数，在容器的内存空间中**就地构造元素**，省去了中间的拷贝 / 移动步骤。

### 常用 `emplace` 函数及场景

不同容器的 `emplace` 函数用法略有差异，核心都是 “传递构造参数，就地构造”。

#### 1. 顺序容器（如 `vector`、`deque`、`list`）

- **`emplace_back(args...)`**：在容器末尾就地构造元素（替代 `push_back`）。
- **`emplace(args...)`**：使用范围很局限，不能在vector中使用，在容器头部就地构造元素（仅 `deque`、`list` 等支持，替代 `push_front`）。
- **`emplace(pos, args...)`**：在迭代器 `pos` 指向的位置前就地构造元素（替代 `insert`）。

#### 2. 关联容器（如 `map`、`set`、`unordered_map`）

关联容器存储的是键值对（`map`/`unordered_map`）或单个元素（`set`/`unordered_set`），`emplace` 直接传递构造键值对（或元素）的参数。

- **`map::emplace(key, value)`**：直接构造键值对 `pair<Key, Value>` 并插入（替代 `insert(make_pair(key, value))`）。

### 关键注意事项

1. **参数需匹配构造函数**：`emplace` 的参数必须与元素类型的某个构造函数参数列表匹配，否则会编译错误。
    
2. **性能优势场景**：
    - 当元素是**自定义大对象**（拷贝 / 移动成本高）时，`emplace` 优势明显；
    - 若元素是基本类型（如 `int`、`string`）或小对象，`emplace` 与传统插入性能差异不大。
3. **避免误区**
    - `emplace` 并非 “一定更快”，例如插入已存在的对象时（`v.emplace_back(p)` 与 `v.push_back(p)`），两者都会触发拷贝（此时 `emplace` 无优势）；
    - 对于 `vector`，`emplace_back` 不会避免因容量不足导致的**元素移动**（扩容时的内部移动与插入方式无关）。