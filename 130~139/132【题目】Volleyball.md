**Codeforces Beta Round 77 (Div. 1 Only)**

D. Volleyball

https://codeforces.com/problemset/problem/95/C

<!--more-->

2 seconds / 256 megabytes

standard input / standard output

## Problem

Petya loves volleyball very much. One day he was running late for a volleyball match. Petya hasn't bought his own car yet, that's why he had to take a taxi. The city has n junctions, some of which are connected by two-way roads. The length of each road is defined by some positive integer number of meters; the roads can have different lengths.

Initially each junction has exactly one taxi standing there. The taxi driver from the i-th junction agrees to drive Petya (perhaps through several intermediate junctions) to some other junction if the travel distance is not more than ti meters. Also, the cost of the ride doesn't depend on the distance and is equal to ci bourles. Taxis can't stop in the middle of a road. Each taxi can be used no more than once. Petya can catch taxi only in the junction, where it stands initially.

At the moment Petya is located on the junction x and the volleyball stadium is on the junction y. Determine the minimum amount of money Petya will need to drive to the stadium.

## Input

The first line contains two integers n and m (1 ≤ n ≤ 1000, 0 ≤ m ≤ 1000). They are the number of junctions and roads in the city correspondingly. The junctions are numbered from 1 to n, inclusive. The next line contains two integers x and y (1 ≤ x, y ≤ n). They are the numbers of the initial and final junctions correspondingly. Next m lines contain the roads' description. Each road is described by a group of three integers ui, vi, wi (1 ≤ ui, vi ≤ n, 1 ≤ wi ≤ 109) — they are the numbers of the junctions connected by the road and the length of the road, correspondingly. The next n lines contain n pairs of integers ti and ci (1 ≤ ti, ci ≤ 109), which describe the taxi driver that waits at the i-th junction — the maximum distance he can drive and the drive's cost. The road can't connect the junction with itself, but between a pair of junctions there can be more than one road. All consecutive numbers in each line are separated by exactly one space character.

## Output

If taxis can't drive Petya to the destination point, print "-1" (without the quotes). Otherwise, print the drive's minimum cost.

Please do not use the %lld specificator to read or write 64-bit integers in С++. It is preferred to use cin, cout streams or the %I64d specificator.

## Examples

Input

> 4 4
> 1 3
> 1 2 3
> 1 4 1
> 2 4 1
> 2 3 5
> 2 7
> 7 2
> 1 2
> 7 7

Output

> 9

## Note

An optimal way — ride from the junction 1 to 2 (via junction 4), then from 2 to 3. It costs 7+2=9 bourles.

## 题解

题目中 $n,m$ 均在 $1000$ 量级，因此可以考虑对每个点做最短路，把每个点的出租车能到的位置全部都算一遍，如果 $i$ 点出租车能到 $j$ 点，则在新图中加一条 $i\to j$ 的边，权重为费用。最后再在新图中跑一边最短路即可。

具体来说，对于点 $i$，首先将其作为起点跑一边 dijkstra，得到到每个点的最短距离 $\text{dist}_j$. 然后考察距离，如果 $\text{dist}_j\leq t_i$，那么将 $i\to j$ 权重为 $c_i$ 添加到新图中，反之跳过。考察完所有 $i$ 后，在新图中以 $x$ 为起点跑一边 dijkstra，最后答案便是 $\text{dist}_j$.

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

typedef pair<int, int> PII;
const int MAXN = 1e3 + 10, INF = 0x3f3f3f3f3f3f3f3f;
int E, V, S;
vector<PII> edge[MAXN], edge2[MAXN];
priority_queue<PII, vector<PII>, greater<PII>> pque;
int dist[MAXN], t[MAXN], c[MAXN];
bool vis[MAXN];

void dijkstra(int src)
{
    memset(dist, 0x3f, sizeof(dist));
    memset(vis, 0, sizeof(vis));
    dist[src] = 0;
    pque.push({dist[src], src});
    while (!pque.empty())
    {
        PII cur = pque.top();
        pque.pop();
        if (vis[cur.second])
            continue;
        vis[cur.second] = true;
        for (auto next : edge[cur.second])
        {
            if (dist[next.second] > dist[cur.second] + next.first)
            {
                dist[next.second] = dist[cur.second] + next.first;
                if (!vis[next.second])
                    pque.push({dist[next.second], next.second});
            }
        }
    }
}

void dijkstra2(int src)
{
    memset(dist, 0x3f, sizeof(dist));
    memset(vis, 0, sizeof(vis));
    dist[src] = 0;
    pque.push({dist[src], src});
    while (!pque.empty())
    {
        PII cur = pque.top();
        pque.pop();
        if (vis[cur.second])
            continue;
        vis[cur.second] = true;
        for (auto next : edge2[cur.second])
        {
            if (dist[next.second] > dist[cur.second] + next.first)
            {
                dist[next.second] = dist[cur.second] + next.first;
                if (!vis[next.second])
                    pque.push({dist[next.second], next.second});
            }
        }
    }
}

void solve()
{
    int n, m;
    cin >> n >> m;
    int x, y;
    cin >> x >> y;
    for (int i = 1; i <= m; i++)
    {
        int u, v, w;
        cin >> u >> v >> w;
        edge[u].push_back({w, v});
        edge[v].push_back({w, u});
    }
    for (int i = 1; i <= n; i++)
        cin >> t[i] >> c[i];
    for (int i = 1; i <= n; i++)
    {
        dijkstra(i);
        for (int j = 1; j <= n; j++)
        {
            if (i == j)
                continue;
            if (dist[j] <= t[i])
                edge2[i].push_back({c[i], j});
        }
    }
    dijkstra2(x);
    if (dist[y] >= INF)
        cout << -1 << endl;
    else
        cout << dist[y] << endl;
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

