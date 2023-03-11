**AtCoder Beginner Contest 293**

E - Geometric Progression

https://atcoder.jp/contests/abc293/tasks/abc293_e

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $500$ points

## Problem Statement

Given integers $A$, $X$, and $M$, find $\displaystyle \sum_{i = 0}^{X-1} A^i$, modulo $M$.

## Constraints

- $1 \leq A, M \leq 10^9$
- $1 \leq X \leq 10^{12}$
- All values in the input are integers.
## Input

The input is given from Standard Input in the following format:

> $A$ $X$ $M$

## Output

Print the answer.

## Sample Input 1

> 3 4 7

## Sample Output 1

> 5

$3^0 + 3^1 + 3^2 + 3^3 = 40$, which equals $5$ modulo $7$, so $5$ should be printed.

## Sample Input 2

> 8 10 9

## Sample Output 2

> 0

## Sample Input 3

> 1000000000 1000000000000 998244353

## Sample Output 3

> 919667211

## 题解

#### 典型错解——使用等比数列求和公式

我们知道等比数列求和公式：
$$
S_n=a_1\cdot\frac{q^n-1}{q-1}
$$
那么易得：
$$
\displaystyle \sum_{i = 0}^{X-1} A^i=\frac{A^X-1}{A-1}
$$
其中， $A^X$ 通过快速幂得到，与 $A-1$ 做除法，在取模的时候转化为乘 $A-1$ 的逆元，然后就便是典型错解。错误的原因是：并不是所有的数都存在乘法逆元。

要求 $a$ 对于模 $p$ 的乘法逆元 $b$，即 $a\cdot b\equiv1\pmod p$：

- 当 $a$ 与 $p$ 互质
  - 可用扩展欧几里得算法得出：$\text{exgcd}(a, p, b, t)=1$
  - 特别的，当 $p$ 为质数，可用费马小定理得出：$a^{p-2}$
- 当 $a$ 与 $p$ 不互质
  - $a$ 对于模 $p$ **不存在**乘法逆元

由题目可知，没有对 $A$ 和 $M$ 的关系做限制，因此 $A-1$ 可能没有关于 $M$ 的乘法逆元。

#### 正确解法——矩阵加速递推

**构造递推**

令 $a_n=\displaystyle \sum_{i = 0}^{n-1} A^i$，那么可构造递推式：
$$
a_{n+1}=Aa_n+1,\ 其中\ a_0=0
$$
若直接进行递推，时间复杂度 $O(n)$，由 $1 \leq X \leq 10^{12}$ 可知无法满足时限。

**加速递推**

将上述递推式改写为矩阵形式：
$$
\begin{bmatrix}
a_{n+1}\\
1
\end{bmatrix}
=
\begin{bmatrix}
A & 1\\
0 & 1
\end{bmatrix}
\begin{bmatrix}
a_{n}\\
1
\end{bmatrix}
,\ 其中\ a_0=0
$$
那么我们的递推式就能变成矩阵次幂形式了：
$$
\begin{bmatrix}
a_{n}\\
1
\end{bmatrix}
=
\begin{bmatrix}
A & 1\\
0 & 1
\end{bmatrix}^n
\begin{bmatrix}
0\\
1
\end{bmatrix}
$$
同时，矩阵的次幂也是可以使用快速幂的，因为矩阵乘法也满足 $M^{2n}=(M^2)^n$，那么我们直接用快速幂计算出：
$$
\begin{bmatrix}
A & 1\\
0 & 1
\end{bmatrix}^X
=
\begin{bmatrix}
p & q\\
m & n
\end{bmatrix}
$$
题目的答案便是 $q$.

## 代码

```cpp
#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

vector<vector<ll>> mat_mul_2d(vector<vector<ll>> a, vector<vector<ll>> b, ll p)
{
    vector<vector<ll>> ans(2, vector<ll>(2));
    ans[0][0] = ((a[0][0] * b[0][0]) % p + (a[0][1] * b[1][0]) % p) % p;
    ans[0][1] = ((a[0][0] * b[0][1]) % p + (a[0][1] * b[1][1]) % p) % p;
    ans[1][0] = ((a[1][0] * b[0][0]) % p + (a[1][1] * b[1][0]) % p) % p;
    ans[1][1] = ((a[1][0] * b[0][1]) % p + (a[1][1] * b[1][1]) % p) % p;
    return ans;
}

vector<vector<ll>> mat_fastpow_2d(vector<vector<ll>> a, ll n, ll p)
{
    vector<vector<ll>> ans(2, vector<ll>(2));
    ans[0][0] = ans[1][1] = 1;
    while (n)
    {
        if (n % 2)
            ans = mat_mul_2d(ans, a, p);
        a = mat_mul_2d(a, a, p);
        n >>= 1;
    }
    return ans;
}

void solve()
{
    ll A, X, M;
    cin >> A >> X >> M;
    vector<vector<ll>> mat = {{A, 1}, {0, 1}};
    vector<vector<ll>> ans = mat_fastpow_2d(mat, X, M);
    cout << ans[0][1] << endl;
}

signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    solve();
    return 0;
}
```

