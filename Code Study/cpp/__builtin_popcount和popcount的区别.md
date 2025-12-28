`__builtin_popcount` 和 `popcount` 都是用于**统计整数二进制表示中 1 的个数**（“人口计数”）的工具，但核心区别在于 **来源（标准 / 编译器扩展）、适用范围、语法细节和兼容性**，以下是详细对比：
### 一、核心区别总览

|特性|`__builtin_popcount`|`popcount`（C++20 标准）/ `std::popcount`|
|---|---|---|
|**来源**|GCC/Clang 编译器内置扩展（非标准）|C++20 标准库函数（`<bit>` 头文件）|
|**命名空间**|全局命名空间（无 `std::`）|必须在 `std::` 命名空间下（C++20+）|
|**参数类型限制**|仅支持 32 位无符号整数（`unsigned int`）|支持所有无符号整数类型（`uint8_t`/`uint64_t` 等）|
|**符号整数处理**|传入有符号整数（如 `int`）会触发未定义行为|禁止传入有符号整数（编译报错，更安全）|
|**兼容性**|仅 GCC/Clang 及兼容编译器（如 MinGW）支持|所有支持 C++20 的编译器（GCC10+/Clang11+/MSVC19.20+）|
|**语法**|直接调用（无需头文件）|需包含 `<bit>` 头文件，加 `std::` 前缀|

### 二、分点详细说明

#### 1. 来源与标准性（最核心区别）

- **`__builtin_popcount`**：是 GCC、Clang 等编译器提供的**非标准内置函数**（“builtin” 表示编译器内置优化），不属于 C/C++ 标准。设计目的是利用硬件指令（如 x86 的 `popcnt` 指令）实现高效计数，无需依赖标准库。
    
- **`popcount` / `std::popcount`**：是 C++20 标准新增的**标准库函数**，定义在 `<bit>` 头文件中，属于 `std` 命名空间。目的是统一 “人口计数” 的标准接口，解决编译器扩展的兼容性问题，同时提供更安全的类型检查。
    
#### 2. 参数类型与兼容性

##### （1）`__builtin_popcount` 的类型限制

- 仅支持 **32 位无符号整数**（`unsigned int`）。
    
    - 若传入 64 位整数（如 `unsigned long long`），需用对应的扩展函数 `__builtin_popcountll`；
    - 若传入 16 位 / 8 位整数（如 `uint16_t`），会被隐式转换为 `unsigned int`（无问题，但需注意高位补 0 不影响结果）；
    - 若传入有符号整数（如 `int`）：若该整数为负数（二进制最高位为 1），会因符号扩展导致未定义行为（结果错误）。
    
    示例（错误用法）：
    ```cpp
    int x = -1; // 二进制为全 1（取决于位数）
    __builtin_popcount(x); // 未定义行为！结果可能不是预期的 32/64
    ```

##### （2）`std::popcount` 的类型支持

- 要求参数必须是 **无符号整数类型**（`unsigned char`/`uint32_t`/`uint64_t` 等），编译器会强制检查：
    
    - 传入有符号整数（如 `int`）会直接编译报错（避免未定义行为）；
    - 自动适配不同位数的无符号类型，无需手动选择 “ll”/“l” 后缀（如 64 位直接传 `uint64_t` 即可）。
    
    示例（正确用法）：
    
    ```cpp
    #include <bit>   // 必须包含
    #include <cstdint>
    
    uint64_t y = 0x123456789ABCDEF0;
    std::popcount(y); // 正确，自动处理 64 位无符号数
    
    int z = 5;
    // std::popcount(z); // 编译报错：不允许有符号整数
    ```
    
#### 3. 兼容性与跨平台

- **`__builtin_popcount`**：仅支持 GCC、Clang、MinGW 等兼容编译器，**MSVC（Visual Studio）不支持**（MSVC 对应的扩展是 `_mm_popcnt_u32`，需配合 SSE4.2 指令集）。适合仅针对 GCC/Clang 环境的开发（如 Linux 服务器程序）。
    
- **`std::popcount`**：属于 C++20 标准，只要编译器支持 C++20（GCC10+、Clang11+、MSVC19.20+），即可跨平台使用，无需关心编译器差异。适合新项目或已升级到 C++20 的项目，是未来的推荐用法。
    
#### 4. 效率

两者效率**基本一致**：均会被编译器优化为硬件 `popcnt` 指令（若硬件支持，如 x86-64 的 SSE4.2、ARM 的 NEON），无需手动优化。

- 若硬件不支持 `popcnt` 指令，`__builtin_popcount` 会生成软件模拟代码；
- `std::popcount` 同样会根据硬件自动选择最优实现（硬件指令或软件模拟），兼容性更优。
### 三、补充：相关变体函数

#### 1. `__builtin_popcount` 家族（编译器扩展）

- `__builtin_popcount`：32 位无符号整数（`unsigned int`）；
- `__builtin_popcountl`：长整数（`unsigned long`）；
- `__builtin_popcountll`：64 位无符号整数（`unsigned long long`）。

#### 2. C++20 标准库相关函数（`<bit>`）

- `std::popcount`：无符号整数的 1 计数；
- `std::has_single_bit`：判断是否为 2 的幂（比 `x & (x-1) == 0` 更高效）；
- `std::bit_width`：获取整数的二进制位数（不含前导 0）。
### 四、使用建议

1. **新项目 / 可升级到 C++20**：优先使用 `std::popcount`（标准、安全、跨平台）；
2. **旧项目 / 仅支持 GCC/Clang**：使用 `__builtin_popcount`（无需升级标准，兼容性受限）；
3. **需要兼容 MSVC 且未用 C++20**：MSVC 下可用 `_mm_popcnt_u32`（需包含 `<intrin.h>`，且开启 SSE4.2 指令集）；
4. **避免传入有符号整数**：无论用哪种函数，尽量用无符号整数（`uint32_t`/`uint64_t`），避免未定义行为。
### 总结

|场景|推荐函数|
|---|---|
|C++20+ 跨平台项目|`std::popcount`|
|GCC/Clang 专属项目（无 C++20）|`__builtin_popcount`|
|MSVC 项目（无 C++20）|`_mm_popcnt_u32`|

核心差异一句话：`__builtin_popcount` 是编译器非标准扩展，`std::popcount` 是 C++20 标准函数，后者更安全、跨平台、推荐优先使用。