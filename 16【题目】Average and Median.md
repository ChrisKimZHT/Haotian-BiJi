**AtCoder Beginner Contest 236**

E - Average and Median

https://atcoder.jp/contests/abc236/tasks/abc236_e

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $500$ points

### Problem Statement

We have $N$ cards. The $i$-th card $(1 \leq i \leq N)$ has an integer $A_i$ written on it.

Takahashi will choose any number of cards from these. However, for each $i$ $(1 \leq i \leq N - 1)$, at least one of the $i$-th and $(i+1)$-th cards must be chosen.

Find the following values.

- The maximum possible average of the integers written on the chosen cards
- The maximum possible median of the integers written on the chosen cards

Here, the median of the $n$ integers is defined to be the $\lceil \frac{n}{2} \rceil$-th smallest of them, where $\lceil x \rceil$ is the smallest integer not less than $x$.

### Constraints

- $2 \leq N \leq 10^5$
- $1 \leq A_i \leq 10^{9}$
- All values in input are integers.

------

### Input

Input is given from Standard Input in the following format:

> $N$
> $A_1$ $\ldots$ $A_N$

### Output

Print two lines. The first and second lines should contain the maximum possible average and maximum possible median of the integers written on the chosen cards, respectively. For the average, your output will be considered correct when its relative or absolute error from the exact answer is at most $10^{-3}$.

------

### Sample Input 1

> 6
> 2 1 2 1 1 10

### Sample Output 1

> 4
> 2

Choosing the $2$-nd, $4$-th, and $6$-th cards makes the average of the written integers $\frac{12}{3} = 4$, which is the maximum possible.

Choosing the $1$-st, $3$-rd, $5$-th, and $6$-th cards makes the median of the written integers $2$, which is the maximum possible.

------

### Sample Input 2

> 7
> 3 1 4 1 5 9 2

### Sample Output 2

> 5.250000000
> 4

For the average, your output may contain some degree of error: for example, the output $5.2491$ is still considered correct. For the median, however, the exact value must be printed.

### 我的笔记

#### 用二分转化问题

可将“平均数（中位数）是什么？”这个问题转化为“平均数（中位数）是否大于 $K$ ？”，这样我们就可以对 $K$ 用二分，找到平均数（中位数）

**平均数是否大于** $K$ ?

令 $B_i=A_i-K$，若将选出的卡片对应的 $B_i$ 求和，若 $>0$ 则为真，若 $<0$ 则为假。

**中位数是否大于** $K$ ?

若 $A_i<K$，令 $B_i=-1$；若 $A_i\geq K$，令 $B_i=1$。若将选出的卡片对应的 $B_i$ 求和，若 $>0$ 则为真，若 $<0$ 则为假。

#### 用动态规划选择卡片

可以看到，我们将问题转化后，这两个问题都需要对 $B_i$ 求和，而且因为我们要求最大的平均数（中位数），所以我们要求的是最大的情况。这个问题可以从 $1$ 张卡片递推到 $N$ 张卡片，因此用动态规划。

创建两个数组：
$f[i]$ 表示在 $1\sim i$ 范围选择卡片，且选择第 $i$ 张卡片的和。
$g[i]$ 表示在 $1\sim i$ 范围选择卡片，且不选第 $i$ 张卡片的和。

可以建立递推关系：
$f[i]=\max(f[i-1],g[i-1])+B_i$
$g[i]=f[i-1]$

分析下递推关系，先看 $g[i]$，因为我们没有选第 $i$ 张卡片，所以第 $i-1$ 张卡片必选，否则就会出现两个连着的未选卡片，因此就等于 $f[i-1]$。再看 $f[i]$，因为我们选了第 $i$ 张卡片，所以第 $i-1$ 张卡片选不选都符合题意，为了让和最大，我们 $f[i-1]$ 和 $g[i-1]$ 中更大的那个，因为选了第 $i$ 张卡片，还得把 $B_i$ 加上。

时间复杂度为 $O(N)$

### 源码

```cpp
#include <bits/stdc++.h>
#define SIZE 100010
#define esp 1e-4

using namespace std;

int N;
int A[SIZE];

int main(void)
{
	ios::sync_with_stdio(false);
	cin >> N;
	for (int i = 1; i <= N; i++)
	{
		cin >> A[i];
	}
	double l1 = 1, r1 = 1e9;
	while (r1 - l1 > esp)
	{
		double mid = (l1 + r1) / 2;
		double f[SIZE], g[SIZE];
		f[0] = g[0] = 0;
		for (int i = 1; i <= N; i++)
		{
			f[i] = max(f[i - 1], g[i - 1]) + A[i] - mid;
			g[i] = f[i - 1];
		}
		if (max(f[N], g[N]) >= 0)
		{
			l1 = mid;
		}
		else
		{
			r1 = mid;
		}
	}
	cout << l1 << endl;
	int l2 = 1, r2 = 1e9;
	while (l2 < r2)
	{
		int mid = (l2 + r2 + 1) / 2;
		int f[SIZE], g[SIZE];
		f[0] = g[0] = 0;
		for (int i = 1; i <= N; i++)
		{
			f[i] = max(f[i - 1], g[i - 1]) + ((A[i] >= mid) ? 1 : -1);
			g[i] = f[i - 1];
		}
		if (max(f[N], g[N]) > 0)
		{
			l2 = mid;
		}
		else
		{
			r2 = mid - 1;
		}
	}
	cout << l2 << endl;
	return 0;
}
```

### 补题时出现的错误

平均数可能为小数，因此需要实数范围的二分。二分判断条件为 `max(f[N], g[N]) >= 0`，因为是实数范围的二分，所以改成 `max(f[N], g[N]) > 0` 也能过。

中位数为整数，因此用整数二分，这里出错了。首先是 `mid = (l2 + r2 + 1) / 2`，因为这个二分相当于查找的是满足条件的最大的一个数，因此是需要加一的。其次是判断条件 `max(f[N], g[N]) > 0`，这个二分要找满足该条件的最大的数，其实就是要找等于零的情况，最开始我带了等号，答案就会变大了。