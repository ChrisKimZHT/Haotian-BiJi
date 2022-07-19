**AtCoder Regular Contest 144**

B - Gift Tax

https://atcoder.jp/contests/arc144/tasks/arc144_b

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $400$ points

### Problem Statement

You are given positive integers a*a* and b*b* such that $a\leq b$, and a sequence of positive integers $A = (A_1, A_2, \ldots, A_N)$.

On this sequence, you can perform the following operation any number of times (possibly zero):

- Choose distinct indices $i, j$ ($1\leq i, j \leq N$). Add $a$ to $A_i$ and subtract $b$ from $A_j$.

Find the maximum possible value of $\min(A_1, A_2, \ldots, A_N)$ after your operations.

### Constraints

- $2\leq N\leq 3\times 10^5$
- $1\leq a\leq b\leq 10^9$
- $1\leq A_i\leq 10^{9}$

------

### Input

Input is given from Standard Input in the following format:

> $N$ $a$ $b$
> $A_1$ $A_2$ $\ldots$ $A_N$

### Output

Print the maximum possible value of $\min(A_1, A_2, \ldots, A_N)$ after your operations.

------

### Sample Input 1

> 3 2 2
> 1 5 9

### Sample Output 1

> 5

Here is one way to achieve $\min(A_1, A_2, A_3) = 5$.

- Perform the operation with $i = 1, j = 3$. $A$ becomes $(3, 5, 7)$.
- Perform the operation with $i = 1, j = 3$. $A$ becomes $(5, 5, 5)$.

------

### Sample Input 2

> 3 2 3
> 11 1 2

### Sample Output 2

> 3

Here is one way to achieve $\min(A_1, A_2, A_3) = 3$.

- Perform the operation with $i = 1, j = 3$. $A$ becomes $(13, 1, -1)$.
- Perform the operation with $i = 2, j = 1$. $A$ becomes $(10, 3, -1)$.
- Perform the operation with $i = 3, j = 1$. $A$ becomes $(7, 3, 1)$.
- Perform the operation with $i = 3, j = 1$. $A$ becomes $(4, 3, 3)$.

------

### Sample Input 3

> 3 1 100
> 8 5 6

### Sample Output 3

> 5

You can achieve $\min(A_1, A_2, A_3) = 5$ by not performing the operation at all.

------

### Sample Input 4

> 6 123 321
> 10 100 1000 10000 100000 1000000

### Sample Output 4

> 90688

------

### 解题笔记

最开始用模拟写了遍，理所当然超时了，然后就不会做了（太久没练又退化了

这题是用二分来解决，在 $1\sim 10^9$ 的区间二分找到最大的 $C$，使进行若干次操作后的 $A[i]\geq C\ (0\leq i<N)$，这个 $C$ 便是答案。

那么如何判断进行若干次操作后，$A[i]$ 是否能够满足 $A[i]\geq C\ (0\leq i<N)$？

可以遍历 $A[i]$，

若 $A[i]\geq C$，那么它可以减去 $\lfloor(A[i]-C)/b\rfloor$  个 $b$，仍能保持 $A[i]\geq C$。

若 $A[i]<C$，那么它需要加上 $\lceil(C-A[i])/a\rceil$ 个 $a$，才能达成 $A[i]\geq C$。

若所有的减去的 $b$ 的数量**大于等于**加上的 $a$ 的数量，那么说明进行若干次操作，能够满足 $A[i]\geq C\ (0\leq i<N)$

### 代码

```cpp
#include <bits/stdc++.h>
#define int long long

using namespace std;

const int MAXN = 3e5 + 20;
int N, a, b;
int A[MAXN];

bool ok(int n)
{
    int x = 0, y = 0;
    for (int i = 0; i < N; i++)
    {
        if (A[i] >= n)
            x += (A[i] - n) / b;
        else
            y += ceil(1.0 * (n - A[i]) / a);
    }
    return x >= y;
}

signed main()
{
    cin >> N >> a >> b;
    for (int i = 0; i < N; i++)
        cin >> A[i];
    int l = 0, r = INT32_MAX;
    while (l < r)
    {
        int mid = (l + r) / 2;
        if (ok(mid))
            l = mid + 1;
        else
            r = mid;
    }
    cout << l - 1 << endl;
    return 0;
}
```



