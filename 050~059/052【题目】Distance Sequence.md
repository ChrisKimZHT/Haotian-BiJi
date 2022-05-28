**NOMURA Programming Contest 2022（AtCoder Beginner Contest 253）**

E - Distance Sequence

https://atcoder.jp/contests/abc253/tasks/abc253_e

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $500$ points

### Problem Statement

How many integer sequences $A=(A_1,\ldots,A_N)$ of length $N$ satisfy all the conditions below?

- $1\le A_i \le M$ $(1 \le i \le N)$
- $|A_i - A_{i+1}| \geq K$ $(1 \le i \le N - 1)$

Since the count can be enormous, find it modulo $998244353$.

### Constraints

- $2 \leq N \leq 1000$
- $1 \leq M \leq 5000$
- $0 \leq K \leq M-1$
- All values in input are integers.

------

### Input

Input is given from Standard Input in the following format:

> $N$ $M$ $K$

### Output

Print the count modulo $998244353$.

------

### Sample Input 1

> 2 3 1

### Sample Output 1

> 6

The following $6$ sequences satisfy the conditions.

- $(1,2)$
- $(1,3)$
- $(2,1)$
- $(2,3)$
- $(3,1)$
- $(3,2)$

------

### Sample Input 2

> 3 3 2

### Sample Output 2

> 2

The following $2$ sequences satisfy the conditions.

- $(1,3,1)$
- $(3,1,3)$

------

### Sample Input 3

> 100 1000 500

### Sample Output 3

> 657064711

Print the count modulo $998244353$.

------

### 我的笔记

很简单的一道 DP，令 $dp[i][j]:=$ 只考虑前 $i$ 个数，且第 $i$ 个数大小为 $j$ 的情况数量。转移方程：
$$
dp[i+1][j]=(dp[i][1]+\dots+dp[i][j-K])+(dp[i][j+K]+\dots+dp[i][M])
$$
暴力法时间复杂度为：$O(N\cdot M^2)$，可以用前缀和简单优化为 $O(N\cdot M)$

导致我 WA 半天写不出来的时本题比较隐蔽的坑： $K=0$ 时的特判。

若 $K=0$，$dp[i][j-K]=dp[i][j+K]$，导致这一项重复了，解决方法就是直接特判，这种情况等于 $dp[i][1]+\dots+dp[i][M]$

### 代码

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN = 1010, MAXM = 5010, MOD = 998244353;
int N, M, K;
long long dp[MAXN][MAXM];
long long ps[MAXM];

int main()
{
    cin >> N >> M >> K;
    for (int i = 1; i <= M; i++)
        dp[1][i] = 1;
    for (int i = 2; i <= N; i++)
    {
        memset(ps, 0, sizeof(ps));
        for (int j = 1; j <= M; j++)
        {
            ps[j] = ps[j - 1] + dp[i - 1][j];
            ps[j] %= MOD;
        }
        for (int j = 1; j <= M; j++)
        {
            if (K == 0)
            {
                dp[i][j] = ps[M];
                continue;
            }
            if (j - K >= 1)
            {
                dp[i][j] += MOD + ps[j - K] - ps[0];
                dp[i][j] %= MOD;
            }
            if (j + K <= M)
            {
                dp[i][j] += MOD + ps[M] - ps[j + K - 1];
                dp[i][j] %= MOD;
            }
        }
    }
    long long ans = 0;
    for (int i = 1; i <= M; i++)
    {
        ans += dp[N][i];
        ans %= MOD;
    }
    cout << ans << endl;
    return 0;
}
```

