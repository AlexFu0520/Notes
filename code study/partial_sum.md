1.在 C++ 标准库中，`partial_sum` 是一个用于计算**前缀和（部分和）** 的算法，定义在 `<numeric>` 头文件中。它可以对一个序列的元素进行累积计算，将结果存储到另一个序列中，第 `i` 个结果是原序列前 `i+1` 个元素的累积和（或自定义操作的结果）。

2.示例代码
```cpp
#include <iostream> 
#include <vector> 
#include <numeric> // partial_sum 所在头文件 
using namespace std;

int main() { 
	vector<int> input = {1, 2, 3, 4, 5}; 
	vector<int> output(input.size()); // 输出容器需提前分配空间 
	
	// 计算前缀和：output[0] = 1, output[1] = 1+2=3, output[2] = 1+2+3=6, ...
	partial_sum(input.begin(), input.end(), output.begin()); 
	// 输出结果 
	for (int num : output) { std::cout << num << " "; 
	// 输出：1 3 6 10 15
	} 
	
	return 0; }
```

3.计算差分（自定义函数）
```cpp
#include <iostream>
#include <vector> 
#include <numeric>
using namespace std;
// 自定义二元操作：op(a, b) = a - b 
int subtract(int a, int b) 
{ 
	return a - b; 
} 

int main() 
{ 
	vector<int> input = {10, 3, 2, 1}; 
	vector<int> output(input.size()); 
	// 计算差分：output[0]=10, output[1]=10-3=7, output[2]=7-2=5, output[3]=5-1=4 
	partial_sum(input.begin(), input.end(), output.begin(), subtract); 
	for (int num : output) 
	{ 
		std::cout << num << " "; 
		// 输出：10 7 5 4 
	} 
	return 0; 
}
```

