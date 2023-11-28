**Codeforces Round 911 (Div. 2)**

D. Small GCD

https://codeforces.com/contest/1900/problem/D

<!--more-->

2 seconds / 256 megabytes

standard input / standard output

## Problem

Let $a$, $b$, and $c$ be integers. We define function $f(a, b, c)$ as follows:

Order the numbers $a$, $b$, $c$ in such a way that $a \le b \le c$. Then return $\gcd(a, b)$, where $\gcd(a, b)$ denotes the greatest common divisor (GCD) of integers $a$ and $b$.

So basically, we take the $\gcd$ of the $2$ smaller values and ignore the biggest one. 

You are given an array $a$ of $n$ elements. Compute the sum of $f(a_i, a_j, a_k)$ for each $i$, $j$, $k$, such that $1 \le i < j < k \le n$. 

More formally, compute

$$
\sum_{i = 1}^n \sum_{j = i+1}^n \sum_{k =j +1}^n f(a_i, a_j, a_k).
$$

## Input

Each test contains multiple test cases. The first line contains the number of test cases $t$ ($1 \le t \le 10$). The description of the test cases follows.

The first line of each test case contains a single integer $n$ ($3 \le n \le 8 \cdot 10^4$) — length of the array $a$.

The second line of each test case contains $n$ integers, $a_1, a_2, \ldots, a_n$ ($1 \le a_i \le 10^5$) — elements of the array $a$.

It is guaranteed that the sum of $n$ over all test cases does not exceed $8 \cdot 10^4$.

## Output

For each test case, output a single number — the sum from the problem statement.

## Example

Input

> 2
> 5
> 2 3 6 12 17
> 8
> 6 12 8 10 15 12 18 16

Output

> 24
> 203

## Note

In the first test case, the values of $f$ are as follows:

-  $i=1$, $j=2$, $k=3$, $f(a_i,a_j,a_k)=f(2,3,6)=\gcd(2,3)=1$; 
-  $i=1$, $j=2$, $k=4$, $f(a_i,a_j,a_k)=f(2,3,12)=\gcd(2,3)=1$; 
-  $i=1$, $j=2$, $k=5$, $f(a_i,a_j,a_k)=f(2,3,17)=\gcd(2,3)=1$; 
-  $i=1$, $j=3$, $k=4$, $f(a_i,a_j,a_k)=f(2,6,12)=\gcd(2,6)=2$; 
-  $i=1$, $j=3$, $k=5$, $f(a_i,a_j,a_k)=f(2,6,17)=\gcd(2,6)=2$; 
-  $i=1$, $j=4$, $k=5$, $f(a_i,a_j,a_k)=f(2,12,17)=\gcd(2,12)=2$; 
-  $i=2$, $j=3$, $k=4$, $f(a_i,a_j,a_k)=f(3,6,12)=\gcd(3,6)=3$; 
-  $i=2$, $j=3$, $k=5$, $f(a_i,a_j,a_k)=f(3,6,17)=\gcd(3,6)=3$; 
-  $i=2$, $j=4$, $k=5$, $f(a_i,a_j,a_k)=f(3,12,17)=\gcd(3,12)=3$; 
-  $i=3$, $j=4$, $k=5$, $f(a_i,a_j,a_k)=f(6,12,17)=\gcd(6,12)=6$.

The sum over all triples is $1+1+1+2+2+2+3+3+3+6=24$.

In the second test case, there are $56$ ways to choose values of $i$, $j$, $k$. The sum over all $f(a_i,a_j,a_k)$ is $203$.

## 题解

**步骤一**

首先可以注意到的是，顺序与结果无关，因此可以首先对序列排序。

排序后，选择的三元组一定是第三个数被丢弃，只考虑前两个数构成的二元组即可。对于二元组 $(a_i,a_j)$，它们的贡献是 $\gcd(a_i,a_j)\times[n-(j+1)]$.

不过即使这样，也需要二重循环 $O(n^2)$，无法满足时间要求。

**步骤二**

上面我们的思路可以表示为：
$$
\sum_{1\leq i<j< n}\gcd(a_i,a_j)\times(n-j-1)
$$
题目给出 $1\leq a_i \leq 10^5$，那么说明 $1\leq\gcd(a_i,a_j)\leq 10^5$，也就是说 $\gcd$ 结果最多只有 $10^5$ 种。那么如果我们能求出来每种 $\gcd$ 结果 $i$ 对应的贡献次数 $c_i$，那么答案就可以表示为：
$$
\sum_{i=1}^{N}i\cdot c_i
$$
对于数 $x$，如果它的因子为 $d_1,d_2,\dots,d_n$. 那么对于任意 $y$，若 $\gcd(x,y)=t$，我们可以确定 $t$ **一定**是 $x$ 的因子，也**一定**是 $y$ 的因子。

也就是说，对于一个数 $x$，它与任意数进行 $\gcd$ 产生的结果最多为它的因子数，为 $\log x$ 级别。考虑整个数列，那么最多需要 $n\log n$ 次计算就能得到结果。

**步骤三**

根据上面的分析，接下来就可以考虑如何计算 $c_i$ 了。

我们依次考虑 $a_j$ 的每个因子 $p_1,p_2,\dots,p_n$. 对于因子 $p$，如果从 $a_1,a_2,\dots,a_{j-1}$ 中有 $k$ 个数**包含**因子 $p$，那么我们选择这些数作为 $a_i$，那么 $a_i,a_j$ 的公因数可以为 $p$. 我们将 $c_p$ 加上 $k\times(n-j-1)$.

可以发现，上面的操作计算的是**公因数**，而不是**最大公因数**。例如 $12,18$ 都公因数之一是 $2$，但显然 $\gcd(12,18)\neq2$. 不过通过简单的处理，就可以让上面计算出的结果转换成最终结果。

如果我们知道公因数为 $t$ 的情况数，我们将这个数目减去**最大**公因数为 $2t,3t,\dots,nt$ 的情况数，最终结果便是最大公因数为 $t$ 的情况数了。这个好似一种 DP 思想，只不过这种是减去而不是加上。

我们从大到小进行处理，就能得到最终的 $c_i$ 了。最终答案为：
$$
\sum_{i=1}^{N}i\cdot c_i
$$

> 注意这里的 $N$ 不是数列长度，而是值域 $N=10^5$.（也可以是数列最大值）

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

constexpr int MAXN = 1e5 + 10;

vector<int> dv[MAXN];

void init()
{
    for (int i = 1; i < MAXN; i++)
        for (int j = i; j < MAXN; j += i)
            dv[j].push_back(i);
}

void solve()
{
    int n;
    cin >> n;
    vector<int> a(n);
    for (int i = 0; i < n; i++)
        cin >> a[i];
    sort(a.begin(), a.end());
    int mx = a.back();
    vector<int> num(mx + 10), cnt(mx + 10);
    for (int i = 0; i < n; i++)
    {
        for (auto &d : dv[a[i]])
        {
            cnt[d] += (n - i - 1) * num[d];
            num[d]++;
        }
    }
    for (int i = mx; i >= 1; i--)
        for (int j = i * 2; j <= mx; j += i)
            cnt[i] -= cnt[j];
    int ans = 0;
	for (int i = 1; i <= mx; i++)
		ans += cnt[i] * i;
    cout << ans << endl;
}

signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    init();
    int t = 1;
    cin >> t;
    while (t--)
        solve();
    return 0;
}
```

