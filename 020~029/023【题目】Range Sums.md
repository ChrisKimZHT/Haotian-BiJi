**AtCoder Beginner Contest 238**

E - Range Sums

https://atcoder.jp/contests/abc238/tasks/abc238_e

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $500$ points

### Problem Statement

Takahashi has a secret integer sequence $a$. You know that the length of $a$ is $N$.

You want to guess the contents of $a$. He has promised to give you the following $Q$ additional pieces of information.

- The $i$-th information: the value $a_{l_i}+a_{l_i+1}+\cdots+a_{r_i}$.

Is it possible to determine the sum of all elements in $a$, $a_1+a_2+\cdots+a_N$, if the $Q$ pieces of promised information are given?

### Constraints

- $1 \leq N \leq 2 \times 10^5$
- $1 \leq Q \leq \min(2 \times 10^5,\frac{N(N+1)}{2})$
- $1 \leq l_i \leq r_i \leq N$
- $(l_i,r_i) \neq (l_j,r_j)\ (i \neq j)$
- All values in input are integers.

------

### Input

Input is given from Standard Input in the following format:

> $N$ $Q$
> $l_1$ $r_1$
> $l_2$ $r_2$
> $\hspace{0.4cm}\vdots$
> $l_Q$ $r_Q$

### Output

If it is possible to determine the sum of all elements in $a$, print `Yes`; otherwise, print `No`.

------

### Sample Input 1

> 3 3
> 1 2
> 2 3
> 2 2

### Sample Output 1

> Yes

From the first and second information, we can find the value $a_1+a_2+a_2+a_3$. By subtracting the value of $a_2$ from it, we can determine the value $a_1+a_2+a_3$.

------

### Sample Input 2

> 4 3
> 1 3
> 1 2
> 2 3

### Sample Output 2

> No

We can determine the sum of the first $3$ elements of $a$, but not the sum of all elements.

------

### Sample Input 3

> 4 4
> 1 1
> 2 2
> 3 3
> 1 4

### Sample Output 3

> Yes

The fourth information directly gives us the sum of all elements.

### 我的笔记

如果能想到并查集这题非常的简单。

首先有 $N$ 个数，我们把这些数排成一排，那么就有 $N+1$ 个间隔（包括两端），然后我们把每一个 $l$ $r$ 对应到这 $N+1$ 个间隔，只要最后 $0\sim N+1$ 相连，那么就说明可以求出和。

如下图，表示的就是 1 6；2 4；2 6；5 8 这四个联通，最后 0 和 8 联通，即可以求出和。

<img src="https://assets.zouht.com/img/note/23-01.webp" style="zoom: 67%;" />

求联通，很简单想到用并查集，因此初始化一个大小为 $N+1$ 的并查集即可。

另外，这题必须要用路径优化，否则会有两个测试点 TLE，按秩合并倒不重要，实测按秩合并效率反而略低。

### 源码

```cpp
#include <bits/stdc++.h>
#define MAXN 200010

using namespace std;

int fa[MAXN];
inline void init(int n)
{
	for (int i = 0; i < n; i++)
		fa[i] = i;
}

int find(int x)
{
	return x == fa[x] ? x : (fa[x] = find(fa[x]));
}

inline void merge(int i, int j)
{
	fa[find(i)] = find(j);
}

int main(void)
{
	ios::sync_with_stdio(false);
	int N, Q;
	cin >> N >> Q;
	init(N + 1);
	while (Q--)
	{
		int l, r;
		cin >> l >> r;
		merge(l - 1, r);
	}
	if (find(0) == find(N))
	{
		cout << "Yes" << endl;
	}
	else
	{
		cout << "No" << endl;
	}
	return 0;
}
```

