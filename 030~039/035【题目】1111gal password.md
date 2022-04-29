**AtCoder Beginner Contest 242**

C - 1111gal password

https://atcoder.jp/contests/abc242/tasks/abc242_c

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $300$ points

### Problem Statement

Given an integer $N$, find the number of integers $X$ that satisfy all of the following conditions, modulo $998244353$.

- $X$ is an $N$-digit positive integer.
- Let $X_1,X_2,\dots,X_N$ be the digits of $X$ from top to bottom. They satisfy all of the following:
  - $1 \le X_i \le 9$ for all integers $1 \le i \le N$;
  - $|X_i-X_{i+1}| \le 1$ for all integers $1 \le i \le N-1$.

### Constraints

- $N$ is an integer.
- $2 \le N \le 10^6$

------

### Input

Input is given from Standard Input in the following format:

> $N$

### Output

Print the answer as an integer.

------

### Sample Input 1

> 4

### Sample Output 1

> 203

Some of the $4$-digit integers satisfying the conditions are $1111,1234,7878,6545$.

------

### Sample Input 2

> 2

### Sample Output 2

> 25

------

### Sample Input 3

> 1000000

### Sample Output 3

> 248860093

Be sure to find the count modulo $998244353$.

### 我的笔记

用到了动态规划的思想：
$$
\begin{align}
&f(x):=满足题目条件数字x的总数\\
&f(1)=1,f(2)=1,f(3)=1,\cdots,f(9)=1\\
&f(1?)=f(1)+f(2)=2,f(2?)=f(1)+f(2)+f(3)=3,\cdots,f(9?)=f(9)+f(8)=2\\
&f(1??)=f(1?)+f(2?)=5,f(2??)=f(1?)+f(2?)+f(3?)=8,\cdots,f(9??)=f(9?)+f(8?)=5\\
&\vdots
\end{align}
$$
这就是状态转移方程，依据此写一个循环即可。

### 代码

```cpp
#include <bits/stdc++.h>
#define MOD 998244353

using namespace std;
unsigned long long dp[1000010][10]{};

int main(void)
{
    int N;
    cin >> N;
    for (int i = 1; i <= 9; i++)
        dp[1][i] = 1;
    for (int i = 2; i <= N; i++)
    {
        dp[i][1] = dp[i - 1][1] + dp[i - 1][2];
        dp[i][1] %= MOD;
        for (int j = 2; j <= 8; j++)
        {
            dp[i][j] = dp[i - 1][j + 1] + dp[i - 1][j] + dp[i - 1][j - 1];
            dp[i][j] %= MOD;
        }
        dp[i][9] = dp[i - 1][9] + dp[i - 1][8];
        dp[i][9] %= MOD;
    }
    long long ans = 0;
    for (int i = 1; i <= 9; i++)
    {
        ans += dp[N][i];
        ans %= MOD;
    }
    cout << ans << endl;
    return 0;
}
```

