**AtCoder Regular Contest 153**

C - ± Increasing Sequence

https://atcoder.jp/contests/arc153/tasks/arc153_c

<!--more-->

### Problem Statement

You are given a sequence of length $N$, $A = (A_1, \ldots, A_N)$, consisting of $1$ and $-1$.

Determine whether there is an integer sequence $x = (x_1, \ldots, x_N)$ that satisfies all of the following conditions, and print one such sequence if it exists.

- $|x_i| \leq 10^{12}$ for every $i$ ($1\leq i\leq N$).
- $x$ is strictly increasing. That is, $x_1 < \cdots < x_N$.
- $\sum_{i=1}^N A_ix_i = 0$.
### Constraints

- $1\leq N\leq 2\times 10^5$
- $A_i \in \lbrace 1, -1\rbrace$
### Input

The input is given from Standard Input in the following format:

> $N$
> $A_1$ $\ldots$ $A_N$

### Output

If there is an integer sequence $x$ that satisfies all of the conditions in question, print `Yes`; otherwise, print `No`. In case of `Yes`, print the elements of such an integer sequence $x$ in the subsequent line, separated by spaces.

> $x_1$ $\ldots$ $x_N$

If multiple integer sequences satisfy the conditions, you may print any of them.

### Sample Input 1

> 5
> -1 1 -1 -1 1

### Sample Output 1

> Yes
> -3 -1 4 5 7

For this output, we have $\sum_{i=1}^NA_ix_i= -(-3) + (-1) - 4 - 5 + 7 = 0$.

### Sample Input 2

> 1
> -1

### Sample Output 2

> Yes
> 0

### Sample Input 3

> 2
> 1 -1

### Sample Output 3

> No

### 题解

这道题很明显是一道构造题，构造题解法往往有挺多的，不过只要能把答案凑出来就行。我的构造思路是：

- 直接用 $x_i=i$ 作为初始解，算出 $sum=\sum_{i=1}^N A_ix_i$
- 如果 $sum>0$ 那么想办法让 $sum$ 减小为 $0$
- 如果 $sum<0$ 那么想办法让 $sum$ 增大为 $0$

于是，解题核心就在于如何调整 $sum$ 的大小。

由于这两种情况完全对称，我们只以 $sum>0$，要减小 $sum$ 为例，另一种情况同理。

对于我们的初始解，有两种操作能保证操作后仍然符合严格单调递增：

- 将第 $k,k+1,\dots,N$ 位同时增大 $t$（$k,t\in\mathbb{Z}\ 且\ 1\leq k\leq N$）
- 将第 $1,2,\dots,k$ 位同时减小 $t$（$k,t\in\mathbb{Z}\ 且\ 1\leq k\leq N$）

那么我们就用这两种操作来控制 $sum$ 的大小。

对于第一种操作，若第 $k,k+1,\dots,N$ 位的 $-1$ 的数量比 $1$ 的数量正好多一个，那么我们将其增大 $t$，则 $sum$ 减小 $t$.

对于第二种操作，若第 $1,2,\dots,k$ 位的 $1$ 的数量比 $-1$ 的数量正好多一个，那么我们将其减小 $t$，则 $sum$ 减小 $t$.

这样，只要我们能找到满足两种操作其中一种的 $k$，我们就可以通过增大(减小)后缀(前缀)来任意调整 $sum$ 的大小，当然就可以将 $sum$ 调整为 $0$. 最后通过 $k,t$ 计算得到最终的解即可，即：
$$
1,2,\dots,k-1,k+t,k+1+t,\dots,N-1+t,N+t(第一种操作)\\
或者\\
1-t,2-t,\dots,k-1-t,k-t,k+1,\dots,N-1,N(第二种操作)
$$
如果我们两种操作都找不到满足条件的 $k$，那么就说明这种情况无解，输出 `No` 即可。

### 代码

```cpp
#include <bits/stdc++.h>
#define int long long
#define endl '\n'

using namespace std;

signed main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    int N;
    cin >> N;
    vector<int> A(N + 1);
    for (int i = 1; i <= N; i++)
        cin >> A[i];
    int sum = 0;
    for (int i = 1; i <= N; i++)
        sum += A[i] * i;
    if (sum > 0)
    {
        int cnt_posi = 0, cnt_nega = 0, pos = 0;
        for (int i = N; i >= 1; i--)
        {
            if (A[i] == 1)
                cnt_posi++;
            else
                cnt_nega++;
            if (cnt_nega > cnt_posi)
            {
                pos = i;
                break;
            }
        }
        if (pos)
        {
            cout << "Yes" << endl;
            for (int i = 1; i < pos; i++)
                cout << i << ' ';
            for (int i = pos; i <= N; i++)
                cout << i + sum << ' ';
            cout << endl;
            return 0;
        }
        cnt_posi = 0, cnt_nega = 0;
        for (int i = 1; i <= N; i++)
        {
            if (A[i] == 1)
                cnt_posi++;
            else
                cnt_nega++;
            if (cnt_posi > cnt_nega)
            {
                pos = i;
                break;
            }
        }
        if (pos)
        {
            cout << "Yes" << endl;
            for (int i = 1; i <= pos; i++)
                cout << i - sum << ' ';
            for (int i = pos + 1; i <= N; i++)
                cout << i << ' ';
            cout << endl;
            return 0;
        }
    }
    else
    {
        int cnt_posi = 0, cnt_nega = 0, pos = 0;
        for (int i = N; i >= 1; i--)
        {
            if (A[i] == 1)
                cnt_posi++;
            else
                cnt_nega++;
            if (cnt_nega < cnt_posi)
            {
                pos = i;
                break;
            }
        }
        if (pos)
        {
            cout << "Yes" << endl;
            for (int i = 1; i < pos; i++)
                cout << i << ' ';
            for (int i = pos; i <= N; i++)
                cout << i - sum << ' ';
            cout << endl;
            return 0;
        }
        cnt_posi = 0, cnt_nega = 0;
        for (int i = 1; i <= N; i++)
        {
            if (A[i] == 1)
                cnt_posi++;
            else
                cnt_nega++;
            if (cnt_posi < cnt_nega)
            {
                pos = i;
                break;
            }
        }
        if (pos)
        {
            cout << "Yes" << endl;
            for (int i = 1; i <= pos; i++)
                cout << i + sum << ' ';
            for (int i = pos + 1; i <= N; i++)
                cout << i << ' ';
            cout << endl;
            return 0;
        }
    }
    cout << "No" << endl;
    return 0;
}
```

