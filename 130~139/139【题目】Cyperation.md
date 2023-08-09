**2023牛客暑期多校训练营7**

G - Cyperation

https://ac.nowcoder.com/acm/contest/57361/G

<!--more-->

## 题意

给定长度为 $n$ 的环 $\{a\}_{i=1}^{n}$，每次可以选择环上最近距离为 $k$ 的两点 $i,j$ 使得 $a_i$ 和 $a_j$ 都减 $1$。问能不能经过若干次操作让整个环上的数字清零。多测，$1\leq T\leq 10^6$，$2\leq \sum n \leq 10^6$，$1\leq k\leq n$。

## 题解

特判全 $0$ 情况，直接输出 `YES`.

特判 $2k>n$ 的情况，直接输出 `NO`.（这种情况 $\min(t,n-t)=k$ 无解所以不成立）

------

对于给出的环 $\{a\}_{i=1}^{n}$，将距离为 $k$ 的所有点都提取出来放到一起，相当于将原来的大环拆成若干个小环。每个小环互相独立，可以分别求解。要注意的是，拆分出的小环不一定顺序和以前一样，因为要保证相邻两位在原大环中距离为 $k$。

当且仅当**所有**小环能够有解时，原问题有解。

例如输入 $[1,4,6,2,8,10]$ 且 $k=2$，那么将会拆分成 $[1,6,8]$ 和 $[4,2,10]$ 两个小环。

接下来就要对每个小环求解了，由于已经进行了拆分，小环中每次操作相当于就是相邻两位一起 $-1$ 了。

------

对于一个小环 $[a_1,a_2,\dots,a_n]$，设如果要将整个环清零，需要对 $[a_1,a_2]$ 这个区间进行 $x$ 次减操作，那么：

- $a_1$ 变成了 $a_1-x$
- $a_2$ 经过上一步变成了 $a_2-x$，还要对 $[a_2,a_3]$ 区间进行 $a_2-x$ 次减操作才能置零。
- $a_3$ 经过上一步变成了 $a_3-a_2+x$，还要对 $[a_3,a_4]$ 区间进行 $a_3-a_2+x$ 次减操作才能置零。
- $a_4$ 经过上一步变成了 $a_4-(a_3-a_2)-x$，还要对 $[a_4,a_5]$ 区间进行 $a_4-(a_3-a_2)-x$ 次减操作才能置零。
- $\vdots$
- $a_n$ 经过上一步变成了 $a_n-(a_{n-1}-(a_{n-2}-(\cdots\cdots)))+(-1)^{n+1}x$，还要对 $[a_n,a_1]$ 区间进行 $(左边那个式子)$ 次减操作才能置零。
- $a_1$ 经过上一步变成了 $a_1-(上面那个式子)$

综上，如果假设了 $[a_1,a_2]$ 区间的操作次数 $x$，剩下区间的操作次数全部都确定了，且均是一次式 $p\pm x$ 这种格式。

为了方便起见，令 $p_i=a_i-p_{i-1},\;i=2,3,\dots$，由于序列是环，特别的令 $p_1=a_1-p_n$.

首先由于操作次数 $\geq0$，所以 $x$ 需要满足：
$$
\begin{cases}
a_1-x\geq0\\
p_2-x\geq0\\
p_3+x\geq0\\
\vdots\\
p_n+(-1)^{n-1}x\geq0\\
p_1+(-1)^{n}x\geq0
\end{cases}
$$
整合一下就是：
$$
l\leq x\leq r\\
其中:
\begin{cases}
l = \max\{0,-p_3,-p_5,-p_7,\dots\}\\
r = \min\{a_1,p_2,p_4,p_6,\dots\}
\end{cases}
$$
如果求出来 $l>r$ 的话很明显 $x$ 无解，直接输出 `NO`.

对于 $l\leq r$ 的情况，也不一定有解，根据上面可以发现，我们假设了对 $[a_1,a_2]$ 操作了 $x$ 次，最后会算出来对 $[a_n,a_1]$ 的操作次数，这两个操作对 $a_1$ 的总操作次数不一定能将 $a_1$ 置零。用公式来说就是，要满足：
$$
(a_1-x)+(p_1+(-1)^n x)=a_1
$$
由于 $x$ 的符号与 $n$ 有关，所以需要分类讨论。

若 $n$ 为偶数，化简后就是：
$$
p_1=0
$$
直接检查就行，不符合则不成立输出 `NO`. （有些题解是 $p_n=a_1$，这俩其实是一样的，因为 $p_1=a_1-p_n$）

若 $n$ 为奇数，化简后就是：
$$
2x=p_1
$$
首先检测是否有整数解（即 $p$ 是否为偶数），若解出 $x$，回代到 $l\leq x\leq r$ 进行验证，若验证通过则有解。

## 代码

代码中为方便取模下标从 $0$ 开始，但思路和公式完全一致。

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

bool check(vector<int> &a)
{
    int n = a.size();
    vector<int> p(n);
    for (int i = 0; i < n; i++)
        p[(i + 1) % n] = a[(i + 1) % n] - p[i];
    bool op = true; // 1+ 0-
    int l = 0, r = a.front();
    for (int i = 0; i < n; i++)
    {
        if (op)
            l = max(l, -p[i]);
        else
            r = min(r, p[i]);
        if (l > r)
            return false;
        op ^= 1;
    }
    if (n & 1)
    {
        if (p.front() & 1)
            return false;
        int ans = p.front() / 2;
        return l <= ans && ans <= r;
    }
    return p.front() == 0;
}

void solve()
{
    int n, k;
    cin >> n >> k;
    vector<int> a(n);
    for (int i = 0; i < n; i++)
        cin >> a[i];
    bool all_zero = true;
    for (int i = 0; i < n; i++)
    {
        if (a[i])
        {
            all_zero = false;
            break;
        }
    }
    if (all_zero)
    {
        cout << "YES" << endl;
        return;
    }
    if (2 * k > n)
    {
        cout << "NO" << endl;
        return;
    }
    vector<bool> vis(n + 10);
    for (int i = 0; i < n; i++)
    {
        if (vis[i])
            continue;
        vector<int> ring;
        int pos = i;
        while (!vis[pos])
        {
            vis[pos] = true;
            ring.push_back(a[pos]);
            pos = (pos + k) % n;
        }
        if (!check(ring))
        {
            cout << "NO" << endl;
            return;
        }
    }
    cout << "YES" << endl;
    return;
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

