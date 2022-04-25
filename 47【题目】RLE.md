**Monoxer Programming Contest 2022（AtCoder Beginner Contest 249）**

E - RLE

https://atcoder.jp/contests/abc249/tasks/abc249_e

<!--more-->

Time Limit: 3 sec / Memory Limit: 1024 MB

Score : $500$ points

### Problem Statement

Consider the following procedure of, given a string $X$ consisting of lowercase English alphabets, obtaining a new string:

- Split the string $X$ off at the positions where two different characters are adjacent to each other.
- For each string $Y$ that has been split off, replace $Y$ with a string consisting of the character which $Y$ consists of, followed by the length of $Y$.
- Concatenate the replaced strings without changing the order.

For example, `aaabbcccc` is divided into `aaa`,`bb`,`cccc`, which are replaced by `a3`,`b2`,`c4`, respectively, which in turn are concatenated without changing the order, resulting in `a3b2c4`.If the given string is `aaaaaaaaaa` , the new string is `a10` .

Find the number, modulo $P$, of strings $S$ of lengths $N$ consisting of lowercase English alphabets, such that the length of $T$ is smaller than that of $S$, where $T$ is the string obtained by the procedure above against the string $S$.

### Constraints

- $1 \le N \le 3000$
- $10^8 \le P \le 10^9$
- $N$ and $P$ are integers.
- $P$ is a prime.

------

### Input

Input is given from Standard Input in the following format:

> $N$ $P$

### Output

Print the answer.

------

### Sample Input 1

> 3 998244353

### Sample Output 1

> 26

Those strings of which the $1$-st, $2$-nd, and $3$-rd characters are all the same satisfy the condition.

For example, `aaa` becomes `a3`, which satisfies the condition, while `abc` becomes `a1b1c1`, which does not.

------

### Sample Input 2

> 2 998244353

### Sample Output 2

> 0

Note that if a string is transformed into another string of the same length, such as `aa` that becomes `a2`, it does not satisfy the condition.

------

### Sample Input 3

> 5 998244353

### Sample Output 3

> 2626

Strings like `aaabb` and `aaaaa` satisfy the condition.

------

### Sample Input 4

> 3000 924844033

### Sample Output 4

> 607425699

Find the number of strings satisfying the condition modulo $P$.

### 我的笔记

#### 核心思想：动态规划

**定义**

$dp[i][j]:=$ 原长为 $i$，转化后长为 $j$ 的字符串种数。

**初始化**

若一个字符串只含一种字符，如长度为 $6$ 的字符串 `aaaaaa`，转化后 `a6` 长度为 $2$，一共有 $26$ 种字母，则可初始化 $dp[i][f(i)] = 26$，$1\leq i \leq N$.
$$
\begin{equation}
f(x)=\left\{
	\begin{aligned}
	&2 \quad 1 \leq x<10\\
	&3 \quad 10 \leq x<100\\
	&4 \quad 100 \leq x<1000\\
	&5 \quad 1000 \leq x<10000
	\end{aligned}
	\right
	.
\end{equation}
$$
**转移**

在字符串 `aaaaaa` 后加上 `bb`，转化后的字符串从 `a6` 变为 `a6b2`。

设加上的字符串长度为 $k$，则：$dp[i+k][j+f(k)]$ += $25\times dp[i][j]$（加的字符串字母不能和之前的一样，所以只有 $26-1$ 种字母）

```cpp
for (int i = 1; i <= N; i++)
    for (int j = 1; j <= N; j++)
        for (int k = 1; k <= N - i; k++)
            if (j + length(k) <= N)
            	{dp[i + k][j + length(k)] += 25 * dp[i][j] % P; dp[i + k][j + length(k)] %= P;}
```

**答案**

题目需求解原长为 $N$，转化后长度 $<N$ 的种数，即 $ans=\sum_{i=1}^{N-1} dp[N][i]$.

**时间复杂度**

$O(N^3)$，超时无疑

#### 优化思想：前缀和

上面的方法第三层循环为：将 $dp[i][j]$ 加到很多个其他数里面去，很费时。

我们可以反向思维：对于一个 $dp[i][j]$，哪些数加到了这里面。

然后我们可以发现，对于一个 $dp[i][j]$，$dp[(i-9)\sim(i-1)][j-2]$、$dp[(i-99)\sim(i-10)][j-3]$、$dp[(i-999)\sim(i-100)][j-4]$、$dp[(i-9999)\sim(i-1000)][j-4]$，这四种数加到了里面。

并且这四种数字，每种数字都是同一列的，那么我们可以将每一列做一个前缀和，这样就可以在 $O(1)$ 时间内获得这四种数字的值，加到 $dp[i][j]$ 里就行了。

每次算完 $dp[i][j]$ 后，更新新一位的前缀和 $presum[i][j]=presum[i-1][j]+dp[i][j]$.

### 代码

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN = 3010;
long long dp[MAXN][MAXN * 2], presum[MAXN][MAXN * 2];

inline int length(int x)
{
    if (x >= 1000)
        return 5;
    else if (x >= 100)
        return 4;
    else if (x >= 10)
        return 3;
    else
        return 2;
}

int main()
{
    int N, P;
    cin >> N >> P;
    for (int i = 1; i <= N; i++)
        dp[i][length(i)] = 26;
    for (int i = 1; i <= N; i++)
    {
        for (int j = 1; j <= N; j++)
        {
            for (int k = 1, len = 2; k < i && len < j; k *= 10, len++)
            {
                int l = max(0, i - k * 10), r = i - k;
                dp[i][j] += 25 * (presum[r][j - len] - presum[l][j - len] + P) % P;
            }
            presum[i][j] = (presum[i - 1][j] + dp[i][j]) % P;
        }
    }
    long long ans = 0;
    for (int i = 1; i < N; i++)
    {
        ans += dp[N][i];
        ans %= P;
    }
    cout << ans << endl;
    return 0;
}
```

