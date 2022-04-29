**AtCoder Regular Contest 135**

C - XOR to All

https://atcoder.jp/contests/arc135/tasks/arc135_c

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $500$ points

### Problem Statement

You are given a sequence of non-negative integers $A = (A_1, \ldots, A_N)$. You can do the operation below any number of times (possibly zero).

- Choose an integer $x\in \{A_1,\ldots,A_N\}$.
- Replace $A_i$ with $A_i\oplus x$ for every $i$ ($\oplus$ denotes the bitwise $\mathrm{XOR}$ operation).

Find the maximum possible value of $\sum_{i=1}^N A_i$ after your operations.

### Constraints

- $1\leq N \leq 3\times 10^{5}$
- $0\leq A_i < 2^{30}$

------

### Input

Input is given from Standard Input from the following format:

> $N$
> $A_1$ $\ldots$ $A_N$

### Output

Print the maximum possible value of $\sum_{i=1}^N A_i$ after your operations.

------

### Sample Input 1

> 5
> 1 2 3 4 5

### Sample Output 1

> 19

Here is a sequence of operations that achieves $\sum_{i=1}^N A_i = 19$.

- Initially, the sequence $A$ is $(1,2,3,4,5)$.
- An operation with $x = 1$ changes it to $(0,3,2,5,4)$.
- An operation with $x = 5$ changes it to $(5,6,7,0,1)$, where $\sum_{i=1}^N A_i = 5 + 6 + 7 + 0 + 1 = 19$.

------

### Sample Input 2

> 5
> 10 10 10 10 10

### Sample Output 2

> 50

Doing zero operations achieves $\sum_{i=1}^N A_i = 50$.

------

### Sample Input 3

> 5
> 3 1 4 1 5

### Sample Output 3

> 18

### 我的笔记

本题中的 "You can do the operation below any number of times (possibly zero)." 和 "Sample 1" 就是个迷惑人的幌子，说是可以进行任意次异或操作，然而结果等价于只进行一次异或操作，因为：$(A_x\ \mathrm{XOR}\ A_i)\ \mathrm{XOR}\ (A_y\ \mathrm{XOR}\ A_i)=A_x\ \mathrm{XOR}\ A_y$

具体到示例 $1$ 的 $(1,2,3,4,5)$，先用 $1$ 异或得到 $(0,3,2,5,4)$，再用 $5$ 异或得到 $(5,6,7,0,1)$，这俩步其实等价于直接用 $4$ 异或（$4$ 是第二步 $5$ 原来对应的那个数）

所以该题简化为：有 $N$ 个数，在其中选 $1$ 个数（可以不选），用这个数与这 $N$ 个数异或，将结果求和，求和的最大值。

然后直接暴力求解一个个异或再求和肯定是不可行的，要整体来操作。

本题异或、求和都可以看成按位操作，所以可以直接统计出每一位 $0$ 和 $1$ 的数量，如果异或的那个数该位为 $0$，那么 $0、1$ 不翻转，如果是 $1$ 那么 $0、1$ 翻转，统计一次需要循环 $30$ 次（因为数字不大于 $2^{30}$）。统计了一位之后就可以把这一位算出来，第一位乘 $2^{0}$，第二位乘 $2^{1}$，第三位乘 $2^{2}$，$\dots$，第三十位乘 $2^{29}$，这样就只花了 $30$ 次循环就求出了一次和。总共有 $N$ 个数字，用每个数字都试一遍，然后找到最大值即可，时间复杂度 $O(30\times N)$ ，$1\leq N \leq 3\times 10^{5}$，因此可以 AC.

### 代码

```cpp
#include <bits/stdc++.h>

using namespace std;

int main(void)
{
	ios::sync_with_stdio(false);
	cin.tie(0);
	int N;
	cin >> N;
	vector<int> A(N);
	vector<int> bit(30);
	for (int i = 0; i < N; i++)
	{
		cin >> A[i];
		int t = A[i];
		for (int i = 0; t; i++, t >>= 1)
		{
			bit[i] += t & 1;
		}
	}
	long long sum = accumulate(A.begin(), A.end(), 0LL);
	for (int i = 0; i < N; i++)
	{
		long long cur_sum = 0, d = 1;
		int t = A[i], bit_sum;
		for (int j = 0; j < 30; j++)
		{
			if (t & 1)
			{
				bit_sum = N - bit[j];
			}
			else
			{
				bit_sum = bit[j];
			}
			t >>= 1;
			cur_sum += d * bit_sum;
			d <<= 1;
		}
		sum = max(sum, cur_sum);
	}
	cout << sum << endl;
	return 0;
}
```

