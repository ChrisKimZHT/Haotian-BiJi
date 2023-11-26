**Educational Codeforces Round 158 (Rated for Div. 2)**

C. Add, Divide and Floor

https://codeforces.com/contest/1901/problem/C

<!--more-->

2 seconds / 256 megabytes

standard input / standard output

## Problem

You are given an integer array $a_1, a_2, \dots, a_n$ ($0 \le a_i \le 10^9$). In one operation, you can choose an integer $x$ ($0 \le x \le 10^{18}$) and replace $a_i$ with $\lfloor \frac{a_i + x}{2} \rfloor$ ($\lfloor y \rfloor$ denotes rounding $y$ down to the nearest integer) for all $i$ from $1$ to $n$. Pay attention to the fact that all elements of the array are affected on each operation. 

Print the smallest number of operations required to make all elements of the array equal.

If the number of operations is less than or equal to $n$, then print the chosen $x$ for each operation. If there are multiple answers, print any of them.

## Input

The first line contains a single integer $t$ ($1 \le t \le 10^4$) — the number of testcases.

The first line of each testcase contains a single integer $n$ ($1 \le n \le 2 \cdot 10^5$).

The second line contains $n$ integers $a_1, a_2, \dots, a_n$ ($0 \le a_i \le 10^9$).

The sum of $n$ over all testcases doesn't exceed $2 \cdot 10^5$.

## Output

For each testcase, print the smallest number of operations required to make all elements of the array equal.

If the number of operations is less than or equal to $n$, then print the chosen $x$ for each operation in the next line. If there are multiple answers, print any of them.

## Example

Input

> 4
> 1
> 10
> 2
> 4 6
> 6
> 2 1 2 1 2 1
> 2
> 0 32

Output

> 0
> 2
> 2 5
> 1
> 1
> 6

## Note

In the first testcase, all elements are already equal, so $0$ operations are required. It doesn't matter if you print an empty line afterwards or not.

In the second testcase, you can't make less than $2$ operations. There are multiple answers, let's consider the answer sequence $[2, 5]$. After applying an operation with $x = 2$, the array becomes $[\lfloor \frac{4 + 2}{2} \rfloor, \lfloor \frac{6 + 2}{2} \rfloor] = [3, 4]$. After applying an operation with $x = 5$ after that, the array becomes $[\lfloor \frac{3 + 5}{2} \rfloor, \lfloor \frac{4 + 5}{2} \rfloor] = [4, 4]$. Both elements are the same, so we are done.

In the last testcase, you can't make less than $6$ operations. Since $6$ is greater than $n$, you don't have to print them. One possible answer sequence is $[0, 0, 0, 0, 0, 0]$. We are just dividing the second element by $2$ every time and not changing the first element.

## 题解

本题重点是要找到关键性质来简化问题，否则问题会变得很麻烦。

**仅考虑最大最小值即可**

显然，对数列中每个数字 $+x$ 并不会改变它们的大小关系。

同时，对数列中每个数字 $/2$ 并向下取整也不会改变它们的大小关系（非严格）。

综上，题目给出的 $\lfloor \frac{a_i + x}{2} \rfloor$ 操作不会改变排序后数列的顺序。那么，如果要让整个数列相等，我们只需要让最大值和最小值相等，那么中间值则一定也相等了。

**仅考虑 $x=1/0$ 即可**

对于两个不相等的数，对于它们之间的不同的二进制位，我们是**不可能**通过同时 $+x$ 让不同的位变得相同的。

对于数 $10100_2,11000_2$，我们进行 $x=100_2$ 的操作后它们变成了 $1100_2,1110_2$，虽说最高两位相同了，但是第三位是不可能相同的。因此我们必须通过 $/2$ 把第三位去掉。

那么更改一下顺序，实际上上面这个操作就是先进行两次 $x=0$ 操作，再进行一次 $x=1$ 操作。

因此，操作都可以转换成 $x=1/0$，只考虑这两种值即可。

**操作策略**

我们只观察二进制最低位：

- 如果它们相同，那么进行 $x=0$ 的操作即可。
- 如果最大值为 $1$，最小值为 $0$，那么进行 $x=0$ 的操作。
- 如果最大值为 $0$，最小值为 $1$，那么进行 $x=1$ 的操作。

这样操作的原因是，$x=1$ 的操作会使最低位为 $1$ 的数变大，因此对于最小值我们希望它变大，对于最大值我们要避免它变大。

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

void solve()
{
    int n;
    cin >> n;
    int mx = 0, mn = INT32_MAX;
    for (int i = 0; i < n; i++)
    {
        int t;
        cin >> t;
        mx = max(mx, t);
        mn = min(mn, t);
    }
    int ans = 0, add = 0;
    vector<int> op;
    while (mx != mn)
    {
        if (mx % 2 == mn % 2)
            add = 0;
        else if (mx % 2 == 0)
            add = 1;
        else
            add = 0;
        mx = (mx + add) / 2;
        mn = (mn + add) / 2;
        op.push_back(add);
        ans++;
    }
    cout << ans << endl;
    if (0 < ans && ans <= n)
    {
        for (auto &ele : op)
            cout << ele << ' ';
        cout << endl;
    }
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

