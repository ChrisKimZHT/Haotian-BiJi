**AtCoder Beginner Contest 262**

D - I Hate Non-integer Number

https://atcoder.jp/contests/abc262/tasks/abc262_d

<!--more-->

Time Limit: 2.5 sec / Memory Limit: 1024 MB

Score : $400$ points

### Problem Statement

You are given a sequence of positive integers $A=(a_1,\ldots,a_N)$ of length $N$.
There are $(2^N-1)$ ways to choose one or more terms of $A$. How many of them have an integer-valued average? Find the count modulo $998244353$.

### Constraints

- $1 \leq N \leq 100$
- $1 \leq a_i \leq 10^9$
- All values in input are integers.

### Input

Input is given from Standard Input in the following format:

> $N$
> $a_1$ $\ldots$ $a_N$

### Output

Print the answer.

### Sample Input 1

> 3
> 2 6 2

### Sample Output 1

> 6

For each way to choose terms of $A$, the average is obtained as follows:

- If just $a_1$ is chosen, the average is $\frac{a_1}{1}=\frac{2}{1} = 2$, which is an integer.
- If just $a_2$ is chosen, the average is $\frac{a_2}{1}=\frac{6}{1} = 6$, which is an integer.
- If just $a_3$ is chosen, the average is $\frac{a_3}{1}=\frac{2}{1} = 2$, which is an integer.
- If $a_1$ and $a_2$ are chosen, the average is $\frac{a_1+a_2}{2}=\frac{2+6}{2} = 4$, which is an integer.
- If $a_1$ and $a_3$ are chosen, the average is $\frac{a_1+a_3}{2}=\frac{2+2}{2} = 2$, which is an integer.
- If $a_2$ and $a_3$ are chosen, the average is $\frac{a_2+a_3}{2}=\frac{6+2}{2} = 4$, which is an integer.
- If $a_1$, $a_2$, and $a_3$ are chosen, the average is $\frac{a_1+a_2+a_3}{3}=\frac{2+6+2}{3} = \frac{10}{3}$, which is not an integer.

Therefore, $6$ ways satisfy the condition.

------

### Sample Input 2

> 5
> 5 5 5 5 5

### Sample Output 2

> 31

Regardless of the choice of one or more terms of $A$, the average equals $5$.

### 我的笔记

这个动态规划比较重要的点就是将不同分母的情况分开讨论，如果分母确定，那么只需要知道余数，就可以进行转移。但如果将不同分母的情况混在一起讨论，那就需要储存不同选法的和，而和的范围非常宽广，不可能使用数组储存下来，因此无法实现。

#### 状态表示

$dp[i][j][k]:=$ 讨论前 $i$ 个数，选择 $j$ 个数时，和的余数为 $k$ 的情况。

#### 状态转移

不选 $a[j]$ 时：$dp[j+1][k][l]\text{+=}dp[j][k][l]$

选 $a[j]$ 时：$dp[j+1][k+1][(l+a[k])\%i]\text{+=}dp[j][k][l]$

### 代码

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MOD = 998244353;
const int MAXN = 110;
int N, a[MAXN];
int ans = 0;

int main()
{
    cin >> N;
    for (int i = 0; i < N; i++)
        cin >> a[i];
    for (int i = 1; i <= N; i++) // 分母为i
    {
        vector dp(N + 1, vector(i + 1, vector<int>(i, 0)));
        dp[0][0][0] = 1;
        for (int j = 0; j < N; j++) // 前j+1个数
        {
            for (int k = 0; k <= i; k++) // 选k个数
            {
                for (int l = 0; l < i; l++) // 除i余l
                {
                    dp[j + 1][k][l] += dp[j][k][l];
                    dp[j + 1][k][l] %= MOD;
                    if (k != i)
                    {
                        dp[j + 1][k + 1][(l + a[j]) % i] += dp[j][k][l];
                        dp[j + 1][k + 1][(l + a[j]) % i] %= MOD;
                    }
                }
            }
        }
        ans += dp[N][i][0];
        ans %= MOD;
    }
    cout << ans << endl;
    return 0;
}
```

