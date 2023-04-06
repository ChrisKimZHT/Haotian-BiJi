**Codeforces Round 862 (Div. 2)**

D. A Wide, Wide Graph

https://codeforces.com/contest/1805/problem/D

<!--more-->

1 second / 256 megabytes

standard input / standard output

## Problem

You are given a tree (a connected graph without cycles) with $n$ vertices. 

Consider a fixed integer $k$. Then, the graph $G_k$ is an undirected graph with $n$ vertices, where an edge between vertices $u$ and $v$ exists if and only if the distance between vertices $u$ and $v$ in the given tree is **at least** $k$.

For each $k$ from $1$ to $n$, print the number of connected components in the graph $G_k$.

## Input

The first line contains the integer $n$ ($2 \le n \le 10^5$) — the number of vertices in the graph.

Each of the next $n-1$ lines contains two integers $u$ and $v$ ($1 \le u, v \le n$), denoting an edge between vertices $u$ and $v$ in the tree. It is guaranteed that these edges form a valid tree.

## Output

Output $n$ integers: the number of connected components in the graph $G_k$ for each $k$ from $1$ to $n$.

## Examples

Input

> 6
> 1 2
> 1 3
> 2 4
> 2 5
> 3 6

Output

> 1 1 2 4 6 6 

Input

> 5
> 1 2
> 2 3
> 3 4
> 3 5

Output

> 1 1 3 5 5 

## Note

In the first example: If $k=1$, the graph has an edge between each pair of vertices, so it has one component. If $k=4$, the graph has only edges $4 \leftrightarrow 6$ and $5 \leftrightarrow 6$, so the graph has $4$ components.

In the second example: when $k=1$ or $k=2$ the graph has one component. When $k=3$ the graph $G_k$ splits into $3$ components: one component has vertices $1$, $4$ and $5$, and two more components contain one vertex each. When $k=4$ or $k=5$ each vertex is a separate component.

## 题解

首先找到整颗树中最长的路线的两个端点 $a,b$，例如我们树为：

```
      1
     /|\
    2 3 4
   /   / \
  5   6   7
 /
8
```

那么最长的路线就是 $6\leftrightarrow8$（或 $7\leftrightarrow8$），如果我们将这棵树的最长路线拉长，这样观察更为方便：

```
8 — 5 — 2 — 1 — 4 — 6
            |   |
            3   7
```

我们可以证明，每个点 $x$ 能到达的最远的距离 $dist_{max}(x)$ 就是上面的树的两个端点，即 $dist_{max}(x)=\max(dist(x,a),dist(x,b))$.

如果我们记录下来每个点能到的最远的距离，再从小到大排序（本例刚好已经排好序了）：

| 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 3    | 3    | 4    | 4    | 4    | 5    | 5    | 5    |

那么不能联通的点就是 $dist_{max}(x)<k$ 的点，不能联通的点的数量 + 1 便是对应的答案。例如 $k=4$ 时，$1,2$ 点的最长距离 $<4$，答案就是 $3$.

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'

using namespace std;

const int MAXN = 1e5 + 10;
vector<int> edge[MAXN];

void dfs(int v, int h, vector<int> &dist)
{
    dist[v] = h;
    for (auto ele : edge[v])
        if (dist[ele] == -1)
            dfs(ele, h + 1, dist);
}

void solve()
{
    int n;
    cin >> n;
    for (int i = 0; i < n - 1; i++)
    {
        int u, v;
        cin >> u >> v;
        edge[u].push_back(v);
        edge[v].push_back(u);
    }
    // init
    vector<int> dist_1(n + 10, -1);
    dfs(1, 0, dist_1);
    // farthest-a
    int max_1 = max_element(dist_1.begin(), dist_1.end()) - dist_1.begin();
    vector<int> dist_2(n + 10, -1);
    dfs(max_1, 0, dist_2);
    // farthest-b
    int max_2 = max_element(dist_2.begin(), dist_2.end()) - dist_2.begin();
    vector<int> dist_3(n + 10, -1);
    dfs(max_2, 0, dist_3);
    vector<int> dist(n + 10);
    for (int i = 1; i <= n; i++)
        dist[i] = max(dist_2[i], dist_3[i]);
    sort(dist.begin() + 1, dist.begin() + 1 + n);
    int cnt = 1;
    for (int i = 1; i <= n; i++)
    {
        while (cnt < n && dist[cnt] < i)
            cnt++;
        cout << cnt << ' ';
    }
    cout << endl;
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

