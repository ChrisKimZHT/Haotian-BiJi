**Codeforces Round 908 (Div. 2)**

E. Freedom of Choice

https://codeforces.com/contest/1894/problem/E

<!--more-->

3 seconds / 512 megabytes

standard input / standard output

## Problem

Let's define the anti-beauty of a multiset $\{b_1, b_2, \ldots, b_{len}\}$ as the number of occurrences of the number $len$ in the multiset.

You are given $m$ multisets, where the $i$-th multiset contains $n_i$ distinct elements, specifically: $c_{i, 1}$ copies of the number $a_{i,1}$, $c_{i, 2}$ copies of the number $a_{i,2}, \ldots, c_{i, n_i}$ copies of the number $a_{i, n_i}$. It is guaranteed that $a_{i, 1} < a_{i, 2} < \ldots < a_{i, n_i}$. You are also given numbers $l_1, l_2, \ldots, l_m$ and $r_1, r_2, \ldots, r_m$ such that $1 \le l_i \le r_i \le c_{i, 1} + \ldots + c_{i, n_i}$.

Let's create a multiset $X$, initially empty. Then, for each $i$ from $1$ to $m$, you must perform the following action exactly once:

1. Choose some $v_i$ such that $l_i \le v_i \le r_i$
2. Choose any $v_i$ numbers from the $i$-th multiset and add them to the multiset $X$.

You need to choose $v_1, \ldots, v_m$ and the added numbers in such a way that the resulting multiset $X$ has the minimum possible anti-beauty.

## Input

Each test consists of multiple test cases. The first line contains a single integer $t$ ($1 \le t \le 10^4$) — the number of test cases. The description of the test cases follows.

The first line of each test case contains a single integer $m$ ($1 \le m \le 10^5$) — the number of given multisets.

Then, for each $i$ from $1$ to $m$, a data block consisting of three lines is entered.

The first line of each block contains three integers $n_i, l_i, r_i$ ($1 \le n_i \le 10^5, 1 \le l_i \le r_i \le c_{i, 1} + \ldots + c_{i, n_i} \le 10^{17}$) — the number of distinct numbers in the $i$-th multiset and the limits on the number of elements to be added to $X$ from the $i$-th multiset.

The second line of the block contains $n_i$ integers $a_{i, 1}, \ldots, a_{i, n_i}$ ($1 \le a_{i, 1} < \ldots < a_{i, n_i} \le 10^{17}$) — the distinct elements of the $i$-th multiset.

The third line of the block contains $n_i$ integers $c_{i, 1}, \ldots, c_{i, n_i}$ ($1 \le c_{i, j} \le 10^{12}$) — the number of copies of the elements in the $i$-th multiset.

It is guaranteed that the sum of the values of $m$ for all test cases does not exceed $10^5$, and also the sum of $n_i$ for all blocks of all test cases does not exceed $10^5$.

## Output

For each test case, output the minimum possible anti-beauty of the multiset $X$ that you can achieve.

## Example

Input

> 7
> 3
> 3 5 6
> 10 11 12
> 3 3 1
> 1 1 3
> 12
> 4
> 2 4 4
> 12 13
> 1 5
> 1
> 7 1000 1006
> 1000 1001 1002 1003 1004 1005 1006
> 147 145 143 143 143 143 142
> 1
> 2 48 50
> 48 50
> 25 25
> 2
> 1 1 1
> 1
> 1
> 1 1 1
> 2
> 1
> 1
> 1 1 1
> 1
> 2
> 2
> 1 1 1
> 1
> 1
> 2 1 1
> 1 2
> 1 1
> 2
> 4 8 10
> 11 12 13 14
> 3 3 3 3
> 2 3 4
> 11 12
> 2 2

Output

> 1
> 139
> 0
> 1
> 1
> 0
> 0

## Note

In the first test case, the multisets have the following form:

1. $\{10, 10, 10, 11, 11, 11, 12\}$. From this multiset, you need to select between $5$ and $6$ numbers.
2. $\{12, 12, 12, 12\}$. From this multiset, you need to select between $1$ and $3$ numbers.
3. $\{12, 13, 13, 13, 13, 13\}$. From this multiset, you need to select $4$ numbers.

You can select the elements $\{10, 11, 11, 11, 12\}$ from the first multiset, $\{12\}$ from the second multiset, nd $\{13, 13, 13, 13\}$ from the third multiset. Thus, $X = \{10, 11, 11, 11, 12, 12, 13, 13, 13, 13\}$. The size of $X$ is $10$, the number $10$ appears exactly $1$ time in $X$, so the anti-beauty of $X$ is $1$. It can be shown that it is not possible to achieve an anti-beauty less than $1$.

## 题解

首先，若第 $i$ 个多重集合含有的元素种类数为 $n_i$，那么所有多重集合总共的元素种类数一定 $\leq\sum n$.

而我们在所有集合中，按照限制条件能够选择的元素数量范围为 $[\sum l,\sum r]$，即共有 $\sum r - \sum l + 1$ 种可能。

那么容易得到，如果 $\sum r - \sum l + 1>\sum n$，那么一定存在一种选则 $x$ 个数的选法，使 $x$ 没有在所有集合中出现过。

因此，我们首先特判 $\sum r - \sum l + 1>\sum n$，如果成立，则答案为 $0$，接下来要做的就是处理剩余的情况了。

------

这个特判的最大意义是缩小数据范围，剩余的情况是 $\sum r - \sum l + 1\leq\sum n$，题目给出限制 $\sum n\leq 10^5$，那么就说明 $\sum r - \sum l + 1\leq 10^5$. 这个数据范围就完全可以遍历所有的选择数目，计算选择 $i$ 个数（$\sum l\leq i \leq \sum r$）时，最佳选法的 anti-beauty 值。最后取所有选法的最小值即可。

接下来，问题就转换为，若规定选择 $x$ 个数，这种情况最佳选法的答案是多少。

------

比较容易想到的是遍历 $m$ 个多重集合，对于每个多重集合，记非 $x$ 的数的个数为 $t$. 若 $t<l$，那么我们不得不选择 $l-t$ 个数 $x$；若 $t\geq l$，那么我们就可以一个 $x$ 都不选，为了给其他集合争取机会，我们选择尽量多的数，因此选择 $\min\{t,r\}$ 个非 $x$ 数。

但是这种情况，对于每个选择数目 $x$，我们都要遍历 $m$ 个集合，时间复杂度为 $O(nm)$，可见这并不能通过。

但是可以转换一下思路，我们将包含有数 $x$ 的集合存到一起，每次考察数 $x$ 的时候只遍历含有它的集合，不遍历不包含它的集合。这样进行遍历的话，遍历的总次数就一定 $\leq\sum n\leq10^5$ 了。

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long
 
using namespace std;
 
void solve()
{
    int m;
    cin >> m;
    vector<int> n(m), l(m), r(m), s(m);
    vector<vector<int>> a(m), c(m);
    int sl = 0, sr = 0, sn = 0;
    for (int i = 0; i < m; i++)
    {
        cin >> n[i] >> l[i] >> r[i];
        sn += n[i];
        sl += l[i];
        sr += r[i];
        a[i].resize(n[i]);
        c[i].resize(n[i]);
        for (int j = 0; j < n[i]; j++)
            cin >> a[i][j];
        for (int j = 0; j < n[i]; j++)
            cin >> c[i][j];
        s[i] = accumulate(c[i].begin(), c[i].end(), 0LL);
    }
    if (sr - sl + 1 > sn)
    {
        cout << 0 << endl;
        return;
    }
    map<int, int> sum;
    map<int, vector<pair<int, int>>> idx;
    for (int i = 0; i < m; i++)
    {
        for (int j = 0; j < n[i]; j++)
        {
            sum[a[i][j]] += r[i];
            idx[a[i][j]].push_back({i, j});
        }
    }
    int ans = INT64_MAX;
    for (int i = sl; i <= sr; i++)
    {
        int need = 0, size = sr - sum[i];
        for (auto &[p, q] : idx[i])
        {
            int t = s[p] - c[p][q];
            if (t < l[p])
            {
                need += l[p] - t;
                size += l[p];
            }
            else
            {
                size += min(t, r[p]);
            }
        }
        ans = min(ans, need + max(i - size, 0LL));
    }
    cout << ans << endl;
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

