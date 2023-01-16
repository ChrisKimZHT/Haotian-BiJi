**Codeforces Round #844 (Div. 1 + Div. 2, based on VK Cup 2022 - Elimination Round)**

D. Many Perfect Squares

https://codeforces.com/contest/1782/problem/D

<!--more-->

4 seconds / 512 megabytes

standard input / standard output

## Problem

You are given a set $a_1, a_2, \ldots, a_n$ of distinct positive integers.

We define the squareness of an integer $x$ as the number of perfect squares among the numbers $a_1 + x, a_2 + x, \ldots, a_n + x$.

Find the maximum squareness among all integers $x$ between $0$ and $10^{18}$, inclusive.

Perfect squares are integers of the form $t^2$, where $t$ is a non-negative integer. The smallest perfect squares are $0, 1, 4, 9, 16, \ldots$.

## Input

Each test contains multiple test cases. The first line contains the number of test cases $t$ ($1 \le t \le 50$). The description of the test cases follows.

The first line of each test case contains a single integer $n$ ($1 \le n \le 50$) — the size of the set.

The second line contains $n$ distinct integers $a_1, a_2, \ldots, a_n$ in increasing order ($1 \le a_1 < a_2 < \ldots < a_n \le 10^9$) — the set itself.

It is guaranteed that the sum of $n$ over all test cases does not exceed $50$.

## Output

For each test case, print a single integer — the largest possible number of perfect squares among $a_1 + x, a_2 + x, \ldots, a_n + x$, for some $0 \le x \le 10^{18}$.

## Example

Input

> 4
> 5
> 1 2 3 4 5
> 5
> 1 6 13 22 97
> 1
> 100
> 5
> 2 5 10 17 26

Output

> 2
> 5
> 1
> 2

## Note

In the first test case, for $x = 0$ the set contains two perfect squares: $1$ and $4$. It is impossible to obtain more than two perfect squares.

In the second test case, for $x = 3$ the set looks like $4, 9, 16, 25, 100$, that is, all its elements are perfect squares.

## 题解

#### 核心思路

首先，$x$ 可能的取值有 $10^{18}$ 种，这个数据规模过于庞大。如果能减小这个规模到合适的大小，我们遍历所有可能的 $x$，找到符合条件最大的就行了。

#### 减小 $x$ 的规模

首先我们可以确定的是，答案最小为 $1$，因为单个数可以放缩到任意完全平方数。

我们继续判断答案是否 $\geq2$：取任意 $i,j$（$1\leq i<j\leq n$），判断是否存在 $x$，使 $a_i+x,a_j+x$ 均为完全平方数。若存在，则代表答案 $\geq2$，具体的值遍历 $a_i+x$ 求得；若不存在，则说明答案 $<2$，即答案为 $1$. 最后求得所有答案的最大值即可。

由于最多有 $\frac{n(n-1)}{2}$ 种取法，因此 $x$ 的数量级大约为 $n^2$（下文会做补充），数据规模瞬间从 $10^{18}$ 下降到了 $10^3$.

#### 给定一对数如何求解 $x$

该问题的形式化表述为：取任意 $i,j$（$1\leq i<j\leq n$），求解：
$$
\begin{cases}
a_i+x=u^2\\
a_j+x=v^2
\end{cases}
$$

三个未知数 $x,u,v$ 两个方程，很明显是无穷解的。但是我们要求的是整数解，那么解的数量就有限了。下面是解法：
$$
\begin{align}
两式相减&\Rightarrow a_i-a_j=u^2-v^2\\
&\Rightarrow a_i-a_j=(u+v)(u-v)\\
\end{align}
$$
为了简洁，令 $t:=a_i-a_j$，得到：
$$
t=(u+v)(u-v)
$$
将 $t$ 分为两个因子 $p,q$，即 $t=pq$，得到：
$$
pq=(u+v)(u-v)
$$
若 $q-p$ 为偶数，则可以构造得到解如下：（如果为奇数则这种分解方式无解）
$$
\begin{cases}
\displaystyle{u=\frac{q-p}{2}}\\\\
\displaystyle{v=\frac{q+p}{2}}
\end{cases}
$$
回代 $u$，将 $x$ 计算出来：
$$
x=u^2-a_i
$$
若 $x\geq0$，则符合条件，遍历 $a_i+x$，计算出多少个为完全平方数。（若 $<0$ 则说明这种情况不符合条件）

#### 时间复杂度

最多 $\frac{n(n-1)}{2}$ 种取法，数量级为 $n^2$

$x$ 的正因子个数为 $d(x)$，这个数值无法用表达式直接确定，但可以查表确定最大值为 $1344$.

| $n\leq$   | $\max\{\omega(n)\}$ | $\max\{d(n)\}$ | $n\leq$   | $\max\{\omega(n)\}$ | $\max\{d(n)\}$ |
| --------- | ------------------- | -------------- | --------- | ------------------- | -------------- |
| $10^1$    | $2$                 | $4$            | $10^9$    | $9$                 | $1344$         |
| $10^2$    | $3$                 | $12$           | $10^{10}$ | $10$                | $2304$         |
| $10^3$    | $4$                 | $32$           | $10^{11}$ | $10$                | $4032$         |
| $10^4$    | $5$                 | $64$           | $10^{12}$ | $11$                | $6720$         |
| $10^5$    | $6$                 | $128$          | $10^{13}$ | $12$                | $10752$        |
| $10^6$    | $7$                 | $240$          | $10^{14}$ | $12$                | $17280$        |
| $10^7$    | $8$                 | $448$          | $10^{15}$ | $13$                | $26880$        |
| $10^8$    | $8$                 | $768$          | $10^{16}$ | $13$                | $41472$        |
| $10^9$    | $9$                 | $1344$         | $10^{17}$ | $14$                | $64512$        |
| $10^{10}$ | $10$                | $2304$         | $10^{18}$ | $15$                | $103680$       |

通过遍历确定答案，数量级为 $n$

综上，时间复杂度勉强写成：$O(n^3\cdot\underset{1\leq i<j\leq n}\max\{d(a_j-a_i)\})$

用 $d(n)\approx\sqrt[3]{n}$ 来近似一下，那么如果是最坏情况可以达到 $10^8$ 规模，有可能超时（但实际上不会，运行时间离时限还很远）

由于正因子个数的无法直接计算，而且对于一个正因子，也不一定有解，所以这个时间复杂度没办法准确表述，实际情况应该远小于上述复杂度。

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

bool is_square(int n)
{
    int r = sqrt(1.0L * n);
    return r * r == n;
}

void solve()
{
    int n;
    cin >> n;
    vector<int> a(n);
    for (int i = 0; i < n; i++)
        cin >> a[i];
    int ans = 1;
    for (int i = 0; i < n; i++)
    {
        for (int j = i + 1; j < n; j++)
        {
            int t = a[j] - a[i];
            for (int p = 1; p * p <= t; p++)
            {
                if (t % p)
                    continue;
                int q = t / p;
                if ((q - p) % 2)
                    continue;
                int u = (q - p) / 2, v = (q + p) / 2;
                int x = u * u - a[i];
                if (x < 0)
                    continue;
                int res = 0;
                for (int ii = 0; ii < n; ii++)
                    if (is_square(a[ii] + x))
                        res++;
                ans = max(ans, res);
            }
        }
    }
    cout << ans << endl;
}

signed main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    int t;
    cin >> t;
    while (t--)
        solve();
    return 0;
}
```

