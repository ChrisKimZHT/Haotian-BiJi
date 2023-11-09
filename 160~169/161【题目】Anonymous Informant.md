**Codeforces Round 908 (Div. 2)**

C. Anonymous Informant

https://codeforces.com/contest/1894/problem/C

<!--more-->

3 seconds / 512 megabytes

standard input / standard output

## Problem

You are given an array $b_1, b_2, \ldots, b_n$.

An anonymous informant has told you that the array $b$ was obtained as follows: initially, there existed an array $a_1, a_2, \ldots, a_n$, after which the following two-component operation was performed $k$ times:

1. A fixed point$^{\dagger}$ $x$ of the array $a$ was chosen.
2. Then, the array $a$ was cyclically shifted to the left$^{\ddagger}$ exactly $x$ times.

As a result of $k$ such operations, the array $b_1, b_2, \ldots, b_n$ was obtained. You want to check if the words of the anonymous informant can be true or if they are guaranteed to be false.

$^{\dagger}$A number $x$ is called a fixed point of the array $a_1, a_2, \ldots, a_n$ if $1 \leq x \leq n$ and $a_x = x$.

$^{\ddagger}$A cyclic left shift of the array $a_1, a_2, \ldots, a_n$ is the array $a_2, \ldots, a_n, a_1$.

## Input

Each test contains multiple test cases. The first line contains an integer $t$ ($1 \le t \le 10^4$) — the number of test cases. The description of the test cases follows.

The first line of each test case contains two integers $n, k$ ($1 \le n \le 2 \cdot 10^5$, $1 \le k \le 10^9$) — the length of the array $b$ and the number of operations performed.

The second line of each test case contains $n$ integers $b_1, b_2, \ldots, b_n$ ($1 \le b_i \le 10^9$) — the elements of the array $b$.

It is guaranteed that the sum of the values of $n$ for all test cases does not exceed $2 \cdot 10^5$.

## Output

For each test case, output "Yes" if the words of the anonymous informant can be true, and "No" if they are guaranteed to be false.

## Example

Input

> 6
> 5 3
> 4 3 3 2 3
> 3 100
> 7 2 1
> 5 5
> 6 1 1 1 1
> 1 1000000000
> 1
> 8 48
> 9 10 11 12 13 14 15 8
> 2 1
> 1 42

Output

> Yes
> Yes
> No
> Yes
> Yes
> No

## Note

In the first test case, the array $a$ could be equal to $[3, 2, 3, 4, 3]$. In the first operation, a fixed point $x = 2$ was chosen, and after $2$ left shifts, the array became $[3, 4, 3, 3, 2]$. In the second operation, a fixed point $x = 3$ was chosen, and after $3$ left shifts, the array became $[3, 2, 3, 4, 3]$. In the third operation, a fixed point $x = 3$ was chosen again, and after $3$ left shifts, the array became $[4, 3, 3, 2, 3]$, which is equal to the array $b$.

In the second test case, the array $a$ could be equal to $[7, 2, 1]$. After the operation with a fixed point $x = 2$, the array became $[1, 7, 2]$. Then, after the operation with a fixed point $x = 1$, the array returned to its initial state $[7, 2, 1]$. These same $2$ operations (with $x = 2$, and $x = 1$) were repeated $49$ times. So, after $100$ operations, the array returned to $[7, 2, 1]$.

In the third test case, it can be shown that there is no solution.

## 题解

首先，对于一个不动点 $x$，对其进行操作后，它的位置一定位于数列尾部。因为 $x$ 位于第 $x$ 位，对它操作后，它左移 $x$ 次越过左界后就来到了数列尾部。

因此，根据上述性质，如果我们知道操作后的结果为 $b_0, b_1, \ldots, b_{n-1}$，那么上次操作的数一定是尾部的 $b_{n-1}$，注意这里为了方便，下标从 $0$ 开始。

那么我们就知道，上一次操作是左移 $b_{n-1}$ 次，那么还原的方式就是右移 $b_{n-1}$ 次。那么还原后，操作前的数列尾部应当是现在尾部前面的第 $b_{n-1}$ 个，即 $\text{pos}\leftarrow \text{pos} -b_{\text{pos}}$，而不成立的情况便是 $b_{\text{pos}}>n$ 的情况。

为了保证下标范围，需要取模：$\text{pos}\leftarrow (\text{pos} -b_{\text{pos}}+n)\bmod n$.

那么该解法将会是 $O(k)$ 的复杂度，$k\in[1,10^9]$ 明显是会超时的，但是考虑一个长度为 $n$ 的数列，它通过左移得到的可能情况只有可能是 $n$ 种。因此如果 $k\geq n$，数组的状态一定存在循环，因此只要验证 $n$ 次成立，那么 $>n$ 的也一定成立。

那么这样复杂度就变成 $O(\min\{n,k\})$ 了，可以接受。

（你也可以通过一个长度为 $n$ 的 bool 数组记录当前出现过的情况，若发现之前出现过的情况则 break 掉循环。但是实测也优化不了多少运行时长。）

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

void solve()
{
    int n, k;
    cin >> n >> k;
    k = min(n, k);
    vector<int> a(n);
    for (int i = 0; i < n; i++)
        cin >> a[i];
    int pos = n - 1;
    for (int i = 0; i < k; i++)
    {
        if (a[pos] > n)
        {
            cout << "No" << endl;
            return;
        }
		pos = (pos - a[pos] + n) % n;
    }
    cout << "Yes" << endl;
}

signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    int t = 1;
    cin >> t;
    while (t--)
        solve();
    return 0;
}
```

