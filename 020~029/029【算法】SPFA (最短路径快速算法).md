**解决赋权图的单源最短路径问题：**SPFA (Shortest Path Faster Algorithm, 最短路径快速算法) - Bellman-Ford 的队列优化算法

- 能解决负边
- 能解决负环

<!--more-->

# SPFA

## 复杂度

平均时间复杂度：$O(\left|E\right|)$

最差时间复杂度：$O(\left|V\right|\cdot\left|E\right|)$

（$\left|V\right|$ 为顶点数，$\left|E\right|$ 为边数）

## 分析

SPFA 算法为 Bellman-Ford 算法的优化，所以建议先学习 Bellman-Ford 算法：https://io.zouht.com/64.html

在 Bellman-Ford 算法中，每一次迭代我们一股脑地对所有边进行了松弛操作，但实际上很多松弛操作是没有意义的。如果一个点距离没有更新，那么没有必要对它进行松弛操作，因为答案根本不会变化。SPFA 就是针对这一点进行了优化。

SPFA 使用一个队列，若一个点被更新了，那么就将其加入队列，等待松弛操作。简单来说就是只有距离被更新过的点才进行松弛操作，若一个点没有变化，不会对其进行操作。

## 代码实现

```cpp
const int MAXN = 1e5 + 10, INF = 0x3f3f3f3f; // INF代表无穷大
int e, v, s;                                 // e边数 v顶点数 s起点
vector<pair<int, int>> edge[MAXN];           // 邻接表存图 pair为(距离, 终点)
queue<int> que;                              // STL队列
int dist[MAXN];                              // 最短距离
bool vis[MAXN];                              // 是否入队

void spfa(int src)
{
    memset(dist, 0x3f, sizeof(dist));
    dist[src] = 0;
    que.push(src);
    vis[src] = true;
    while (!que.empty())
    {
        int cur = que.front();
        que.pop();
        vis[cur] = false;
        for (auto next : edge[cur])
        {
            if (dist[next.second] > dist[cur] + next.first)
            {
                dist[next.second] = dist[cur] + next.first;
                if (!vis[next.second])
                {
                    que.push(next.second);
                    vis[next.second] = true;
                }
            }
        }
    }
}
```

## 利与 SPFA 判断负权回路

由于 SPFA 为 Bellman-Ford 算法的优化，所以它也可以判断负权回路，思路就是记录一个点被松弛了多少次。若没有负环一个点最多被松弛 $\left|V\right|-1$ 次，但如果有负环，一个点可以被松弛 $\infin$ 次。只需要对代码稍作修改即可：

- 无需将 dist 初始化为 $\infin$：因为我们只判负环，不在意距离是多少，因此无需初始化。
- 算法开始时需要将所有点入队：若只以一个点为起点，可能有些负环不与其连通无法判断到，因此得每个点都判断一遍。
- 增加一个 cnt 数组：储存被松弛多少次，一个点最多被松弛 $\left|V\right|-1$ 次，若大于它一定是出现了负环。

更改后的代码如下：

```cpp
const int MAXN = 1e5 + 10, INF = 0x3f3f3f3f;
int E, V, S;
vector<pair<int, int>> edge[MAXN];
queue<int> que;
int dist[MAXN], cnt[MAXN];
bool vis[MAXN];

bool spfa()
{
    for (int i = 1; i <= V; i++)
    {
        que.push(i);
        vis[i] = true;
    }
    while (!que.empty())
    {
        int cur = que.front();
        que.pop();
        vis[cur] = false;
        for (auto next : edge[cur])
        {
            if (dist[cur] + next.first < dist[next.second])
            {
                dist[next.second] = dist[cur] + next.first;
                cnt[next.second] = cnt[cur] + 1;
                if (cnt[next.second] >= V)
                    return true;
                if (!vis[next.second])
                {
                    que.push(next.second);
                    vis[next.second] = true;
                }
            }
        }
    }
    return false;
}
```
