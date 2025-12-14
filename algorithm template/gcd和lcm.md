###  C++代码

```cpp
int gcd(int a,int b)
{
	return b == 0 ? a : gcd(b,a % b);
}
int lcm(int a, int b)
{
	return a / gcd(a,b) * b;
}
```

### Python代码

```python
def gcd(a,b):
	if b != 0:
		gcd(b,a % b)
	else:
		return a
```
