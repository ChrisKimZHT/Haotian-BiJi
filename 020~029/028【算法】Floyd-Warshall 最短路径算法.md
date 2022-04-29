**解决赋权图的多源最短路径问题：**Floyd-Warshall 算法

- 能解决负边
- 不能解决负环

<!--more-->

# Floyd-Warshall 算法

### 时间复杂度：$O(\left|V\right|^3)$

$\left|V\right|$ 为顶点数

### 代码实现：

```cpp
#include <bits/stdc++.h>
#define SIZE 10010 // SIZE需比顶点数大

using namespace std;

int E, V;             // E边数，V顶点数
int dist[SIZE][SIZE]; // 储存节点距离

void floyd_init(void)
{
    // 初始化，设置距离为无穷大（用最大int表示）
    for (int i = 1; i <= V; i++)
    {
        for (int j = 1; j <= V; j++)
        {
            dist[i][j] = INT32_MAX;
        }
    }
    // 将自身到自身距离设置为0
    for (int i = 1; i <= V; i++)
    {
        dist[i][i] = 0;
    }
}

void floyd(void)
{
    // 若i->k->j路径比i->j短，则更新为新距离
    for (int k = 1; k <= V; k++)
        for (int i = 1; i <= V; i++)
            for (int j = 1; j <= V; j++)
                // if防止相加导致int溢出
                if (dist[i][k] < INT32_MAX && dist[k][j] < INT32_MAX)
                    dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);
}

int main(void)
{
    cin >> E >> V;
    floyd_init();
    while (E--)
    {
        int a, b, dis;
        cin >> a >> b >> dis;
        dist[a][b] = dis;
        // 若双向都能通过，添加 dist[b][a] = dis;
    }
    floyd();
    cout << dist[1][V] << endl; // 表示1到V的最短距离
    return 0;
}
```

### 算法分析：

Floyd 算法比较简单，即暴力枚举了所有可能，从而找到任意两点的距离。

从点 $i$ 到点 $j$ 的走法无非两种：

- $i\rightarrow j$
- $i\rightarrow k\rightarrow j$

那么三个循环，遍历所有的 $i,j,k$，如果 $i\rightarrow k\rightarrow j$ 距离比 $i\rightarrow j$ 短，那么就更新距离。
