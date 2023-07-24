**Codeforces Round 852 (Div. 2)**

D. Moscow Gorillas

https://codeforces.com/problemset/problem/1793/D

<!--more-->

2 seconds / 256 megabytes

standard input / standard output

## Problem

In winter, the inhabitants of the Moscow Zoo are very bored, in particular, it concerns gorillas. You decided to entertain them and brought a permutation $p$ of length $n$ to the zoo.

A permutation of length $n$ is an array consisting of $n$ distinct integers from $1$ to $n$ in any order. For example, $[2,3,1,5,4]$ is a permutation, but $[1,2,2]$ is not a permutation ($2$ occurs twice in the array) and $[1,3,4]$ is also not a permutation ($n=3$, but $4$ is present in the array).

The gorillas had their own permutation $q$ of length $n$. They suggested that you count the number of pairs of integers $l, r$ ($1 \le l \le r \le n$) such that $\operatorname{MEX}([p_l, p_{l+1}, \ldots, p_r])=\operatorname{MEX}([q_l, q_{l+1}, \ldots, q_r])$.

The $\operatorname{MEX}$ of the sequence is the minimum integer positive number missing from this sequence. For example, $\operatorname{MEX}([1, 3]) = 2$, $\operatorname{MEX}([5]) = 1$, $\operatorname{MEX}([3, 1, 2, 6]) = 4$.

You do not want to risk your health, so you will not dare to refuse the gorillas.

## Input

The first line contains a single integer $n$ ($1 \le n \le 2 \cdot 10^5$) — the permutations length.

The second line contains $n$ integers $p_1, p_2, \ldots, p_n$ ($1 \le p_i \le n$) — the elements of the permutation $p$.

The third line contains $n$ integers $q_1, q_2, \ldots, q_n$ ($1 \le q_i \le n$) — the elements of the permutation $q$.

## Output

Print a single integer — the number of suitable pairs $l$ and $r$.

## Examples

**Input**

> 3
> 1 3 2
> 2 1 3

**Output**

> 2

**Input**

> 7
> 7 3 6 2 1 5 4
> 6 7 2 5 3 1 4

**Output**

16

**Input**

> 6
> 1 2 3 4 5 6
> 6 5 4 3 2 1

**Output**

> 11

## Note

In the first example, two segments are correct – $[1, 3]$ with $\operatorname{MEX}$ equal to $4$ in both arrays and $[3, 3]$ with $\operatorname{MEX}$ equal to $1$ in both of arrays.

In the second example, for example, the segment $[1, 4]$ is correct, and the segment $[6, 7]$ isn't correct, because $\operatorname{MEX}(5, 4) \neq \operatorname{MEX}(1, 4)$.

## 题解

首先，我们从 $1$ 开始，维护完整包含了 $1\sim x$ 的区间的左右端点 $p_{\min},p_{\max}$ ($p_{\min}\leq p_{\max}$). 维护方法也简单，如果要从 $x$ 向后递推到 $x+1$，则先找到 $p,q$ 序列里 $x+1$ 这个数在哪，记靠左的位置为 $mn$，靠右的位置为 $mx$ ($mn\leq mx$)，然后更新：

- $p_{\min}=\min\{p_{\min},mn\}$
- $p_{\max}=\min\{p_{\max},mx\}$

我们找区间时，也是从 $\operatorname{MEX}$ 值为 $1$ 的开始向上找。如果我们现在已经计算出了 $\operatorname{MEX}$ 值为 $1\sim x$ 的数目，接下来我们找 $\operatorname{MEX}$ 值为 $x+1$ 的区间数目。

首先我们找到 $p,q$ 序列里 $x+1$ 这个数在哪，记为 $mn,mx$. 接下来，我们对 $mn,mx$ 与 $p_{\min},p_{\max}$ 的位置进行分类讨论：

- **情况一：**$p_{\max}<mn$

这种情况的例子如下（为了示例更清晰，无关信息用点代替。第一行为下标）：

$x=1$，$p_{\min}=2$，$p_{\max}=3$，$mn=5$，$mx=7$.

```
i | 1 2 3 4 5 6 7 8
-------------------
p | . 1 . . . . 2 .
q | . . 1 . 2 . . .
```

若一个区间的 $\operatorname{MEX}$ 值为 $x+1$，那么它一定要包含 $[p_{\min},p_{\max}]$ 这个区间，并且它不能包含 $[mn,mx]$ 这个区间（因为一旦包含了，那 $\operatorname{MEX}$ 就一定 $\geq x+1$ 了）。

所以满足这种情况的区间数为：$p_{\min}\cdot(mn-p_{\max})$，上面示例计算出来为 $2\cdot 2=4$.

- **情况二：**$mx<p_{\min}$

这种情况的例子如下：

$x=1$，$p_{\min}=6$，$p_{\max}=7$，$mn=2$，$mx=3$.

```
i | 1 2 3 4 5 6 7 8
-------------------
p | . . 2 . . 1 . .
q | . 2 . . . . 1 .
```

满足这种情况的区间数为：$(p_{\min}-mx)\cdot(n-p_{\max}+1)$，上面示例计算出来为 $3\cdot 2=6$.

- **情况三：**$mn<p_{\min}$ 且 $p_{\max}<mx$

这种情况非常容易漏掉，导致这道题后面的测试点过不去，例子如下：

$x=1$，$p_{\min}=4$，$p_{\max}=5$，$mn=2$，$mx=8$.

```
i | 1 2 3 4 5 6 7 8
-------------------
p | . 2 . 1 . . . .
q | . . . . 1 . . 2
```

由于这种情况 $[p_{\min},p_{\max}]$ 被包含在 $[mn,mx]$ 里面了，若一个区间 $[l,r]$ 的 $\operatorname{MEX}$ 值为 $x+1$，只需要保证 $mn<l\leq p_{\min}$ 且 $p_{\max}\leq r<mx$ 即可。

满足这种情况的区间数为：$(p_{\min}-mn)\cdot(mx-p_{\max})$，上面示例计算出来为 $2\cdot 3=6$.

- **其他情况**

除了上述三种情况，其他情况均不成立，即答案是 $0$. 具体为什么不成立可以举几个例子看。

------

上面是从 $x$ 到 $x+1$ 的思路，那么首先需要初始化一个初值。由于初始时，不存在上一个数的限制，即不存在 $p_{\min},p_{\max}$ 的限制，因此初始化时，上述三种情况均成立。

另外还需要额外 $+1$，代表取全部序列 $\operatorname{MEX}=n+1$ 的情况（当然这个不一定非要写到初始化里，也能在后面加上 $1$）.

找到 $p,q$ 序列里 $1$ 这个数的位置，记靠左的位置为 $mn$，靠右的位置为 $mx$. 那么初始情况为：
$$
\begin{align}
ans_{\text{init}}=&\frac{1}{2}(n - mx)(n - mx + 1)\\
+&\frac{1}{2}(mn - 1)mn\\
+&\frac{1}{2}(mx - mn - 1)(mx - mn)\\
+&1
\end{align}
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
    vector<int> rp(n + 10), rq(n + 10);
    for (int i = 1; i <= n; i++)
    {
        int t;
        cin >> t;
        rp[t] = i;
    }
    for (int i = 1; i <= n; i++)
    {
        int t;
        cin >> t;
        rq[t] = i;
    }
    int pmin = min(rp[1], rq[1]), pmax = max(rp[1], rq[1]);
    int ans = (n - pmax) * (n - pmax + 1) / 2 +
              (pmin - 1) * pmin / 2 +
              (pmax - pmin - 1) * (pmax - pmin) / 2 + 1;
    for (int i = 2; i <= n; i++)
    {
        int mn = min(rp[i], rq[i]);
        int mx = max(rp[i], rq[i]);
        if (pmax < mn)
            ans += (mn - pmax) * pmin;
        else if (mx < pmin)
            ans += (pmin - mx) * (n - pmax + 1);
        else if (mn < pmin && mx > pmax)
            ans += (pmin - mn) * (mx - pmax);
        pmax = max(pmax, mx);
        pmin = min(pmin, mn);
        if (pmax == n && pmin == 1)
            break;
    }
    cout << ans << endl;
}

signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    solve();
    return 0;
}
```

