**VK Cup 2015 - Round 2 (unofficial online mirror, Div. 1 only)**

B. Work Group

https://codeforces.com/problemset/problem/533/B

<!--more-->

2 seconds / 256 megabytes

standard input / standard output

## Problem

One Big Software Company has n employees numbered from 1 to n. The director is assigned number 1. Every employee of the company except the director has exactly one immediate superior. The director, of course, doesn't have a superior.

We will call person a a subordinates of another person b, if either b is an immediate supervisor of a, or the immediate supervisor of a is a subordinate to person b. In particular, subordinates of the head are all other employees of the company.

To solve achieve an Important Goal we need to form a workgroup. Every person has some efficiency, expressed by a positive integer ai, where i is the person's number. The efficiency of the workgroup is defined as the total efficiency of all the people included in it.

The employees of the big software company are obsessed with modern ways of work process organization. Today pair programming is at the peak of popularity, so the workgroup should be formed with the following condition. Each person entering the workgroup should be able to sort all of his subordinates who are also in the workgroup into pairs. In other words, for each of the members of the workgroup the number of his subordinates within the workgroup should be even.

Your task is to determine the maximum possible efficiency of the workgroup formed at observing the given condition. Any person including the director of company can enter the workgroup.

## Input

The first line contains integer n (1 ≤ n ≤ 2·105) — the number of workers of the Big Software Company. 

Then n lines follow, describing the company employees. The i-th line contains two integers pi, ai (1 ≤ ai ≤ 105) — the number of the person who is the i-th employee's immediate superior and i-th employee's efficiency. For the director p1 =  - 1, for all other people the condition 1 ≤ pi < i is fulfilled.

## Output

Print a single integer — the maximum possible efficiency of the workgroup.

## Examples

Input

> 7
> -1 3
> 1 2
> 1 1
> 1 4
> 4 5
> 4 3
> 5 2

## Output

> 17

## Note

In the sample test the most effective way is to make a workgroup from employees number 1, 2, 4, 5, 6.

## 题解

**典型错解**

这道题非常容易漏掉一些特殊情况，写出贪心思想的 DFS 解法最后 WA 掉。贪心的思想是：

- 如果子树有偶数个，则全部取掉。这样子树的子节点数量是奇数，加上自身满足偶数。
- 如果子树有奇数个，则最小的那个不取。这样子树的子节点数量是奇数，加上自身满足偶数。

WA 在第 7 个测试点的特殊情况是：

```
     1
   / | \
100 101 102
```

这种情况，其实可以不取那个权值为 $1$ 的，取剩下 $3$ 个也符合条件。即正确答案是 $303$ 而不是 $204$.

WA 在第 8 个测试点的特殊情况是：

```
   100
    |
    1
   / \
100   100
```

这种情况，其实可以不取那个权值为 $1$ 的，取剩下 $3$ 个也符合条件。即正确答案是 $300$ 而不是 $201$.

综上，贪心法解这道题还是有很多漏洞的，不是正确解法。

**正确解法：树上 DP**

上面错解的贪心法取子树时，只能取偶数个，这实际上是不对的。取奇数个子树仍然可以是正确的（上方测试点 7），不取某个子树的根节点也可以是正确的（上方测试点 7, 8）. 因此得对取法进行分类讨论，用动态规划来解决。

状态表示：$dp_{i,j}:=$ 根节点为 $i$ 的子树中，满足题目限制的取法中，节点数为 $j=0$ 偶数 / $j=1$ 奇数时，最大权值的值。（注意这里的节点数包含子树的根节点）

状态计算：

奇偶性的变化规律如下：

- 奇+奇=偶
- 偶+偶=偶
- 奇+偶=奇
- 偶+奇=奇

因此：（其中 $u$ 代表当前节点，$v$ 代表它的子节点）

- $dp_{u,0}=\max\{dp_{u,0}+dp_{v,0},dp_{u,1}+dp_{v,1}\}$
- $dp_{u,1}=\max\{dp_{u,1}+dp_{v,0},dp_{u,0}+dp_{v,1}\}$

最后还需要注意的一点是，根节点 $u$ 可以选择取或者不取。如果不取的话，奇数和偶数都满足条件（因为压根就没有这个节点限制了）。如果取的话，只有子树节点数为偶数能成立，加上这个根节点，一共节点数为奇数：

- $dp_{u,1}=\max\{dp_{u,1},dp_{u,0}+a_u\}$

另外要注意的细节就是，$dp_{u,1}$ 需要初始化为 $-\infin$，因为不取任何节点的初始情况时，节点数为偶数 $0$，不可能出现为奇数的情况。

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

const int MAXN = 5e5 + 10;
int e[MAXN], ne[MAXN], h[MAXN], cnt;
int a[MAXN];
int dp[MAXN][2];

void add(int x, int y)
{
    e[cnt] = y;
    ne[cnt] = h[x];
    h[x] = cnt++;
}

void dfs(int now)
{
    dp[now][1] = INT64_MIN;
    for (int i = h[now]; i != -1; i = ne[i])
    {
        int &nxt = e[i];
        dfs(nxt);
        int now1 = dp[now][1], now0 = dp[now][0];
        dp[now][0] = max(dp[nxt][1] + now1, dp[nxt][0] + now0);
        dp[now][1] = max(dp[nxt][1] + now0, dp[nxt][0] + now1);
    }
    dp[now][1] = max(dp[now][1], dp[now][0] + a[now]);
}

void solve()
{
    int n;
    cin >> n;
    for (int i = 1; i <= n; i++)
    {
        int p;
        cin >> p >> a[i];
        if (p == -1)
            continue;
        add(p, i);
    }
    dfs(1);
    cout << max(dp[1][0], dp[1][1]) << endl;
}

signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    memset(h, -1, sizeof(h));
    solve();
    return 0;
}
```

