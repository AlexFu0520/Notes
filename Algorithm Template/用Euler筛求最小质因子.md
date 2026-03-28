# Code:
```cpp
const int N = 1e5 + 5;
vector<int> prime;
int minifactor[N];
void init() {
    for (int i = 2 ; i < N ; ++i) {
	    //如果i没有被更小的质数筛过，则说明i就是质数
        if (!minifactor[i]) { 
            prime.push_back(i);
            minifactor[i] = i; //质数i的最小质因子就是他自己
        }
        for (int j = 0 ; i * prime[j] < N ; ++j) {
	        //i与每个质数的倍数的最小质因子都是prime[j]
            minifactor[i * prime[j]] = prime[j]; 
            if (i % prime[j] == 0) break;
        }
    }
}
```