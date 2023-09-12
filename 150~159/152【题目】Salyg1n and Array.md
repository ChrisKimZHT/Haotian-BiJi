**Codeforces Round 897 (Div. 2)**

E1. Salyg1n and Array (simple version)

E2. Salyg1n and Array (hard version)

https://codeforces.com/contest/1867/problem/E1

https://codeforces.com/contest/1867/problem/E2

<!--more-->

1 second / 256 megabytes

standard input / standard output

## Problem (hard version)

This is the hard version of the problem. The only difference between the versions is the limit on the number of queries. In this version, you can make no more than 57 queries. You can make hacks only if both versions of the problem are solved.

This is an interactive problem!

salyg1n has given you a positive integer $k$ and wants to play a game with you. He has chosen an array of $n$ integers $a_1, a_2, \ldots, a_n$ ($1 \leq a_i \leq 10^9$). You must print $a_1 \oplus a_2 \oplus \ldots \oplus a_n$, where $\oplus$ denotes the bitwise XOR operation. You can make queries of the following type:

-  $?$ $i$: in response to this query, you will receive $a_i \oplus a_{i + 1} \oplus \ldots \oplus a_{i + k - 1}$. Also, after this query, the subarray $a_i, a_{i + 1}, \ldots, a_{i + k - 1}$ will be reversed, i.e., the chosen array $a$ will become: $a_1, a_2, \ldots a_{i - 1}, a_{i + k - 1}, a_{i + k - 2}, \ldots, a_{i + 1}, a_i, a_{i + k}, \ldots, a_n$. 

You can make no more than $57$ queries to answer the problem.

## Input

The first line contains a single integer $t$ ($1 \leq t \leq 1000$) – the number of test cases.

## Interaction

The interaction between your program and the jury's program begins with reading two positive even integers $n$ and $k$ ($1 \leq k \leq n \leq k^2 \leq 2500$) – the length of the chosen array and the length of the query subarray, respectively.

To find the value of $a_i \oplus a_{i + 1} \oplus \ldots \oplus a_{i + k - 1}$, print the query in the format $?$ $i$ ($1 \leq i \leq n - k + 1$). Then read a single integer – the answer to your query.

You can make no more than $57$ queries. When you are ready to print the answer, output it in the format $!$ $x$. After that, proceed to process the next test case or terminate the program if it was the last test case. Printing the answer does not count as one of the $57$ queries.

If your program makes more than $57$ queries for one set of input data, or makes an invalid query, then the response to the query will be -1. After receiving such a response, your program should immediately terminate to receive the verdict Wrong Answer. Otherwise, it may receive any other verdict.

After printing a query do not forget to output the end of line and flush the output. Otherwise, you will get Idleness limit exceeded. To do this, use:

-  fflush(stdout) or cout.flush() in C++;
-  System.out.flush() in Java;
-  flush(output) in Pascal;
-  stdout.flush() in Python;
-  see the documentation for other languages.

It is guaranteed that the sum of $n$ over all test cases does not exceed $10000$. The interactor in this problem is not adaptive.Hacks:

To perform a hack, use the following format:

The first line contains a single integer $t$ – the number of test cases.

The description of each test case should consist of two lines. The first line contains the numbers $n$ and $k$ – the length of the chosen array and the length of the query subarray, respectively. The second line contains $n$ numbers $a_1, a_2, \ldots, a_n$ – the array that the jury should choose for this test case.

## Example

Input

```
2
4 2

6

4

6 6

4
```

Output

```
? 1

? 3

! 2

? 1

! 4
```

## Note

In the first test case, the jury has chosen the array $a$ $=$ $[4, 2, 5, 1]$

In the second test case, the jury has chosen the array $a$ $=$ $[5, 7, 1, 3, 3, 7]$

## 题解

> 本题的 Hard Version 实际上非常简单，因此直接略过 Easy Version.

首先，对于 $k\mid n$ 的情况，求解方式非常简单，就是把每一段长度为 $k$ 的区间的异或和询问到，异或到一起便是答案了：

```cpp
for (int i = 1; i <= n - k + 1; i += k)
    ans ^= query(i);
```

对于其他情况，长度则可以表示为 $x\cdot k+t$，其中 $t\in(0,k)$. 那么首先将问题分成两部分来考虑：

- $x-1$ 段长度为 $k$ 的区间：长度共 $(x-1)\cdot k$
- 剩下的一段区间：长度为 $x+k$

对于前面 $x-1$ 段长度为 $k$ 的区间，直接依次询问出结果即可。然后专注于考虑剩下的这段长度为 $x+k$ 的区间，要解决的问题就是构造一个询问流程，把这段长度为 $x+k$ 的区间的异或和拼凑出来。。

为了表示方便，对于这段长度为 $x+k$ 的区间，重新令下标从 $1$ 开始，不妨令长度为 $7$，$k=5$，记为 $[a_1,a_2,a_3,a_4,a_5,a_6,a_7]$

下面就直接展示答案了（因为构造题确实没啥因果）：

- 询问靠左的 $k$ 区间，得到答案 $a$，代表的是 $\{a_1,a_2,a_3,a_4,a_5\}$ 的异或和，操作后区间变成 $[a_5,a_4,a_3,a_2,a_1,a_6,a_7]$
- 询问正中心的 $k$ 区间，得到答案 $b$，代表的是 $\{a_1,a_2,a_3,a_4,a_6\}$ 的异或和，操作后区间变成 $[a_5,a_6,a_1,a_2,a_3,a_4,a_7]$
- 此时，$a\oplus b$ 就可以得到 $\{a_5,a_6\}$ 的异或和。此时我们发现非常巧的是，$a_5,a_6$ 正好跑到最左边来了。
- 询问靠右的 $k$ 区间，得到答案 $c$，代表的是 $\{a_1,a_2,a_3,a_4,a_7\}$ 的异或和。

综上，$a\oplus b\oplus c$ 遍代表的是 $\{a_1,a_2,a_3,a_4,a_5,a_6,a_7\}$ 的异或和，再将其与前面一段的答案异或，则得到了最终答案。

（上面是一个具体的例子，如果要通式的话可以自己推导下）

总共的询问次数是 $x-1+3=x+2$ 次，代入 $n,k$ 则为：$\lceil n/k\rceil+1$，根据数据范围，最坏情况下也只需要询问 $51$ 次，甚至比题目限制的 $57$ 次还少 $6$ 次，可谓非常宽松了。

## 代码

```cpp
#include <bits/stdc++.h>
// #define endl '\n'
// #define int long long

using namespace std;

int query(int x)
{
    cout << "? " << x << endl;
    int res;
    cin >> res;
    return res;
}

void finish(int x)
{
    cout << "! " << x << endl;
    return;
}

void solve()
{
    int n, k;
    cin >> n >> k;
    int ans = 0;
    // remain length: [n, 2 * n)
    for (int i = 1; i + k - 1 <= n - k; i += k)
        ans ^= query(i);
    if (n % k)
    {
        int offset = (n / k - 1) * k;
        int len = n - offset;
        int pre = (len - k) / 2;
        ans ^= query(offset + 1);
        ans ^= query(offset + 1 + pre);
    }
    ans ^= query(n - k + 1);
    finish(ans);
}

signed main()
{
    // ios::sync_with_stdio(false);
    // cin.tie(0);
    // cout.tie(0);
    int t = 1;
    cin >> t;
    while (t--)
        solve();
    return 0;
}
```

