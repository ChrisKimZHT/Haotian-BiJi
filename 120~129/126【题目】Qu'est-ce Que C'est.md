**2023牛客暑期多校训练营4**

J - Qu'est-ce Que C'est?

https://ac.nowcoder.com/acm/contest/57358/J

<!--more-->

## 题意

给定长度为 $n$ 的数列 $\{a\}_{i=1}^n$, 要求每个数都在 $[-m, m]$ 范围, 且任意长度大于等于 2 的区间和都大于等于 0 问方案数。 $1 \leq n, m \leq 5 \times 10^3$ 。

## 题解

首先，要注意到如果现在数列已经填了 $x$ 个数 $a_1,a_2,\dots,a_x$，记此时从 $x$ 位开始的后缀和最小值为：
$$
t=\min\left\{\sum_{i=1}^{x}a_i,\sum_{i=2}^{x}a_i,\dots,\sum_{i=x}^{x}a_i\right\}
$$
那么，在填下一个数 $a_{x+1}$ 时，$a_{x+1}$ 要满足：
$$
-t\leq a_{x+1}\leq m
$$
下面简单解释一下为什么。

首先是为什么只关心后缀和。因为对于一个长为 $x$ 的数列，往后再加 $1$ 位，此时多出的子区间的和其实就是 $x$ 个后缀和。所以如果能保证 $x$ 长度的数列合法，那么往后再加 $1$ 位，如果多出的后缀和合法，则 $x+1$ 长度的数列也合法。

然后是为什么只关心最小值。因为如果保证最小的后缀和区间 $\geq0$，那其他区间就一定都 $\geq0$ 了。

最后一点为什么限制是这样。因为当 $t<0$ 时，为了保证后缀和 $\geq0$，那么必须填一个 $\geq -t$ 的数把后缀和的大小抬到 $0$ 以上，要不然就不成立了。而当 $t\geq0$ 时，不能填 $<-t$ 的数，因为这样会把一个后缀和压到 $0$ 以下。

现在，动态规划的思路大概就有了，首先是状态表示：

- $dp_{i,j}:=$ 数列已经确定了 $i$ 位，且后缀和最小值为 $j$ 的情况下的方案总数。

接下来就是转移方式：

- 如果 $j\geq0$：$dp_{i,j}$ 转移到 $dp_{i,a_{x+1}}$
- 如果 $j<0$：$dp_{i,j}$ 转移到 $dp_{i,j+a_{x+1}}$

转移时跟 $j$ 大小有关的原因是，如果 $j\geq0$，那么新来一个 $a_{x+1}$，它这一位肯定是最小的，即：
$$
a_{x+1}=\min\left\{a_{x+1}+\sum_{i=1}^{x}a_i,a_{x+1}+\sum_{i=2}^{x}a_i,\dots,a_{x+1}+\sum_{i=x}^{x}a_i,a_{x+1}\right\}
$$
而如果 $j<0$，那么最小的仍然是原来的那一位，只不过它的大小会被加上 $a_{x+1}$，即：
$$
t+a_{x+1}=\min\left\{a_{x+1}+\sum_{i=1}^{x}a_i,a_{x+1}+\sum_{i=2}^{x}a_i,\dots,a_{x+1}+\sum_{i=x}^{x}a_i,a_{x+1}\right\}
$$
根据上述分析，就可以写出一个朴素的三重循环 DP 了：

```cpp
for (int i = m; i >= -m; i--)
    dp[1][i + m] = 1;
for (int i = 2; i <= n; i++) {
    for (int j = m; j >= -m; j--) {
        if (j >= 0) {
            for (int k = m; k >= j - m; k--) {
                dp[i][j + m] = (dp[i][j + m] + dp[i - 1][k + m]) % MOD;
            }
        } else {
            for (int k = m; k >= -j; k--) {
                dp[i][j + m] = (dp[i][j + m] + dp[i - 1][k + m]) % MOD;
            }
        }
    }
}
```

只不过这样时间复杂度为 $O(n^3)$，无法通过。不过能发现，第三重循环加的区间都是连续的，因此可以用前缀和优化，把 $O(n)$ 复杂度的区间和优化成 $O(1)$，这样时间复杂度优化为 $O(n^2)$ 即可通过了。

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

constexpr int MOD = 998244353;
constexpr int MAXN = 5050;
int n, m;
int dp[2 * MAXN], sum[2 * MAXN];

void solve()
{
    cin >> n >> m;
    for (int i = m; i >= -m; i--)
    {
        dp[i + m] = 1;
        sum[i + m] = sum[i + 1 + m] + 1;
    }
    for (int i = 2; i <= n; i++)
    {
        for (int j = m; j >= -m; j--)
        {
            if (j >= 0)
                dp[j + m] = sum[j];
            else
                dp[j + m] = sum[m - j];
        }
        for (int j = m; j >= -m; j--)
            sum[j + m] = (sum[j + m + 1] + dp[j + m]) % MOD;
        // for (int j = m; j >= -m; j--)
        //     cout << sum[j + m] - sum[j + m + 1] << " \n"[j == -m];
    }
    cout << sum[0] << endl;
}

signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    int t = 1;
    // cin >> t;
    while (t--)
        solve();
    return 0;
}
```



