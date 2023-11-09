**Codeforces Round 908 (Div. 2)**

D. Neutral Tonality

https://codeforces.com/contest/1894/problem/D

<!--more-->

3 seconds / 512 megabytes

standard input / standard output

## Problem

You are given an array $a$ consisting of $n$ integers, as well as an array $b$ consisting of $m$ integers.

Let $\text{LIS}(c)$ denote the length of the [longest increasing subsequence](https://en.wikipedia.org/wiki/Longest_increasing_subsequence) of array $c$. For example, $\text{LIS}([2, \underline{1}, 1, \underline{3}])$ = $2$, $\text{LIS}([\underline{1}, \underline{7}, \underline{9}])$ = $3$, $\text{LIS}([3, \underline{1}, \underline{2}, \underline{4}])$ = $3$.

You need to insert the numbers $b_1, b_2, \ldots, b_m$ into the array $a$, at any positions, in any order. Let the resulting array be $c_1, c_2, \ldots, c_{n+m}$. You need to choose the positions for insertion in order to **minimize** $\text{LIS}(c)$.

Formally, you need to find an array $c_1, c_2, \ldots, c_{n+m}$ that simultaneously satisfies the following conditions:

-  The array $a_1, a_2, \ldots, a_n$ is a subsequence of the array $c_1, c_2, \ldots, c_{n+m}$.
-  The array $c_1, c_2, \ldots, c_{n+m}$ consists of the numbers $a_1, a_2, \ldots, a_n, b_1, b_2, \ldots, b_m$, possibly rearranged.
-  The value of $\text{LIS}(c)$ is the **minimum** possible among all suitable arrays $c$.
## Input

Each test contains multiple test cases. The first line contains a single integer $t$ $(1 \leq t \leq 10^4)$ — the number of test cases. The description of the test cases follows.

The first line of each test case contains two integers $n, m$ $(1 \leq n \leq 2 \cdot 10^5, 1 \leq m \leq 2 \cdot 10^5)$ — the length of array $a$ and the length of array $b$.

The second line of each test case contains $n$ integers $a_1, a_2, \ldots, a_n$ $(1 \leq a_i \leq 10^9)$ — the elements of the array $a$.

The third line of each test case contains $m$ integers $b_1, b_2, \ldots, b_m$ $(1 \leq b_i \leq 10^9)$ — the elements of the array $b$.

It is guaranteed that the sum of $n$ over all test cases does not exceed $2\cdot 10^5$, and the sum of $m$ over all test cases does not exceed $2\cdot 10^5$.

## Output

For each test case, output $n + m$ numbers — the elements of the final array $c_1, c_2, \ldots, c_{n+m}$, obtained after the insertion, such that the value of $\text{LIS}(c)$ is minimized. If there are several answers, you can output any of them.

## Example

Input

> 7
> 2 1
> 6 4
> 5
> 5 5
> 1 7 2 4 5
> 5 4 1 2 7
> 1 9
> 7
> 1 2 3 4 5 6 7 8 9
> 3 2
> 1 3 5
> 2 4
> 10 5
> 1 9 2 3 8 1 4 7 2 9
> 7 8 5 4 6
> 2 1
> 2 2
> 1
> 6 1
> 1 1 1 1 1 1
> 777

Output

> 6 5 4
> 1 1 7 7 2 2 4 4 5 5
> 9 8 7 7 6 5 4 3 2 1
> 1 3 5 2 4
> 1 9 2 3 8 8 1 4 4 7 7 2 9 6 5
> 2 2 1
> 777 1 1 1 1 1 1

## Note

In the first test case, $\text{LIS}(a) = \text{LIS}([6, 4]) = 1$. We can insert the number $5$ between $6$ and $4$, then $\text{LIS}(c) = \text{LIS}([6, 5, 4]) = 1$.

In the second test case, $\text{LIS}([\underline{1}, 7, \underline{2}, \underline{4}, \underline{5}])$ = $4$. After the insertion, $c = [1, 1, 7, 7, 2, 2, 4, 4, 5, 5]$. It is easy to see that $\text{LIS}(c) = 4$. It can be shown that it is impossible to achieve $\text{LIS}(c)$ less than $4$.

## 题解

首先需要注意到，将 $b$ 序列插入 $a$ 序列是不可能降低 $a$ 序列原有的 $\text{LIS}$ 长度的。题目让我们最小化 $\text{LIS}$ 长度，则意义是让我们最小化 $\Delta\text{LIS}$. 

最基本的，我们插入的序列必须是降序的，如此能保证插入的 $b$ 序列自身不会产生上升子序列，避免 $\text{LIS}$ 长度增长。所以首先将 $b$ 序列排序为降序。

接下来，若插入的值与原序列形成的都是降序段，则不会使 $\text{LIS}$ 长度增长。因此，可以将每个数都插入到原序列比它小的数左边。对于一个序列 $3,1,5$，如果要插入 $6,4,2$，变成 $\underline{6},\underline{4},3,\underline{2},1,5$，不会导致 $\text{LIS}$ 长度增长。

因此，这道题的最优解实际上 $\Delta\text{LIS}=0$.

代码实现上其实非常简单，和归并排序的合并过程是一样的写法。

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long
 
using namespace std;
 
void solve()
{
    int n, m;
    cin >> n >> m;
    vector<int> a(n), b(m);
    for (int i = 0; i < n; i++)
        cin >> a[i];
    for (int i = 0; i < m; i++)
        cin >> b[i];
    sort(b.begin(), b.end(), greater<int>());
    int i = 0, j = 0;
    while (i < n && j < m)
    {
        if (a[i] <= b[j])
            cout << b[j++] << ' ';
        else
            cout << a[i++] << ' ';
    }
    for (; i < n; i++)
        cout << a[i] << ' ';
    for (; j < m; j++)
        cout << b[j] << ' ';
    cout << endl;
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

