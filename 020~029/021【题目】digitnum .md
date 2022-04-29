**AtCoder Beginner Contest 238**

C - digitnum

https://atcoder.jp/contests/abc238/tasks/abc238_c

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $300$ points

### Problem Statement

Given an integer $N$, solve the following problem.

Let $f(x)=$ (The number of positive integers at most $x$ with the same number of digits as $x$).
Find $f(1)+f(2)+\dots+f(N)$ modulo $998244353$.

### Constraints

- $N$ is an integer.
- $1 \le N < 10^{18}$

------

### Input

Input is given from Standard Input in the following format:

> $N$

### Output

Print the answer as an integer.

------

### Sample Input 1

> 16


### Sample Output 1

> 73

- For a positive integer $x$ between $1$ and $9$, the positive integers at most $x$ with the same number of digits as $x$ are $1,2,\dots,x$.
  - Thus, we have $f(1)=1,f(2)=2,...,f(9)=9$.
- For a positive integer $x$ between $10$ and $16$, the positive integers at most $x$ with the same number of digits as $x$ are $10,11,\dots,x$.
  - Thus, we have $f(10)=1,f(11)=2,...,f(16)=7$.

------

### Sample Input 2

> 238

### Sample Output 2

> 13870

------

### Sample Input 3

> 999999999999999999

### Sample Output 3

> 762062362

Be sure to find the sum modulo $998244353$.

### 我的笔记

这道题很明显是一个逆元题，并没有什么难度，只不过当时完全忘记了除法逆元这回事，于是较大数据全部 WA

除法逆元：

>由费马小定理可推出：$\frac{1}{a} \% m = a^{m-2} \% m$ ，其中m为素数。
>
>那么，$\frac{b}{a} \% m$ 就可以变成 $b\times a^{m-2} \% m$
>
>如果 $m$ 太大，可以使用快速幂。

因为本题只用 /2 需要逆元，因此直接把 2 的逆元求出来然后代入就行了，不需要每次都求一边。写个程序算出来为 499122177

解决完逆元问题后我仍然没有得出正确答案，折腾了半天发现中途乘法溢出了 long long 范围。比如 `long long ans = ((1 + N - pow_10(num_digit - 1) + 1) % MOD * (N - pow_10(num_digit - 1) + 1) % MOD * 499122177) % MOD`，三个数都是 1e9 级别的，乘起来肯定溢出了。因此得每乘一次就取一次模。

### 源码

```cpp
#include <bits/stdc++.h>
#define MOD 998244353
#define INV_2 499122177

using namespace std;

long long pow_10(int x)
{
	long long ans = 1;
	for (int i = 0; i < x; i++)
		ans *= 10;
	return ans;
}

int main(void)
{
	long long N;
	cin >> N;
	long long t = N;
	int num_digit = 0;
	while (t)
	{
		t /= 10;
		num_digit++;
	}
	long long x = (N - pow_10(num_digit - 1) + 1) % MOD;
	long long ans = x;
	ans *= x + 1;
	ans %= MOD;
	ans *= INV_2;
	ans %= MOD;
	for (int i = num_digit - 1; i > 0; i--)
	{
		long long y = (pow_10(i) - pow_10(i - 1)) % MOD;
		long long tmp = y;
		tmp *= y + 1;
		tmp %= MOD;
		tmp *= INV_2;
		tmp %= MOD;
		ans += tmp;
		ans %= MOD;
	}
	cout << ans << endl;
	return 0;
}
```

