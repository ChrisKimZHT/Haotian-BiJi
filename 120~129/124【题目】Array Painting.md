**Educational Codeforces Round 152 (Rated for Div. 2)**

D. Array Painting

https://codeforces.com/contest/1849/problem/D

<!--more-->

2 seconds / 256 megabytes

standard input / standard output

## Problem

You are given an array of $n$ integers, where each integer is either $0$, $1$, or $2$. Initially, each element of the array is blue.

Your goal is to paint each element of the array red. In order to do so, you can perform operations of two types:

-  pay one coin to choose a blue element and paint it red; 
-  choose a red element which is not equal to $0$ and a blue element adjacent to it, decrease the chosen red element by $1$, and paint the chosen blue element red. 
What is the minimum number of coins you have to spend to achieve your goal?

## Input

The first line contains one integer $n$ ($1 \le n \le 2 \cdot 10^5$).

The second line contains $n$ integers $a_1, a_2, \dots, a_n$ ($0 \le a_i \le 2$).

## Output

Print one integer — the minimum number of coins you have to spend in order to paint all elements red.

## Examples

**Input**

> 3
> 0 2 0

**Output**

> 1

**Input**

> 4
> 0 0 1 1

**Output**

> 2

**Input**

> 7
> 0 1 0 0 1 0 2

**Output**

> 4

## Note

In the first example, you can paint all elements red with having to spend only one coin as follows:

1. paint the $2$-nd element red by spending one coin;
2. decrease the $2$-nd element by $1$ and paint the $1$-st element red;
3. decrease the $2$-nd element by $1$ and paint the $3$-rd element red.

In the second example, you can paint all elements red spending only two coins as follows:

1. paint the $4$-th element red by spending one coin;
2. decrease the $4$-th element by $1$ and paint the $3$-rd element red;
3. paint the $1$-st element red by spending one coin;
4. decrease the $3$-rd element by $1$ and paint the $2$-nd element red.

## 题解

首先，对于不为 $0$ 的位，肯定是优先考虑的，因为对于它们，首先使用一个硬币“激活”为红色后，可以免费将旁边 $1$ 或 $2$ 个块转变为红色。如果旁边也是不为 $0$ 的块，有可能还发生链式反应。可将链式反应分成三类：

- 第 $i$ 位为 $0$：花一个硬币“激活”为后，只有第 $i$ 位变红，总区间为 $[i,i]$。
- 第 $i$ 位为 $1$：花一个硬币“激活”为后，除了第 $i$ 位变红还有：
  - 如果选择左侧，则从第 $i-1$ 位开始到左侧第一个 $0$ 结束都能链式变红，总区间为 $[左侧第一个0,i]$.
  - 如果选择右侧，则从第 $i+1$ 位开始到右侧第一个 $0$ 结束都能链式变红，总区间为 $[i,右侧第一个0]$.
- 第 $i$ 位为 $2$：花一个硬币“激活”为后，除了第 $i$ 位变红还有：
  - 对于左侧，从第 $i-1$ 位开始到左侧第一个 $0$ 结束都能链式变红。
  - 对于右侧，从第 $i+1$ 位开始到右侧第一个 $0$ 结束都能链式变红。
  - 总区间为 $[左侧第一个0,右侧第一个0]$

从左到右扫描一遍每个点，将每个点能够变红的区间储存下来，对于每个区间，使用 $1$ 个硬币即可变红。接下来使用贪心思想取区间即可，贪心思想就是如果要将第 $i$ 个点变红，则区间的左端点必须满足 $l\leq i$. 为了节省硬币，我们希望在左端点满足条件的区间内，选择右端点 $r$ 最大的区间，然后将 $i$ 更新为 $r+1$.

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
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    vector<int> pre(n + 10), nxt(n + 10);
    for (int i = 1; i <= n; i++)
    {
        if (a[i - 1])
            pre[i] = pre[i - 1];
        else
            pre[i] = i - 1;
    }
    for (int i = n; i >= 1; i--)
    {
        if (a[i + 1])
            nxt[i] = nxt[i + 1];
        else
            nxt[i] = i + 1;
    }
    // for (int i = 1; i <= n; i++)
    //     cout << pre[i] << " \n"[i == n];
    // for (int i = 1; i <= n; i++)
    //     cout << nxt[i] << " \n"[i == n];
    vector<pair<int, int>> pv;
    for (int i = 1; i <= n; i++)
    {
        if (a[i] == 0)
        {
            pv.push_back({i, i});
        }
        else if (a[i] == 1)
        {
            pv.push_back({i, nxt[i]});
            pv.push_back({pre[i], i});
        }
        else // if (a[i] == 2)
        {
            pv.push_back({pre[i], nxt[i]});
        }
    }
    sort(pv.begin(), pv.end());
    int ans = 0, idx = 0;
    for (int i = 1; i <= n; i++)
    {
        int l = -1, r = -1;
        for (; idx < pv.size(); idx++)
        {
            if (!(l == -1 || pv[idx].first <= i))
                break;
            l = pv[idx].first;
            r = pv[idx].second;
        }
        ans += max(0LL, l - i);
        ans++;
        i = r;
    }
    cout << ans << endl;
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

