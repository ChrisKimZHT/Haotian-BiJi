**AtCoder Beginner Contest 238**

D - AND and SUM

https://atcoder.jp/contests/abc238/tasks/abc238_d

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $400$ points

### Problem Statement

Solve the following problem for $T$ test cases.

> Given are non-negative integers $a$ and $s$. Is there a pair of non-negative integers $(x,y)$ that satisfies both of the conditions below?
>
> - $x\ \text{AND}\ y=a$
> - $x+y=s$

### Constraints

- $1 \leq T \leq 10^5$
- $0 \leq a,s \lt 2^{60}$
- All values in input are integers.

------

### Input

Input is given from Standard Input. The first line is in the following format:

> $T$

Then, $T$ test cases follow. Each test case is in the following format:

> $a$ $s$

### Output

Print $T$ lines. The $i$-th line $(1 \leq i \leq T)$ should contain `Yes` if, in the $i$-th test case, there is a pair of non-negative integers $(x,y)$ that satisfies both of the conditions in the Problem Statement, and `No` otherwise.

### Sample Input 1

> 2
> 1 8
> 4 2

### Sample Output 1

> Yes
> No

In the first test case, some pairs such as $(x,y)=(3,5)$ satisfy the conditions.

In the second test case, no pair of non-negative integers satisfies the conditions.

------

### Sample Input 2

> 4
> 201408139683277485 381410962404666524
> 360288799186493714 788806911317182736
> 18999951915747344 451273909320288229
> 962424162689761932 1097438793187620758

### Sample Output 2

> No
> Yes
> Yes
> No

### 我的笔记

本题纯思维题，需要两个结论：

1. $(x\ \text{XOR}\ y)\ \text{AND}\ (x\ \text{AND}\ y)=0$

2. $x+y=2\times(x\ \text{AND}\ y)+(x\ \text{XOR}\ y)$

下面来推导一下这两个结论。

**先看第一个结论：**$(x\ \text{XOR}\ y)\ \text{AND}\ (x\ \text{AND}\ y)=0$

用具体的数来说明更形象，不妨令：
$x=00110100_2$
$y=11011110_2$

那么：
$x\ \text{AND}\ y=00010100_2$
$x\ \text{XOR}\ y=11101010_2$

观察上面两数，用通俗的话来解释：
$x\ \text{AND}\ y$ 的第 $a$ 位为 $1$ 就是 $x$ 和 $y$ 两数的第 $a$ 位**同时都**为 $1$
$x\ \text{XOR}\ y$ 的第 $a$ 位为 $1$ 就是 $x$ 和 $y$ 两数的第 $a$ 位**有一个**为 $1$

因此这两个事件不可能同时发生，即不可能两个数的同一位**既**同时为 $1$ **又**有一个为 $1$，所以第一个结论得证。

**再看第二个结论：**$x+y=2\times(x\ \text{AND}\ y)+(x\ \text{XOR}\ y)$

我们把 $x$ 和 $y$ 都为 $1$ 的那位单独提出来（对应加号前的数）：
$x=00110100_2=00010100_2+00100000_2$
$y=11011110_2=00010100_2+11001010_2$

再将 $x$ 和 $y$ 相加：
$\begin{aligned}x+y&=00010100_2+00100000_2+00010100_2+11001010_2\\&=2\times00010100_2+11101010_2\\&=2\times(x\ \text{AND}\ y)+(x\ \text{XOR}\ y)\end{aligned}$

至于为什么另外两个数加起来是 $x\ \text{XOR}\ y$，那是因为把同为 $1$ 的提出去了，剩下的都是同为 $0$ 或二者之一为 $1$，加起来当然就是 $x\ \text{XOR}\ y$.

**本题解法：**

本题知道：
$x\ \text{AND}\ y=a$
$x+y=s$

由第二个结论可求出 $x\ \text{XOR}\ y=s-2\times a$

再使用第一个结论，若 $(s-2\times a)\ \text{AND}\ a=0$，则符合题意，反之不符合。

还有一个大前提，$s-2\times a$ 要 $\geq0$，否则就肯定不成立。

### 源码

```cpp
#include <bits/stdc++.h>

using namespace std;

int main(void)
{
	int T;
	cin >> T;
	while (T--)
	{
		long long a, s;
		cin >> a >> s;
		long long x = s - 2 * a;
		if (x >= 0 && !(x & a))
			cout << "Yes" << endl;
		else
			cout << "No" << endl;
	}
	return 0;
}
```

