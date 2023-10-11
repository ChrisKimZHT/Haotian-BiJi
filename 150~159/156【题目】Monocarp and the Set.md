**Educational Codeforces Round 156 (Rated for Div. 2)**

D. Monocarp and the Set

https://codeforces.com/contest/1886/problem/D

<!--more-->

2 seconds / 256 megabytes

standard input / standard output

## Problem

Monocarp has $n$ numbers $1, 2, \dots, n$ and a set (initially empty). He adds his numbers to this set $n$ times in some order. During each step, he adds a new number (which has not been present in the set before). In other words, the sequence of added numbers is a permutation of length $n$.

Every time Monocarp adds an element into the set except for the first time, he writes out a character:

-  if the element Monocarp is trying to insert becomes the maximum element in the set, Monocarp writes out the character >; 
-  if the element Monocarp is trying to insert becomes the minimum element in the set, Monocarp writes out the character <; 
-  if none of the above, Monocarp writes out the character ?. 

You are given a string $s$ of $n-1$ characters, which represents the characters written out by Monocarp (in the order he wrote them out). You have to process $m$ queries to the string. Each query has the following format:

-  $i$ $c$ — replace $s_i$ with the character $c$. 

Both before processing the queries and after each query, you have to calculate the number of different ways to order the integers $1, 2, 3, \dots, n$ such that, if Monocarp inserts the integers into the set in that order, he gets the string $s$. Since the answers might be large, print them modulo $998244353$.

## Input

The first line contains two integers $n$ and $m$ ($2 \le n \le 3 \cdot 10^5$; $1 \le m \le 3 \cdot 10^5$).

The second line contains the string $s$, consisting of exactly $n-1$ characters <, > and/or ?.

Then $m$ lines follow. Each of them represents a query. Each line contains an integer $i$ and a character $c$ ($1 \le i \le n-1$; $c$ is either <, >, or ?).

## Output

Both before processing the queries and after each query, print one integer — the number of ways to order the integers $1, 2, 3, \dots, n$ such that, if Monocarp inserts the integers into the set in that order, he gets the string $s$. Since the answers might be large, print them modulo $998244353$.

## Examples

Input

```
6 4
<?>?>
1 ?
4 <
5 <
1 >
```

Output

```
3
0
0
0
1
```

Input

```
2 2
>
1 ?
1 <
```

Output

```
1
0
1
```

## Note

In the first example, there are three possible orderings before all queries: 

-  $3, 1, 2, 5, 4, 6$; 
-  $4, 1, 2, 5, 3, 6$; 
-  $4, 1, 3, 5, 2, 6$. 

After the last query, there is only one possible ordering: 

-  $3, 5, 4, 6, 2, 1$. 

## 题解

**初始情况**

逆向思维考虑，初始集合内完整地包含数字 $1\sim n$，从右向左遍历操作序列 $s$，设此时集合大小 $k$：

- 如果 $s_i=\text{'>'}$：删除集合中最大值，只有 $1$ 种情况。
- 如果 $s_i=\text{'<'}$：删除集合中最小值，只有 $1$ 种情况。
- 如果 $s_i=\text{'?'}$：删除集合中任意非最值，有 $k-2$ 种情况。

要获得情况数的话，只需要将每次操作的情况数累乘就好了，并不需要考虑具体的操作数是多少，并且每次操作之间是独立的。

综上，要计算初始情况的情况数，只需 $O(n)$ 的遍历即可。

**后续操作**

后续操作每次会修改操作序列的一位，通过上面的分析可以知道，修改后只有这一次的情况数会改变，那么对答案乘除即可：

- 如果 $s_i$ 由 `>` / `<` 变成 `?`，那么情况数应当 $\times (k-2)$.
- 如果 $s_i$ 由 `?` 变成 `>` / `<`，那么情况数应当 $\div (k-2)$.

由于答案对 $998244353$ 取模，那么除法转换为乘法逆元即可。

**非法情况**

对于一个操作序列 $s$，还需要考虑该序列是否合法。

- 第二次插入时，如果插入数比第一次大，则 $s_1=\text{'>'}$

- 第二次插入时，如果插入数比第一次小，则 $s_1=\text{'<'}$

因此，对于一个操作序列，$s_1$ 不可能为 `?`，因此直接判断第一位即可，如果为 `?` 则不合法， 应当输出 0.

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

constexpr int MOD = 998244353;

int qpow(int a, int b)
{
    a %= MOD;
    int ans = 1;
    while (b)
    {
        if (b % 2)
            ans = ans * a % MOD;
        a = a * a % MOD;
        b /= 2;
    }
    return ans;
}

void solve()
{
    int n, m;
    cin >> n >> m;
    string s;
    cin >> s;
    int ans = 1;
    for (int i = 1; i < s.size(); i++)
        if (s[i] == '?')
            ans = ans * i % MOD;
    cout << (s[0] == '?' ? 0 : ans) << endl;
    for (int i = 0; i < m; i++)
    {
        int x;
        char c;
        cin >> x >> c;
        x--;
        if (x)
        {
            if (s[x] == '?' && c != '?')
           	    ans = ans * qpow(x, MOD - 2) % MOD;
            else if (s[x] != '?' && c == '?')
           	    ans = ans * x % MOD;
        }
        s[x] = c;
        cout << (s[0] == '?' ? 0 : ans) << endl;
    }
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

