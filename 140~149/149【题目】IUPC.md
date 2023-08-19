**“范式杯”2023牛客暑期多校训练营10**

F - IUPC

https://ac.nowcoder.com/acm/contest/57364/F

<!--more-->

## 题意

一场时长为 $t$ 分钟的比赛有 $n$ 道题，每个题有最早过题时间的约束，并且一分钟内至多只能交一题，任意 $k$ 分钟内只能至多交 $m$ 题，问比赛 AK 的方案数。 $1\leq t\leq 300$，$1\leq n \leq 13$ ，$1\leq m\leq k\leq 10$。

## 题解

使用状态压缩动态规划思想。

#### 状态表示

$dp_{i,j,S}:=$ 在 $1\sim i$ 分钟，提交了 $j$ 题，且最后 $k$ 分钟的提交情况为 $S$ 的方案数。

其中提交情况 $S$ 是一个 $k$ 位二进制数：${\overbrace{00\dots00}^{k\ 位}}_2$，其中从左到右第 $p$ 位代表第 $i-k+p$ 时刻是否过题，如果过题则为 $1$，反之为 $0$.

#### 状态计算

首先需要预处理每个 $i$ 时刻有答案可提交的题目数 $pa_i$，用一个前缀和处理即可。那么 $i$ 时刻可以提交的题目有 $pa_i-j$ 种，即有答案可提交的数目减去之前已经提交过的数目。

为方便表示，定义 $\mathrm{LeftShift}(S):=$ $S$ 左移一位，且去除超出 $k$ 位的部分（保证此时仍然为 $k$ 位），定义 $\mathrm{PopCount}(S):=$ $S$ 的二进制表示中 $1$ 的数目。

- 如果第 $i$ 时刻不提交，转移到的情况 $C=\mathrm{LeftShift}(S)$，情况数直接原封不动加进去：

$$
dp_{i,j,C}=dp_{i,j,C}+dp_{i-1,j,S}
$$

- 如果第 $i$ 时刻提交，转移到的情况 $C=\mathrm{LeftShift}(S)+1$，需要分类讨论：
  - 如果 $\mathrm{PopCount}(C)>m$，则 $k$ 分钟内交题数超限，这种转移不成立。
  - 如果 $\mathrm{PopCount}(C)\leq m$，则可以正常交题，可提交的题目种类有 $pa_i-j$ 种，转移方式：

$$
dp_{i,j+1,C}=dp_{i,j+1,C}+(pa_i-j)\cdot dp_{i-1,j,S}
$$

最后答案就是：
$$
\sum_{S=0}^{2^k-1}dp_{t,n,S}
$$

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

constexpr int MAXN = 310, MAXM = 15, MOD = 1e9 + 7;
int n, t, k, m;
int a[MAXN], pa[MAXN];
int dp[MAXN][MAXM][1 << MAXM];

void solve()
{
    cin >> n >> t >> k >> m;
    for (int i = 1; i <= n; i++)
    {
        int t;
        cin >> t;
        a[t]++;
    }
    for (int i = 1; i <= t; i++)
        pa[i] = pa[i - 1] + a[i];
    int mask = (1 << k) - 1;
    dp[0][0][0] = 1;
    for (int i = 1; i <= t; i++)
    {
        for (int j = 0; j <= pa[i]; j++)
        {
            for (int p = 0; p < (1 << k); p++)
            {
                int cur = (p << 1) & mask;
                dp[i][j][cur] += dp[i - 1][j][p];
                dp[i][j][cur] %= MOD;
            }
        }
        for (int j = 0; j <= pa[i]; j++)
        {
            for (int p = 0; p < (1 << k); p++)
            {
                int cur = ((p << 1) & mask) | 1;
                if (__builtin_popcount(cur) > m)
                    continue;
                dp[i][j + 1][cur] += dp[i - 1][j][p] * (pa[i] - j) % MOD;
                dp[i][j + 1][cur] %= MOD;
            }
        }
    }
    int ans = 0;
    for (int i = 0; i < (1 << k); i++)
        ans = (ans + dp[t][n][i]) % MOD;
    cout << ans << endl;
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

