**AtCoder Beginner Contest 284**

F - ABCBAC

https://atcoder.jp/contests/abc284/tasks/abc284_f

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $500$ points

### Problem Statement

For a string $S$ of length $N$ and an integer $i\ (0\leq i\leq N)$, let us define the string $f_i(S)$ as the concatenation of:

- the first $i$ characters of $S$,
- the reversal of $S$, and
- the last $(N-i)$ characters of $S$,

in this order. For instance, if $S=$ `abc` and $i=2$, we have $f_i(S)=$ `abcbac`.

You are given a string $T$ of length $2N$. Find a pair of a string $S$ of length $N$ and an integer $i\ (0\leq i\leq N)$ such that $f_i(S)=T$. If no such pair of $S$ and $i$ exists, report that fact.

### Constraints

- $1\leq N \leq 10^6$
- $N$ is an integer.
- $T$ is a string of length $2N$ consisting of lowercase English letters.
### Input

The input is given from Standard Input in the following format:

> $N$ 
> $T$

### Output

If no pair of $S$ and $i$ satisfies the condition, print -1. Otherwise, print $S$ and $i$, separated by a newline. If multiple pairs of $S$ and $i$ satisfy the condition, you may print any of them.

### Sample Input 1

> 3
> abcbac

### Sample Output 1

> abc
> 2

As mentioned in the problem statement, if $S=$ abc and $i=2$, we have $f_i(S)=$ abcbac, which equals $T$, so you should print abc and $2$.

### Sample Input 2

> 4
> abababab

### Sample Output 2

> abab
> 1

$S=$ abab and $i=3$ also satisfy the condition.

### Sample Input 3

> 3
> agccga

### Sample Output 3

> cga
> 0

$S=$ agc and $i=3$ also satisfy the condition.

### Sample Input 4

> 4
> atcodeer

### Sample Output 4

> -1

If no pair of $S$ and $i$ satisfies the condition, print -1.

### 题解

可以用字符串哈希的额外性质解决：https://io.zouht.com/63.html

对于一个字符串 $S:abcdef$，其逆序字符串 $S':fedcba$，分别对两个字符串预处理字符串哈希得到 $h$ 和 $hrev$ 数组。

对于任意 $i$，我们可以在 $O(1)$ 时间内确定这种情况是否成立，如 $i=2$ 时：

字符串拆分为 $ab|cde|f$，首先计算两端字符串的哈希值：
$$
hash(ab)=h_2\\
hash(f)=h_5-h_4\cdot p
$$
然后组合出新字符串的哈希值：
$$
hash(abf)=hash(ab)\cdot p+hash(f)
$$
然后计算中间字符串的哈希值，这里利用逆序的字符串：
$$
hash(edc)=hrev_4-hrev_1\cdot p^3
$$
再比对 $hash(abf)$ 和 $hash(edc)$ 即可确定字符串是否一致。

**双哈希**

自然溢出法的单次哈希确定会被测试样例卡掉 5 个点，但是使用取模两个质数的哈希就能通过。下面的代码用了 $19260817$ 和 $19660813$ 两个质数。

### 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'

using namespace std;

const int MAXN = 2e6 + 10;
int N;
char T[MAXN], T_rev[MAXN];

// hash-1: 19260817
const unsigned long long MOD_1 = 19260817;
unsigned long long p_1[MAXN], h_1[MAXN], h_rev_1[MAXN];
void init_1()
{
    p_1[0] = 1;
    for (int i = 1; i <= 2 * N; i++)
    {
        p_1[i] = (p_1[i - 1] * 131) % MOD_1;
        h_1[i] = ((h_1[i - 1] * 131) % MOD_1 + T[i]) % MOD_1;
        h_rev_1[i] = ((h_rev_1[i - 1] * 131) % MOD_1 + T_rev[i]) % MOD_1;
    }
}

unsigned long long get_1(int l, int r)
{
    return (h_1[r] % MOD_1 + MOD_1 - (h_1[l - 1] * p_1[r - l + 1]) % MOD_1) % MOD_1;
}

unsigned long long get_rev_1(int l, int r)
{
    return (h_rev_1[r] % MOD_1 + MOD_1 - (h_rev_1[l - 1] * p_1[r - l + 1]) % MOD_1) % MOD_1;
}

// hash-2: 19660813
const unsigned long long MOD_2 = 19660813;
unsigned long long p_2[MAXN], h_2[MAXN], h_rev_2[MAXN];
void init_2()
{
    p_2[0] = 1;
    for (int i = 1; i <= 2 * N; i++)
    {
        p_2[i] = (p_2[i - 1] * 131) % MOD_2;
        h_2[i] = ((h_2[i - 1] * 131) % MOD_2 + T[i]) % MOD_2;
        h_rev_2[i] = ((h_rev_2[i - 1] * 131) % MOD_2 + T_rev[i]) % MOD_2;
    }
}

unsigned long long get_2(int l, int r)
{
    return (h_2[r] % MOD_2 + MOD_2 - (h_2[l - 1] * p_2[r - l + 1]) % MOD_2) % MOD_2;
}

unsigned long long get_rev_2(int l, int r)
{
    return (h_rev_2[r] % MOD_2 + MOD_2 - (h_rev_2[l - 1] * p_2[r - l + 1]) % MOD_2) % MOD_2;
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    cin >> N >> T + 1;
    for (int i = 2 * N; i >= 1; i--)
        T_rev[2 * N - i + 1] = T[i];
    init_1();
    init_2();
    for (int i = 0; i <= N; i++)
    {
        int l = N - i + 1, r = 2 * N - i;
        unsigned long long a_1 = get_rev_1(l, r),
                           a_2 = get_rev_2(l, r),
                           b_1 = get_1(1, i),
                           b_2 = get_2(1, i),
                           c_1 = get_1(i + N + 1, 2 * N),
                           c_2 = get_2(i + N + 1, 2 * N);
        if (a_1 == ((b_1 * p_1[N - i]) % MOD_1 + c_1) % MOD_1 &&
            a_2 == ((b_2 * p_2[N - i]) % MOD_2 + c_2) % MOD_2)
        {
            for (int j = i + N; j >= i + 1; j--)
                cout << T[j];
            cout << endl;
            cout << i << endl;
            return 0;
        }
    }
    cout << -1 << endl;
    return 0;
}
```

