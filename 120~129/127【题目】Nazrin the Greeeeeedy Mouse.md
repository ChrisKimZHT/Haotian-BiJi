**2023牛客暑期多校训练营5**

H - Nazrin the Greeeeeedy Mouse

https://ac.nowcoder.com/acm/contest/57359/H

<!--more-->

## 题意

有 $n$ 个物品，第 $i$ 个物品的体积是 $a_i$，价值是 $b_i$，有 $m$ 个背包，第 $i$ 个背包的容积是 $s_i$ 且 $s_{i-1}\leq s_i$.

物品需要从 $1$ 到 $n$ 顺序取，即取了第 $i$ 个物品后，$<i$ 的没有取的物品不能再取。

背包需要从 $1$ 到 $m$ 顺序用，即开始用第 $i$ 个背包后，$<i$ 的背包就不能再次使用了。

$1\leq n\leq 200,\;1\leq m\leq 10^5$

$1\leq a_i\leq 200,\; 1\leq b_i\leq 10^5,\; 1\leq s_i\leq 200$

## 题解

#### 当 $m>n$ 时，只考虑最大的 $n$ 个背包即可。

因为对于 $n$ 个物品，最多用掉 $n$ 个背包（每个背包装 $1$ 个），又因为背包大小单调不减 $s_{i-1}\leq s_i$，那么如果 $m>n$，取最靠后的最大的 $n$ 个背包一定是最优的方案。

#### 预处理每个区间的 0/1 背包问题

对于每个区间 $[i,j]$，用 0/1 背包模型算出背包大小为 $k$ 时能取到的最大的价值。实际上就是固定起点为 $i$，跑一遍 $i\sim n$ 的 0/1 背包问题。代码中的 $f_{i,j,k}:=$ 起点为 $i$ 终点为 $j$ 的区间，背包大小为 $k$ 时能取到的最大的价值。

预处理的时间复杂度为：$O(n^2\max\{s_i\})$

#### 计算最终结果

$dp_{i,k}:=$ 第 $1\sim i$ 个背包取区间 $[1,k]$ 的物品，能取到的最大价值。

转移方式为：$dp_{i,k}=dp_{i-1,j}+f_{j,k,w_i}$

计算结果的时间复杂度为：$O(n^3)$

> 如果没有发现可只考虑 $n$ 个最大的背包，时间复杂度是 $O(n^2m)$，肯定是过不去的。

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
// #define int long long

using namespace std;

constexpr int MAXN = 210;
int f[MAXN][MAXN][MAXN];
int a[MAXN], b[MAXN], s[MAXN];
int dp[MAXN][2];

void solve()
{
    int n, m;
    cin >> n >> m;
    for (int i = 1; i <= n; i++)
        cin >> a[i] >> b[i];
    for (int i = 1; i <= m; i++)
        cin >> s[max(1, min(0, n - m) + i)];
    m = min(m, n);
    for (int i = 1; i <= n; i++)
    {
        for (int j = i; j <= n; j++)
        {
            for (int k = 0; k <= 200; k++)
            {
                f[i][j][k] = f[i][j - 1][k];
                if (k - a[j] >= 0)
                    f[i][j][k] = max(f[i][j][k], f[i][j - 1][k - a[j]] + b[j]);
            }
        }
    }
    int p = 0;
    for (int i = 1; i <= m; i++)
    {
        for (int j = 0; j <= 200; j++)
            dp[j][p] = 0;
        for (int j = 0; j <= n; j++)
            for (int k = j + 1; k <= n; k++)
                dp[k][p] = max(dp[k][p], dp[j][p ^ 1] + f[j + 1][k][s[i]]);
        p ^= 1;
    }
    cout << dp[n][p ^ 1] << endl;
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