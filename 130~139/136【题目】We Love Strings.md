**2023牛客暑期多校训练营7**

I - We Love Strings

https://ac.nowcoder.com/acm/contest/57361/I

<!--more-->

## 题意

给定 $n$ 个仅含 `0,1,?` 的正则串，`?` 可以匹配一个 `0` 或 `1`，问有多少个 $01$ 串可以被至少一个正则串匹配。$1\leq n\leq400$，$\sum|s_i|\leq400$.

## 题解

这道题的突破口不在题目本身，而在数据范围上。正则串个数最多 $400$ 个，同时包含的总字符数最多 $400$ 个，这代表正则串多的时候，每个正则串的长度短；正则串长的时候，正则串数量少。（不过要注意的是同一个输入内，正则串不一定一样长）所以，可以使用两种不同的解法，取长补短过掉这题。

**暴力法**

如果 0/1 串长度为 $k$，那么对于 $\overbrace{0\dots0000}^{k个}\sim\overbrace{1\dots1111}^{k个}$ 的每个 0/1 串，逐一考察判断是否存在正则串 $s_i$ 与它匹配，若匹配则答案 $+1$.

暴力法时间复杂度为 $O(nk\cdot2^k)$. 当 $k$ 较小时可以接受，例如 $k=20$ 时数量级为 $4\cdot 10^8$.

**容斥原理法**

对于一个正则串 $s_i$，记能与它匹配的 0/1 串的集合为 $A_i$，那么根据[容斥原理](https://io.zouht.com/71.html)：
$$
{\displaystyle {\begin{aligned}\left|\bigcup _{i=1}^{n}A_{i}\right|={}&\sum _{i=1}^{n}|A_{i}|-\sum _{1\leq i<j\leq n}|A_{i}\cap A_{j}|+\sum _{1\leq i<j<k\leq n}|A_{i}\cap A_{j}\cap A_{k}|-\cdots +(-1)^{n-1}\left|A_{1}\cap \cdots \cap A_{n}\right|.\end{aligned}}}
$$
接下来的要解决的问题就是怎么统计每种**交**情况的集合数量了。

对于 $n$ 个正则串的集合 $S$，我们取一个子集 $T\subseteq S$，可以计算出能**同时**匹配 $T$ 中所有正则串的 0/1 串个数。计算方法为：

1. 初始化一个串 $M$ 为 $\overbrace{?\dots????}^{k个}$
2. 对于每个 $s_i\in T$，逐位考察：
   1. 如果 $s_{i,j}=0/1$，$M_j=\;?$：令 $M_j=s_{i,j}$.
   2. 如果 $s_{i,j}=\;?$，$M_j=0/1$：不操作
   3. 如果 $s_{i,j}=0/1$，$M_j=1/0$：发生矛盾！直接退出算法，匹配数为 $0$.
3. 统计串 $M$ 内的 $?$ 数目为 $t$
4. 能同时匹配 $T$ 中所有正则串的 0/1 串数目为：$2^t$

最后，对于元素个数为奇数的子集，加上它们的答案，对于元素个数为偶数的子集，减去它们的答案，便是最终答案了。

容斥原理法时间复杂度为 $O(nk\cdot2^n)$. 当 $n$ 较小时可以接受，例如 $n=20$ 时数量级为 $4\cdot 10^8$.

**两种方法结合**

可以发现暴力法时间复杂度关于 $k$ 呈指数上升，容斥原理法关于 $n$ 呈指数上升，那么取长补短：

- 当 $k\leq20$ 时，数据范围保证 $n\geq 20$，选择暴力法.
- 当 $k>20$ 时，数据范围保证 $n<20$，选择容斥原理法.

（时间复杂度算出来 $4\cdot10^8$ 级别看着很大，但是该题时间给到了 3s，并且实际跑出来耗时在 200ms 内，还是很充裕的）

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

constexpr int MOD = 998244353;

// 暴力 Brute Force
int brute_force(vector<string> &arr)
{
    int len = arr.front().length();
    int siz = arr.size();
    int res = 0;
    for (int i = 0; i < (1 << len); i++)
    {
        for (int j = 0; j < siz; j++)
        {
            bool flag = true;
            for (int k = 0; k < len; k++)
            {
                if (arr[j][k] == '?')
                    continue;
                if (arr[j][k] != (i >> k & 1) + '0')
                {
                    flag = false;
                    break;
                }
            }
            if (flag)
            {
                res++;
                break;
            }
        }
    }
    return res;
}

// 容斥原理 Include-Exclude Principle
int inc_exc(vector<string> &arr)
{
    int len = arr.front().length();
    int siz = arr.size();
    int res = 0;
    for (int i = 1; i < (1 << siz); i++)
    {
        bool flag = true;
        string match(len, '?');
        for (int j = 0; flag && j < siz; j++)
        {
            if ((i >> j & 1) == 0)
                continue;
            for (int k = 0; k < len; k++)
            {
                if (match[k] == '0' && arr[j][k] == '1' ||
                    match[k] == '1' && arr[j][k] == '0')
                {
                    flag = false;
                    break;
                }
                if (match[k] == '?')
                    match[k] = arr[j][k];
            }
        }
        if (!flag)
            continue;
        int tmp = 1;
        for (int j = 0; j < len; j++)
            if (match[j] == '?')
                tmp = (tmp * 2) % MOD;
        bool op = __builtin_popcount(i) & 1; // odd+ even-
        if (op)
            res = (res + tmp) % MOD;
        else
            res = (res - tmp + MOD) % MOD;
    }
    return res;
}

void solve()
{
    int n;
    cin >> n;
    map<int, vector<string>> mp;
    for (int i = 1; i <= n; i++)
    {
        string s;
        cin >> s;
        mp[s.length()].emplace_back(s);
    }
    int ans = 0;
    for (auto &[len, arr] : mp)
    {
        if (len <= 20)
            ans = (ans + brute_force(arr)) % MOD;
        else
            ans = (ans + inc_exc(arr)) % MOD;
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

