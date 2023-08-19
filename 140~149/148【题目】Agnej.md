**“范式杯”2023牛客暑期多校训练营10**

D - Agnej

https://ac.nowcoder.com/acm/contest/57364/D

<!--more-->

## 题意

有个 $n+1$ 层的木塔，每层原本由 $m$ 根木头构成。现在除了最顶层是满的 $m$ 根木头，其余每层有些木头已经被抽走了。先后手轮流从非顶层抽走一根木头，如果一层中最左侧或最右侧 $\lceil\frac{m}{2}\rceil$ 根木头都被抽走，则木塔倒塌。谁让木塔倒塌谁输，问胜负。$1\leq n\times m\leq 10^5$。

## 题解

显然每一层独立，因此每一层都是一个有向图游戏，那么根据 SG 定理，对每一个层求出 SG 值再异或到一起，如果答案 $\neq0$ 则先手必胜，$0$ 则先手必败。下面仅考虑求解一层的 SG 值。

#### $m$ 为偶数

为方便，记左侧有 $l$ 块，右侧有 $r$ 块。

要求 $SG(l,r)$，则考察其子情况。考察到左右仅一块的边界情况，此时先手必败，即 $SG(1,1)=0$.

每经过一次传递 SG 值会 $0/1$ 切换，那么 $SG(l,r)=(l+r)\bmod2$.

#### $m$ 为奇数

奇数情况的唯一特殊点就是最中心的一块。

为方便，中心有 $c$ 块（$c=0/1$），记中心左侧有 $l$ 块，右侧有 $r$ 块。且下面的分类讨论中，不妨默认 $l$ 取特殊值。

1. 当 $c=0$ 时，退化为偶数情况，答案 $SG(l,0,r)=(l+r)\bmod2$.

2. 当 $c=1$ 时，需要分类讨论：

   1. 有一侧为空 $SG(0,1,r)$ 时，考虑边界情况 $SG(0,1,0)$：

      - $SG(0,1,0)=0$，每经过一次传递 SG 值会 $0/1$ 切换，那么 $SG(0,1,r)=r\bmod2$.

   2. 有一侧仅一块 $SG(1,1,r)$ 时，考虑边界情况 $SG(1,1,1)$：

      1. 抽中间一块，转移为情况 $1$，SG 值 $(1+r)\bmod2$.
      2. 抽 $=1$ 的一侧，转移为情况 $2.1$，SG 值 $r\bmod 2$.
   
      - 情况 $2.2.1$ 和 $2.2.2$ 的 SG 值一定包含 $0$ 和 $1$，则 $SG(1,1,1)=2$，那么每经过一次传递 SG 值会 $2/3$ 切换，那么 $SG(1,1,r)=2+(r+1)\bmod 2$.
   
   3. 一般情况 $SG(l,1,r)$ 时，考虑边界情况 $SG(2,1,r)$：
   
      1. 抽中间一块，转移为情况 $1$， SG 值 $r\bmod2$.
   
      2. 抽左侧一块，转移为情况 $2.2$，SG 值为 $2+(r+1)\bmod 2$
   
      3. 抽右侧一块，由于 $SG(2,1,1)=3$，考虑边界情况 $SG(2,1,2)=1$，$SG(2,1,3)=0$，$\dots$
   
      - 经过归纳得到，$SG(2,1,r)=(l+1+r)\bmod 2$.

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
// #define int long long

using namespace std;

int sg(string &s)
{
    int len = s.size();
    int mid = len / 2;
    int cnt_l = 0, cnt_r = 0;
    for (int i = 0; i <= mid - 1; i++)
        if (s[i] == '1')
            cnt_l++;
    for (int i = mid; i < len; i++)
        if (s[i] == '1')
            cnt_r++;
    if (len % 2)
    {
        cnt_r -= s[mid] - '0';
        if (s[mid] == '0')
            return (cnt_l + cnt_r) % 2;
        if (cnt_l == 0 || cnt_r == 0)
            return max(cnt_l, cnt_r) % 2;
        if (cnt_l == 1 || cnt_r == 1)
            return 2 + (max(cnt_l, cnt_r) + 1) % 2;
        return (cnt_l + cnt_r + 1) % 2;
    }
    return (cnt_l + cnt_r) % 2;
}

void solve()
{
    int n, m;
    cin >> n >> m;
    int ans = 0;
    while (n--)
    {
        string s;
        cin >> s;
        ans ^= sg(s);
    }
    if (ans)
        cout << "Alice" << endl;
    else
        cout << "Bob" << endl;
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

