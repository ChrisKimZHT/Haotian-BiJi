**Codeforces Round 891 (Div. 3)**

F - Sum and Product

https://codeforces.com/contest/1857/problem/F

<!--more-->

4 seconds / 256 megabytes

standard input / standard output

## Problem

You have an array $a$ of length $n$.

Your task is to answer $q$ queries: given $x,y$, find the number of pairs $i$ and $j$ ($1 \le i < j \le n$) that both $a_i + a_j = x$ and $a_i \cdot a_j = y$.

That is, for the array $[1,3,2]$ and asking for $x=3,y=2$ the answer is $1$: 

-  $i=1$ and $j=2$ fail because $1 + 3 = 4$ and not $3,$ also $1 \cdot 3=3$ and not $2$; 
-  $i=1$ and $j=3$ satisfies both conditions; 
-  $i=2$ and $j=3$ fail because $3 + 2 = 5$ and not $3,$ also $3 \cdot 2=6$ and not $2$; 
## Input

The first line contains one integer $t$ ($1\le t\le 10^4$) — the number of test cases.

The second line of each test case contains one integer $n$ ($1 \le n \le 2\cdot 10^5$) — the length of the array $a$.

The third line of each test case contains $n$ integers $a_1,a_2,\dots,a_n$ ($1 \le |a_i| \le 10^9$) — array $a$.

The fourth line of each test case contains the integer $q$ ($1 \le q \le 2\cdot 10^5$) — the number of requests.

The next $q$ lines contain two numbers each $x$ and $y$ ($1 \le |x|\le 2\cdot 10^9,1\le |y|\le 10^{18}$) — request.

It is guaranteed that the sum of $n$ over all test cases does not exceed $2\cdot 10^5$. This is also guaranteed for the sum of $q$ values.

## Output

For each test case print a line with $q$ numbers — the answers to the queries.

## Example

Input

> 3
> 3
> 1 3 2
> 4
> 3 2
> 5 6
> 3 1
> 5 5
> 4
> 1 1 1 1
> 1
> 2 1
> 6
> 1 4 -2 3 3 3
> 3
> 2 -8
> -1 -2
> 7 12

Output

> 1 1 0 0 
> 6 
> 1 1 3 

## Note

For the first test case, let's analyze each pair of numbers separately: 

- pair $(a_1,a_2)$: $a_1 + a_2 = 4$, $a_1 \cdot a_2 = 3$ 

- pair $(a_1,a_3)$: $a_1 + a_3 = 3$, $a_1 \cdot a_3 = 2$ 

-  pair $(a_2,a_3)$: $a_2 + a_3 = 5$, $a_2 \cdot a_3 = 6$ 

From this, we can see that for the first query, the pair $(a_1,a_3)$ is suitable, for the second query, it is $(a_2,a_3)$, and there are no suitable pairs for the third and fourth queries.In the second test case, all combinations of pairs are suitable.

## 题解

给出 $x,y$，在一个数列中选择 $a,b$ 两个数，满足 $a+b=x,\; ab=y$，问有多少种选法。

首先联立两个限制，得到 $a,b$ 之间的关系：
$$
(a-b)^2=(a+b)^2-4ab=x^2-4y\geq0
$$
为方便，令 $x^2-4y=t$. 容易得到如果 $t<0$ 则原式不成立，没有任何 $a,b$ 满足条件。

如果 $t\geq0$，那么：
$$
|a-b|=\sqrt{t}
$$
不妨设 $a>b$，那么：
$$
\begin{cases}
a-b=\sqrt{t}\\
a+b=x
\end{cases}
$$
直接求解出一对合法的 $a,b$：
$$
\begin{cases}
a=\frac{1}{2}(x+\sqrt{t})\\
b=\frac{1}{2}(x-\sqrt{t})\\
\end{cases}
$$
当然，$a,b$ 如果不是整数也是不合法的，即要满足：$\sqrt{t}$ 是整数、$x\pm\sqrt{t}$ 是偶数。

对于给出的一对 $x,y$，能够直接求解出唯一解 $a,b$（或无解），那么就可以直接预先维护每个数的个数 $\text{cnt}(\cdot)$，最后直接输出：
$$
\text{cnt}(a)\cdot\text{cnt}(b)
$$
此时要注意一个细节，如果 $t=0$ 的时候，求解出来 $a=b$，那么答案就应该是：
$$
\frac{\text{cnt}(a)\cdot(\text{cnt}(a)-1)}{2}
$$

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

void solve()
{
    int n;
    cin >> n;
    vector<int> a(n + 10);
    map<int, int> mp;
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
        mp[a[i]]++;
    }
    int q;
    cin >> q;
    while (q--)
    {
        int x, y;
        cin >> x >> y;
        int t = x * x - 4 * y;
        if (t < 0)
        {
            cout << 0 << ' ';
        }
        else if (t == 0)
        {
            int cnt = mp[x / 2];
            cout << cnt * (cnt - 1) / 2 << ' ';
        }
        else // if (t > 0)
        {
            int st = sqrt(t);
            if (st * st != t)
            {
                cout << 0 << ' ';
                continue;
            }
            int a = x + st;
            int b = x - st;
            if (a & 1)
            {
                cout << 0 << ' ';
                continue;
            }
            int ca = mp[a / 2];
            int cb = mp[b / 2];
            cout << ca * cb << ' ';
        }
    }
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

