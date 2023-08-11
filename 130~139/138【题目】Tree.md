**2023牛客暑期多校训练营6**

A - Tree

https://ac.nowcoder.com/acm/contest/57360/A

<!--more-->

## 题意

给定 $n$ 个点的一棵边带权的树，点有黑白二色（$0,1$ 表示），现在可以以 $a_i$ 的价值翻转第 $i$ 个点的颜色，一对异色点 $(u,v)$ 的价值为树上路径的最大边权值。问经过任意颜色翻转后，价值减去代价的最大值。$1\leq n\leq3\times 10^3$，$1\leq a_i\leq 10^9$。

## 题解

该题有关树上两点间路径的最大边权，考虑借助 [Kruskal 重构树](https://io.zouht.com/137.html)解决这道题。

首先将题目给出的带边权的树转换为带点权的 Kruskal 重构树，$\text{val}_i$ 是重构树的点权。

那么如果对于重构树的一个内部节点 $p$，如果 $a$ 点在它的左子树，$b$ 点在它的右子树，那么 $a,b$ 两点的最近公共祖先就是 $p$，根据重构树性质，$a,b$ 两点路径的最大边权就是 $\text{val}_p$.

如果左子树大小为 $n_{\text{l}}$，右子树大小为 $n_{\text{r}}$，左子树有 $n_{\text{l,black}}$ 个黑点，左子树有 $n_{\text{l,white}}$ 个白点，右子树有 $n_{\text{r,black}}$ 个黑点，右子树有 $n_{\text{r,white}}$ 个白点，那么由 $p$ 节点代表的这条边的贡献就是：
$$
\text{val}_p\cdot(n_{\text{l,black}}\cdot n_{\text{r,white}}+n_{\text{l,white}}\cdot n_{\text{r,black}})
$$
然后，遍历所有的黑点分配方式，便能算出最大的共享值。当然，这只计算了经过这个 $p$ 节点产生的贡献。要解决整个问题，要使用动态规划的思想。

用 $dp_{i,j}$ 代表对于重构树上 $i$ 节点，左右子树加起来共 $j$ 个黑点的最大贡献。

转移方式为，其中左子树根节点下标 $l$，右子树根节点下标 $r$，左子树大小为 $n_{\text{l}}$，右子树大小为 $n_{\text{r}}$：
$$
dp_{i,j}=\max_{k=\max\{0,j-n_{\text{r}}\}}^{\min\{n_{\text{l}},j\}}(dp_{l,n_{\text{l,black}}}+dp_{r,n_{\text{r,black}}}+\text{val}_p\cdot(n_{\text{l,black}}\cdot n_{\text{r,white}}+n_{\text{l,white}}\cdot n_{\text{r,black}}))\\
其中,
\begin{cases}
n_{\text{l,black}}=k\\
n_{\text{l,white}}=n_{\text{l}}-n_{\text{l,black}}\\
n_{\text{r,black}}=j-k\\
n_{\text{r,white}}=n_{\text{r}}-n_{\text{r,black}}
\end{cases}
$$

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

constexpr int MAXN = 3e3 + 10;
int n;
int a[MAXN], cost[MAXN];
int fa[MAXN << 1];
int val[MAXN << 1], siz[MAXN << 1]; // 重构树点权, 重构树子树大小
vector<int> krus[MAXN << 1];        // Kruskal 重构树
int dp[MAXN << 1][MAXN];

void init()
{
    for (int i = 0; i < (MAXN << 1); i++)
        fa[i] = i;
}

int find(int x)
{
    return (fa[x] == x) ? x : (fa[x] = find(fa[x]));
}

void dfs(int now)
{
    if (now <= n)
    {
        siz[now] = 1;
        dp[now][a[now]] = 0;
        dp[now][1 - a[now]] = -cost[now];
        return;
    }
    int l_son = krus[now].front(), r_son = krus[now].back();
    dfs(l_son);
    dfs(r_son);
    siz[now] = siz[l_son] + siz[r_son];
    for (int blk = 0; blk <= siz[now]; blk++)
    {
        dp[now][blk] = INT64_MIN;
        for (int l = max(0LL, blk - siz[r_son]); l <= min(blk, siz[l_son]); l++)
        {
            int l_black = l, l_white = siz[l_son] - l_black;
            int r_black = blk - l, r_white = siz[r_son] - r_black;
            int tmp = val[now] * (l_black * r_white + l_white * r_black);
            tmp += dp[l_son][l_black];
            tmp += dp[r_son][r_black];
            dp[now][blk] = max(dp[now][blk], tmp);
        }
    }
}

void solve()
{
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    for (int i = 1; i <= n; i++)
        cin >> cost[i];
    vector<array<int, 3>> edge(n + 10);
    for (int i = 1; i < n; i++)
        cin >> edge[i][0] >> edge[i][1] >> edge[i][2];
    sort(edge.begin() + 1, edge.begin() + n, [](array<int, 3> a, array<int, 3> b) { return a[2] < b[2]; });
    int pos = n + 1;
    for (int i = 1; i < n; i++)
    {
        auto &[u, v, w] = edge[i];
        int fa_u = find(u), fa_v = find(v);
        if (fa_u == fa_v)
            continue;
        krus[pos].push_back(fa_u);
        krus[pos].push_back(fa_v);
        val[pos] = w;
        fa[fa_u] = pos;
        fa[fa_v] = pos;
        pos++;
    }
    int root = find(1);
    dfs(root);
    int ans = 0;
    for (int i = 0; i <= n; i++)
        ans = max(ans, dp[root][i]);
    cout << ans << endl;
}

signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    int t = 1;
    // cin >> t;
    init();
    while (t--)
        solve();
    return 0;
}
```

