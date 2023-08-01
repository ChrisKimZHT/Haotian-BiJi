**2023牛客暑期多校训练营4**

H - Merge the squares!

https://ac.nowcoder.com/acm/contest/57358/H

<!--more-->

## 题意

给定 $n \times n$ 个 $1 \times 1$ 组成的正方形，每次可以合并相邻不超过 $50$ 个正方形变成一个大正方形。问如何通过合并得到一个大的 $n \times n$ 的大正方形，不限次数。 $1 \leq n \leq 10^3$ 。

## 题解

这道题是一道构造题，重点是找到一个通用的构造方法。

对于一个边长为 $c$ 的正方形，可以很简单地分成两个小正方形和两个小长方形，小正方形的边长为 $a,b$，面积关系满足：
$$
c^2=(a+b)^2=a^2+b^2+2ab
$$
<img src="https://assets.zouht.com/img/note/128-01.webp" style="zoom: 15%;" />

对于分出来的正方形，可以一直递归下去解决。先假定递归能够成功回来，那么边长为 $a,b$ 的小正方形就已经合好了，在这个区域内只占 $2$ 个合并名额。一次性最多合并的数量是 $50$ 个，那么分给旁边两个小长方形的合并名额便是每个 $24$ 个。

接下来的问题就是，如何判定能在 $24$ 个名额中合并好两个小长方形。这个可以用辗转相除法（欧几里得算法）解决：

<img src="https://assets.zouht.com/img/note/128-02.webp" style="zoom:33%;" />

先用宽做边长，切出几个正方形，再用剩下的长方形的宽做边长，切出几个正方形，一直这样下去直到全部切成正方形，最后把数目统计出来即可。如果切出的数目 $>24$，则说明不成立，如果切出的数目 $\leq 24$，则说明成立。

那对于一个边长为 $n$ 的大正方形，有多种切法，那该选择哪一种呢。由于欧几里得算法的时间复杂度为：$O(a+b)$，$a,b$ 为输入的两个数，那么可以选择预处理遍历所有的切割情况，记录下合法的切法。这个时间复杂度 $O(n^2)$ 可以接受。

预处理好后就可以开始正式计算了，使用递归分治的思想。对于边长为 $n$ 的正方形：

- 如果 $n=1$：结束算法
- 如果 $n>1$：在预处理的表中查询到正确切法 $a,b$
  - 如果查到可以直接合并
    - 储存合并方式后直接结束
  - 如果查到需要切割
    - 储存合并方式
    - 递归处理左上 $a\times a$ 正方形
    - 递归处理右下 $b\times b$ 正方形
    - 递归处理左下 $b\times a$ 长方形
    - 递归处理右上 $a\times b$ 长方形

对于边长 $a,b$ 的长方形（$a>b$）：

- 如果边长为 $0$：结束算法
- 如果边长都不为 $0$：
  - 从切出的第 $1$ 个正方形遍历到第 $\lfloor a/b\rfloor$ 个正方形：
    - 递归处理每一个正方形
  - 递归处理剩下的长方形 $b,a\bmod b$

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
// #define int long long

using namespace std;

constexpr int MAXN = 1010;
int split[MAXN];
vector<array<int, 3>> res;

// a-lu b-rd
bool check_split(int a, int b)
{
    if (!b)
        return a <= 7;
    int ans = 0;
    while (b)
    {
        ans += a / b;
        a %= b;
        swap(a, b);
    }
    return ans <= 24;
}

void init_split(int n)
{
    for (int i = 1; i <= n; i++)
    {
        for (int j = 0; j <= i / 2; j++)
        {
            // i - j >= j
            if (check_split(i - j, j))
            {
                split[i] = j;
                break;
            }
        }
    }
    // for (int i = 1; i <= n; i++)
    //     cout << split[i] << " \n"[i == n];
}

void dfs(int x, int y, int a);
void dfs_hori(int x, int y, int r, int c);
void dfs_vert(int x, int y, int r, int c);

void dfs_hori(int x, int y, int r, int c)
{
    if (r < 1 || c < 1)
        return;
    int cnt = c / r;
    for (int i = 0; i < cnt; i++)
        dfs(x, y + i * r, r);
    dfs_vert(x, y + cnt * r, r, c % r);
}

void dfs_vert(int x, int y, int r, int c)
{
    if (r < 1 || c < 1)
        return;
    int cnt = r / c;
    for (int i = 0; i < cnt; i++)
        dfs(x + i * c, y, c);
    dfs_hori(x + cnt * c, y, r % c, c);
}

void dfs(int x, int y, int a)
{
    if (a == 1)
        return;
    res.push_back({x, y, a});
    if (split[a] == 0)
        return;
    int lu = a - split[a], rd = split[a];
    dfs(x, y, lu);
    dfs(x + lu, y + lu, rd);
    dfs_hori(x + lu, y, rd, lu);
    dfs_vert(x, y + lu, lu, rd);
}

void solve()
{
    int n;
    cin >> n;
    init_split(n);
    dfs(1, 1, n);
    cout << res.size() << endl;
    for (int i = res.size() - 1; i >= 0; i--)
        cout << res[i][0] << ' ' << res[i][1] << ' ' << res[i][2] << endl;
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

