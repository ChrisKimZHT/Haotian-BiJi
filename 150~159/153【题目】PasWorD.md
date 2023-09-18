**The 2023 ICPC Asia Regionals Online Contest (1)**

I - Pa?sWorD

https://pintia.cn/market/item/1703381331863785472

<!--more-->

Time Limit: 1000ms

Memory Limit: 32MB

## Problem

You need to log into a mysterious system, but you realize you've forgotten your password. The system does not support resetting password, so the only way to log in is to keep trying.

Fortunately, you still remember some information about the password:

Firstly, you are sure that the length of the password is $n$ .

Then the information you remember can be described by a string *s* of length $n$.

let $s_i$ represent the $i$-th character of $s$ :

- if $s_i$ is an uppercase letter or a digital character, then the *i*-th character of the password must be $s_i$;
- if $s_i$ is an lowercase letter, then the $i$-th character of the password might be $s_i$ or the uppercase form of $s_i$;
- if $s_i$ is a question mark `?` , then the $i$-th character of the password might be any uppercase letters, lowercase letters, and digital characters.

It's guaranteed that the string $s$ only contains uppercase letters, lowercase letters, digital characters, and question marks `?`.
In addition, the system also has several requirements for passwords:

- At least one uppercase letter appears in the password;
- At least one lowercase letter appears in the password;
- At least one digital character appears in the password;
- Any two adjacent characters in the password cannot be the same.

Now you want to know, how many possible passwords are there? A possible password should fits both your memory and the system's requirements, and it’s guaranteed that there exists at least one possible password.

You know that this answer will be very large, so you just need to calculate the result modulo $998244353$ .

**Please note the unusual memory limit.**

## Input

The first line contains a single integer $n$ $(3≤n≤10^5)$ , representing the length of the password .

The second line contains a string $s$ with length $n$. It's guaranteed that the string $s$ only contains uppercase letters, lowercase letters, digital characters, and question marks `?`.

It’s guaranteed that there exists at least one possible password.

## Output

output a single line containing a single integer, representing the answer modulo $998244353$.

## Sample

Input

> 4
> a?0B

Output

> 86

## 题解

使用状态压缩 DP 思想。

#### 状态表示

$dp(i,j,k):=$ 考虑 $1\sim i$ 位，第 $i$ 位为字符 $j$，且到此构成的字符串状态为 $k$ 的状态数目。

为了让字符能够用连续整型表示，将字符映射到整型，记这个映射为 $mp(\cdot)$：

- 大写 A ~ Z: $[0,26)$
- 小写 a ~ z: $[26,52)$
- 数字 0 ~ 9: $[52,62)$

字符串状态 $k$ 看作一个三位二进制数 $[000_2,111_2)$，具体含义如下：

| #    | 是否含有大写 | 是否含有小写 | 是否含有数字 |
| ---- | ------------ | ------------ | ------------ |
| 0    | 0            | 0            | 0            |
| 1    | 0            | 0            | 1            |
| 2    | 0            | 1            | 0            |
| 3    | 0            | 1            | 1            |
| 4    | 1            | 0            | 0            |
| 5    | 1            | 0            | 1            |
| 6    | 1            | 1            | 0            |
| 7    | 1            | 1            | 1            |

#### 状态计算

记输入字符串为 $s$.

- 如果 $s_i$ 为大写字符，对于最高位为 $1$ 的 $k$：

$$
dp(i,mp(s_i),k)=\sum_{j=0,j\neq mp(s_i)}^{61}dp(i-1,j,k)+\sum_{j=0,j\neq mp(s_i)}^{61}dp(i-1,j,k\oplus 2^2)
$$

- 如果 $s_i$ 为数字，对于最低位为 $1$ 的 $k$：

$$
dp(i,mp(s_i),k)=\sum_{j=0,j\neq mp(s_i)}^{61}dp(i-1,j,k)+\sum_{j=0,j\neq mp(s_i)}^{61}dp(i-1,j,k\oplus 2^0)
$$

- 如果 $s_i$ 为小写字符，对于中间位为 $1$ 的 $k$：

$$
dp(i,mp(s_i),k)=\sum_{j=0,j\neq mp(s_i)}^{61}dp(i-1,j,k)+\sum_{j=0,j\neq mp(s_i)}^{61}dp(i-1,j,k\oplus 2^1)
$$

- 如果 $s_i$ 为小写字符，记 $t_i$ 为 $s_i$ 对应的大写字符，对于最高位为 $1$ 的 $k$：

$$
dp(i,mp(t_i),k)=\sum_{j=0,j\neq mp(t_i)}^{61}dp(i-1,j,k)+\sum_{j=0,j\neq mp(t_i)}^{61}dp(i-1,j,k\oplus 2^2)
$$

- 如果 $s_i$ 为问号 $?$，则对于任意 $j,k$：

$$
\begin{matrix}
\text{if}\;k\;的最高位为\;1\;&dp(i,j,k)\leftarrow dp(i,j,k)+\sum_{l=26,l\neq j}^{51} dp(i-1,l,k)+\sum_{l=26,l\neq j}^{51} dp(i-1,l,k\oplus2^2)\\
\text{if}\;k\;的中间位为\;1\;&dp(i,j,k)\leftarrow dp(i,j,k)+\sum_{l=0,l\neq j}^{25} dp(i-1,l,k)+\sum_{l=0,l\neq j}^{25} dp(i-1,l,k\oplus2^1)\\
\text{if}\;k\;的最低位为\;1\;&dp(i,j,k)\leftarrow dp(i,j,k)+\sum_{l=52,l\neq j}^{61} dp(i-1,l,k)+\sum_{l=52,l\neq j}^{61} dp(i-1,l,k\oplus2^0)\\
\end{matrix}
$$

- 对于其他情况，不进行转移。

#### 初始化

- 若 $s_0$ 为大写字符：$dp(0,mp(s_0),4)=1$
- 若 $s_0$ 为小写字符，其对应的大写字符为 $t_i$：$dp(0,mp(t_0),4)=dp(0,mp(s_0),2)=1$
- 若 $s_0$ 为数字：$dp(0,mp(s_0),1)=1$
- 若 $s_0$ 为问号 $?$：$dp(0,j,k)=1$，其中 $j\in[0,62)$，$k$ 是对应 $j$ 的状态

#### 空间优化

上面所诉算法的空间复杂度为 $O(n\cdot 62\cdot8)\approx O(500n)$

最大情况 dp 需要储存 $5\cdot10^7$ 个整型，如果使用 64 位整型，则至少需要约 400MB 内存，不符合题目要求的 32MB.

因此，选择滚动数组进行储存，这样只需要 $2\cdot62\cdot8\approx1000$ 个整型就能存下了，符合空间限制。

#### 时间优化

上面所诉算法最好为 $O(62n)$，但是能用问号卡到最坏复杂度 $O(62^2n)$，如果题目输入一个长 $10^5$ 的问号字符串，那么将会导致超时。

为了优化问号情况的计算速度，预先求和：
$$
sum(i,k)=\sum_{j=0}^{61}dp(i,j,k)
$$
那么计算问号时，无需再每次重复求和，直接取值就行，可以优化掉一个循环。

优化后，时间复杂度稳定为 $O(62n)$.

#### 神秘问题

本题不要尝试使用 map/unordered_map 储存字符映射，因为每次转移都会查询字符映射，最多查询约 $10^6$ 次，若使用 STL map 会导致超时。

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

constexpr int MOD = 998244353;

int dp[2][62][8];
int sum[8];
/*  #   upper lower  num
 *  0     0     0     0
 *  1     0     0     1
 *  2     0     1     0
 *  3     0     1     1
 *  4     1     0     0
 *  5     1     0     1
 *  6     1     1     0
 *  7     1     1     1
 */
int mp[128];

void init()
{
    for (int i = 0; i < 26; i++)
    {
        mp['a' + i] = i;
        mp['A' + i] = i + 26;
    }
    for (int i = 0; i < 10; i++)
    {
        mp['0' + i] = i + 52;
    }
}

void solve()
{
    int n;
    cin >> n;
    string s;
    cin >> s;
    // init dp
    char &frt = s[0];
    if (isupper(frt))
    {
        dp[0][mp[frt]][4] = 1;
    }
    else if (islower(frt))
    {
        dp[0][mp[frt]][2] = 1;
        dp[0][mp[toupper(frt)]][4] = 1;
    }
    else if (isdigit(frt))
    {
        dp[0][mp[frt]][1] = 1;
    }
    else // if (frt == '?')
    {
        for (int i = 0; i < 26; i++)
            dp[0][i][2] = 1;
        for (int i = 26; i < 52; i++)
            dp[0][i][4] = 1;
        for (int i = 52; i < 62; i++)
            dp[0][i][1] = 1;
    }
    for (int i = 0; i < 8; i++)
    {
        sum[i] = 0;
        for (int j = 0; j < 62; j++)
        {
            sum[i] += dp[0][j][i];
            sum[i] %= MOD;
        }
    }
    int op = 1;
    for (int i = 2; i <= n; i++, op ^= 1)
    {
        char &now = s[i - 1];
        for (int j = 0; j < 62; j++)
        {
            for (int k = 0; k < 8; k++)
            {
                dp[op][j][k] = 0;
            }
        }
        if (isupper(now))
        {
            for (int j = 0; j < 62; j++)
            {
                if (j == mp[now])
                    continue;
                for (int k = 0; k < 8; k++)
                {
                    if (k >> 2 & 1) // 1**
                    {
                        dp[op][mp[now]][k] += dp[op ^ 1][j][k];
                        dp[op][mp[now]][k] += dp[op ^ 1][j][k ^ (1 << 2)];
                        dp[op][mp[now]][k] %= MOD;
                    }
                }
            }
        }
        else if (isdigit(now))
        {
            for (int j = 0; j < 62; j++)
            {
                if (j == mp[now])
                    continue;
                for (int k = 0; k < 8; k++)
                {
                    if (k >> 0 & 1) // **1
                    {
                        dp[op][mp[now]][k] += dp[op ^ 1][j][k];
                        dp[op][mp[now]][k] += dp[op ^ 1][j][k ^ (1 << 0)];
                        dp[op][mp[now]][k] %= MOD;
                    }
                }
            }
        }
        else if (islower(now))
        {
            for (int j = 0; j < 62; j++)
            {
                if (j == mp[now])
                    continue;
                for (int k = 0; k < 8; k++)
                {
                    if (k >> 1 & 1) // *1*
                    {
                        dp[op][mp[now]][k] += dp[op ^ 1][j][k];
                        dp[op][mp[now]][k] += dp[op ^ 1][j][k ^ (1 << 1)];
                        dp[op][mp[now]][k] %= MOD;
                    }
                }
            }
            for (int j = 0; j < 62; j++)
            {
                if (j == mp[toupper(now)])
                    continue;
                for (int k = 0; k < 8; k++)
                {
                    if (k >> 2 & 1) // 1**
                    {
                        dp[op][mp[toupper(now)]][k] += dp[op ^ 1][j][k];
                        dp[op][mp[toupper(now)]][k] += dp[op ^ 1][j][k ^ (1 << 2)];
                        dp[op][mp[toupper(now)]][k] %= MOD;
                    }
                }
            }
        }
        else // if (now == '?')
        {
            for (int j = 0; j < 26; j++)
            {
                for (int k = 0; k < 8; k++)
                {
                    if (k >> 1 & 1) // *1*
                    {
                        dp[op][j][k] += sum[k];
                        dp[op][j][k] -= dp[op ^ 1][j][k];
                        dp[op][j][k] += sum[k ^ (1 << 1)];
                        dp[op][j][k] -= dp[op ^ 1][j][k ^ (1 << 1)];
                        dp[op][j][k] %= MOD;
                        dp[op][j][k] += MOD;
                        dp[op][j][k] %= MOD;
                    }
                }
            }
            for (int j = 26; j < 52; j++)
            {
                for (int k = 0; k < 8; k++)
                {
                    if (k >> 2 & 1) // 1**
                    {
                        dp[op][j][k] += sum[k];
                        dp[op][j][k] -= dp[op ^ 1][j][k];
                        dp[op][j][k] += sum[k ^ (1 << 2)];
                        dp[op][j][k] -= dp[op ^ 1][j][k ^ (1 << 2)];
                        dp[op][j][k] %= MOD;
                        dp[op][j][k] += MOD;
                        dp[op][j][k] %= MOD;
                    }
                }
            }
            for (int j = 52; j < 62; j++)
            {
                for (int k = 0; k < 8; k++)
                {
                    if (k >> 0 & 1) // **1
                    {
                        dp[op][j][k] += sum[k];
                        dp[op][j][k] -= dp[op ^ 1][j][k];
                        dp[op][j][k] += sum[k ^ (1 << 0)];
                        dp[op][j][k] -= dp[op ^ 1][j][k ^ (1 << 0)];
                        dp[op][j][k] %= MOD;
                        dp[op][j][k] += MOD;
                        dp[op][j][k] %= MOD;
                    }
                }
            }
        }
        for (int j = 0; j < 8; j++)
        {
            sum[j] = 0;
            for (int k = 0; k < 62; k++)
            {
                sum[j] += dp[op][k][j];
                sum[j] %= MOD;
            }
        }
    }
    int ans = 0;
    for (int i = 0; i < 62; i++)
        ans = (ans + dp[op ^ 1][i][7]) % MOD;
    cout << ans << endl;
}

signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    int t = 1;
    // cin >> t;
    init();
    while (t--)
        solve();
    return 0;
}
```

