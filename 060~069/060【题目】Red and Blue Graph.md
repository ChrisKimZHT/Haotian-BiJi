**AtCoder Beginner Contest 262**

E - Red and Blue Graph

https://atcoder.jp/contests/abc262/tasks/abc262_e

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $500$ points

### Problem Statement

You are given a simple undirected graph with $N$ vertices and $M$ edges. The vertices are numbered $1, \dots, N$, and the $i$-th $(1 \leq i \leq M)$ edge connects Vertices $U_i$ and $V_i$.

There are $2^N$ ways to paint each vertex red or blue. Find the number, modulo $998244353$, of such ways that satisfy all of the following conditions:

- There are exactly $K$ vertices painted red.
- There is an even number of edges connecting vertices painted in different colors.

### Constraints

- $2 \leq N \leq 2 \times 10^5$
- $1 \leq M \leq 2 \times 10^5$
- $0 \leq K \leq N$
- $1 \leq U_i \lt V_i \leq N \, (1 \leq i \leq M)$
- $(U_i, V_i) \neq (U_j, V_j) \, (i \neq j)$
- All values in input are integers.

------

### Input

Input is given from Standard Input in the following format:

> $N$ $M$ $K$
> $U_1$ $V_1$
> $\vdots$
> $U_M$ $V_M$

### Output

Print the answer.

------

### Sample Input 1

> 4 4 2
> 1 2
> 1 3
> 2 3
> 3 4

### Sample Output 1

> 2

The following two ways satisfy the conditions.

- Paint Vertices $1$ and $2$ red and Vertices $3$ and $4$ blue.
- Paint Vertices $3$ and $4$ red and Vertices $1$ and $2$ blue.

In either of the ways above, the $2$-nd and $3$-rd edges connect vertices painted in different colors.

------

### Sample Input 2

> 10 10 3
> 1 2
> 2 4
> 1 5
> 3 6
> 3 9
> 4 10
> 7 8
> 9 10
> 5 9
> 3 4

### Sample Output 2

> 64

------

### 我的笔记

需要满足的要求是，有 $K$ 个红点，且连接不同颜色点的边数为偶数，这样思考起来比较困难，需要进行转换。

我们可以思考红点的度数，所有红点度数之和 $D_r$ 的组成有两种，一种是连接不同颜色点的边 $E_{rb}$ ，一种是连接两个红点的边 $E_{rr}$，用表达式表示即为：$D_r=E_{rb}+2E_{rr}$

我们需要满足连接不同颜色点的边数为偶数，即 $E_{rb}$ 为偶数，通过上式即可知我们需要满足 $D_r$ 为偶数。如此我们就将原问题转化为简单的顶点的度数问题。

我们首先读入边的情况，计算出每个点的度数，并计算出奇度、偶度顶点的个数，我们只需要这两个数据。

由于我们要求 $D_r$ 为偶数，那么红偶度顶点没有限制，而红奇度顶点的个数必须为偶数，通过组合公式计算即可。

另外，由于组合公式 $C_n^m=\frac{n!}{m!(n-m)!}$，取模时需要用到模逆元，即变为 $C_n^m=n!\cdot inv(m!)\cdot inv((n-m)!)$，可以预先计算出阶乘加快速度。

### 代码

```cpp
#include <bits/stdc++.h>
#define int long long

using namespace std;

const int MOD = 998244353;
const int MAXN = 2e5 + 10;
int fact[MAXN], invf[MAXN];

int fast_pow(int a, int b)
{
    a %= MOD;
    int ans = 1;
    while (b)
    {
        if (b % 2 == 1)
            ans = ans * a % MOD;
        a = a * a % MOD;
        b >>= 1;
    }
    return ans;
}

void fact_init()
{
    fact[0] = 1;
    for (int i = 1; i <= MAXN; i++)
        fact[i] = fact[i - 1] * i % MOD;
    for (int i = 0; i <= MAXN; i++)
        invf[i] = fast_pow(fact[i], MOD - 2);
}

int comb(int n, int m)
{
    return ((fact[n] * invf[m]) % MOD * invf[n - m]) % MOD;
}

signed main()
{
    fact_init();
    int N, M, K;
    cin >> N >> M >> K;
    vector<int> d(N);
    for (int i = 0; i < M; i++)
    {
        int U, V;
        cin >> U >> V;
        d[U - 1]++;
        d[V - 1]++;
    }
    int odd = 0;
    for (auto x : d)
        if (x % 2)
            odd++;
    int even = N - odd;
    long long ans = 0;
    for (int selected_odd = 0; selected_odd <= K; selected_odd += 2)
    {
        int selected_even = K - selected_odd;
        if (selected_odd <= odd && selected_even <= even)
        {
            ans += comb(odd, selected_odd) * comb(even, selected_even);
            ans %= MOD;
        }
    }
    cout << ans << endl;
    return 0;
}
```

