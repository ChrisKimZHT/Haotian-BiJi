**Codeforces Round 909 (Div. 3)**

G. Unusual Entertainment

https://codeforces.com/contest/1899/problem/G

<!--more-->

3 seconds / 256 megabytes

standard input / standard output

## Problem

A tree is a connected graph without cycles.

A permutation is an array consisting of $n$ distinct integers from $1$ to $n$ in any order. For example, $[5, 1, 3, 2, 4]$ is a permutation, but $[2, 1, 1]$ is not a permutation (as $1$ appears twice in the array) and $[1, 3, 2, 5]$ is also not a permutation (as $n = 4$, but $5$ is present in the array).

After a failed shoot in the BrMeast video, Alex fell into depression. Even his birthday did not make him happy. However, after receiving a gift from Timofey, Alex's mood suddenly improved. Now he spent days playing with the gifted constructor. Recently, he came up with an unusual entertainment.

Alex builds a tree from his constructor, consisting of $n$ vertices numbered from $1$ to $n$, with the root at vertex $1$. Then he writes down each integer from $1$ to $n$ in some order, obtaining a permutation $p$. After that, Alex comes up with $q$ triples of integers $l, r, x$. For each triple, he tries to determine if there is at least one descendant of vertex $x$ among the vertices $p_l, p_{l+1}, \ldots, p_r$.

A vertex $u$ is a descendant of vertex $v$ if and only if $\mathrm{dist}(1, v) + \mathrm{dist}(v, u) = \mathrm{dist}(1, u)$, where $\mathrm{dist}(a, b)$ is the distance between vertices $a$ and $b$. In other words, vertex $v$ must be on the path from the root to vertex $u$.

Alex told Zakhar about this entertainment. Now Alex tells his friend $q$ triples as described above, hoping that Zakhar can check for the presence of a descendant. Zakhar is very sleepy, so he turned to you for help. Help Zakhar answer all of Alex's questions and finally go to sleep.

## Input

The first line of the input contains a single integer $t$ ($1 \le t \le 10^4$) — the number of test cases.

The first line of each test case contains two integers $n, q$ ($1 \le n, q \le 10^5$) — the number of vertices in the tree and the number of questions, respectively.

Each of the next $n - 1$ lines contains two integers $u_i$ and $v_i$ ($1 \le u_i, v_i \le n$), indicating that there is an edge between vertices $u_i$ and $v_i$ (it is guaranteed that the resulting graph is a tree).

The next line contains $n$ integers $p_1, p_2, \dots, p_n$ ($1 \le p_i \le n$) — the permutation $p$ (it is guaranteed that each integer from $1$ to $n$ appears exactly once).

Then follow $q$ lines describing Alex's questions. The $i$-th line contains three integers $l, r, x$ ($1 \le l \le r \le n$, $1 \le x \le n$), as described in the statement.

It is guaranteed that the sum of $n$ and the sum of $q$ over all test cases do not exceed $10^5$.

## Output

For each of Alex's questions, print "Yes" (without quotes) if the described descendant exists, otherwise print "No" (without quotes).

You can output the answer in any case (for example, the strings "yEs", "yes", "Yes", and "YES" will be recognized as a positive answer).

## Example

Input

```
3
3 5
1 2
2 3
1 2 3
1 2 2
1 2 3
2 3 1
1 2 3
2 3 3
10 10
2 6
2 7
2 4
1 7
2 8
10 6
8 5
9 4
3 4
10 2 5 9 1 7 6 4 3 8
8 9 8
7 8 1
7 10 6
4 8 9
5 5 10
7 10 1
9 9 2
9 10 6
6 6 2
10 10 6
1 1
1
1 1 1
```

Output

```
YES
NO
YES
NO
YES

NO
YES
YES
YES
NO
YES
YES
NO
NO
NO

YES
```

## 题解

**树上问题转为区间问题**

（该思想和树链剖分一样，是树链剖分的简化版，因此直接树剖也是能做的）

如果我们使用递归，以中序遍历的顺序来给树的节点标号。记递归进入标号为 $x$ 节点时标号为 $in_x$，离开标号为 $x$ 节点时标号为 $out_x$，那么我们会得到一个性质：

- 标号为 $x$ 所代表的子树的标号是连续的，且这棵子树的节点标号范围是 $[in_x,out_x]$.

用一个例子来解释，例如我们用中序遍历，这棵树将会被标记为：

```cpp
     1
    / \
   2   7
  / \
 3   4
    / \
   5   6
```

对于标号为 $2$ 的节点，$in_2 = 2,out_2=6$，发现标号 $[2,6]$ 的节点都是这棵子树的节点。

对于标号为 $4$ 的节点，$in_4=4,out_4=6$，发现标号 $[4,6]$ 的节点都是这棵子树的节点。

那么，我们先用一次 dfs，将 $in,out$ 数组处理出来，我们还需要一个原节点 ID 到中序遍历 ID 的映射，这个其实就是 $in$ 数组。

接下来，我们用 $in$ 数组，将 $p$ 数组从原节点 ID 转为我们的中序遍历 ID 得到 $a$ 数组，具体就是 $a_i=p_{in_i}$.

那么，原来的树上问题就被转成了一个区间问题：

- **树上问题：**查找 $p$ 数组 $[l,r]$ 区间内，是否存在 $x$ 的后代。
- **区间问题：**查找 $a$ 数组 $[l,r]$ 区间内，是否存在大小在 $[in_x,out_x]$ 之间的数。

**解决该区间问题**

是否存在其实就是查询存在几个，$\geq1$ 则为是，$0$ 则为否。该问题实际上就是查询一个序列的 $[l,r]$ 区间内，大小在 $[a,b]$ 的数的个数。

该问题可以用很多种数据结构完成，例如可持久化线段树。本题时限较宽，用[归并树](https://io.zouht.com/164.html)来完成也是可以的，不会超时。

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

struct MergeSortTree
{
    /* ### array index must start from ONE ### */
    int n;
    vector<vector<int>> tree;

    // arr: ori arr, [s, t]: cur seg, x: cur node
    void build(const vector<int> &arr, int s, int t, int x)
    {
        if (s == t)
        {
            tree[x] = {arr[s]};
            return;
        }
        int m = s + (t - s) / 2;
        build(arr, s, m, 2 * x);
        build(arr, m + 1, t, 2 * x + 1);
        merge(tree[2 * x].begin(), tree[2 * x].end(), tree[2 * x + 1].begin(), tree[2 * x + 1].end(),
              back_inserter(tree[x]));
    }

    MergeSortTree(const vector<int> &arr) : n(arr.size())
    {
        int sz = 1 << (__lg(n) + bool(__builtin_popcount(n) - 1)); // sz = \lceil \log_{2}{n} \rceil
        tree.resize(2 * sz);
        build(arr, 1, n, 1);
    }

    // [l, r]: query array interval, [mn, mx]: query value interval, [s, t]: cur seg, x: cur node
    int count(int l, int r, int mn, int mx, int s, int t, int x)
    {
        if (l <= s && t <= r)
            return upper_bound(tree[x].begin(), tree[x].end(), mx) - lower_bound(tree[x].begin(), tree[x].end(), mn);
        int m = s + (t - s) / 2, ans = 0;
        if (l <= m)
            ans += count(l, r, mn, mx, s, m, x * 2);
        if (r > m)
            ans += count(l, r, mn, mx, m + 1, t, x * 2 + 1);
        return ans;
    }

    // query number of elements in the [l, r] interval that fall within the range [mn, mx]
    int count(int l, int r, int mn, int mx)
    {
        return count(l, r, mn, mx, 1, n, 1);
    }

    // find the kth smallest number in the [l, r] interval
    int count(int l, int r, int k)
    {
        int pl = 1, pr = n;
        while (pl < pr)
        {
            int mid = (pl + pr) / 2;
            if (count(l, r, INT32_MIN, tree[1][mid]) < k)
                pl = mid + 1;
            else
                pr = mid;
        }
        return tree[1][pl];
    }
};

int n, q;
vector<vector<int>> edge;
vector<int> in, out;
int idx = 0;

void dfs(int now, int fa)
{
    idx++;
    in[now] = idx;
    for (auto &nxt : edge[now])
        if (nxt != fa)
            dfs(nxt, now);
    out[now] = idx;
}

void solve()
{
    cin >> n >> q;
    edge.assign(n + 10, vector<int>());
    for (int i = 1; i <= n - 1; i++)
    {
        int u, v;
        cin >> u >> v;
        edge[u].push_back(v);
        edge[v].push_back(u);
    }
    idx = 0;
    in.resize(n + 10);
    out.resize(n + 10);
    dfs(1, 0);
    vector<int> p(n + 10), a(n + 10);
    for (int i = 1; i <= n; i++)
        cin >> p[i];
    for (int i = 1; i <= n; i++)
        a[i] = in[p[i]];
    MergeSortTree mst(a);
    while (q--)
    {
        int l, r, x;
        cin >> l >> r >> x;
        if (mst.count(l, r, in[x], out[x]))
            cout << "YES" << endl;
        else
            cout << "NO" << endl;
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

