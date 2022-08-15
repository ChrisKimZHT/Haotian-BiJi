**解决赋权图的多源最短路径问题：**Floyd-Warshall 算法

- 能解决负边
- 不能解决负环

<!--more-->

# Floyd-Warshall 算法

## 复杂度

时间复杂度：$O(\left|V\right|^3)$

$\left|V\right|$ 为顶点数

## 分析

Floyd 算法比较简单，即暴力枚举了所有可能，从而找到任意两点的距离。

从点 $i$ 到点 $j$ 的走法无非两种：

- $i\rightarrow j$
- $i\rightarrow k\rightarrow j$

那么三个循环，遍历所有的 $i,j,k$，如果 $i\rightarrow k\rightarrow j$ 距离比 $i\rightarrow j$ 短，那么就更新距离。

#### 防止无穷大溢出

数学中 $\infty+\infty=\infty$，但程序中两个代表 $\infty$ 的大数相加肯定是会溢出成负数的，这会对该松弛操作产生巨大的干扰。因此进行松弛操作时需要判断两数是否为 $\infty$，只要有一个数为 $\infty$，就跳过这次松弛操作以防溢出。

## 代码实现

```cpp
const int MAXN = 1010, INF = 0x3f3f3f3f;
int e, v;             // e边数 v顶点数
int dist[MAXN][MAXN]; // dist[x][y]代表x到y的距离

void floyd_init(void)
{
    memset(dist, 0x3f, sizeof(dist));
    for (int i = 1; i <= v; i++)
        dist[i][i] = 0;
}

void floyd(void)
{
    for (int k = 1; k <= v; k++)
        for (int i = 1; i <= v; i++)
            for (int j = 1; j <= v; j++)
                if (dist[i][k] < INF && dist[k][j] < INF)
                    dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);
}
```
