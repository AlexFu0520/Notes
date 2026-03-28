# Code:
```cpp
const int N = 1e5 + 5;
int prime[N]; //用静态数组更快
int minifactor[N];
int primepos;
void init() {
    for (int i = 2 ; i < N ; ++i) {
        if (!minifactor[i]) {
            prime[primepos++] = i;
            minifactor[i] = i; //质数i的最小质因子就是他自己
        }
        for (int j = 0 ; i * prime[j] < N ; ++j) {
            minifactor[i * prime[j]] = prime[j];
            if (i % prime[j] == 0) break;
        }
    }
}
```