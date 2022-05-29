**解决赋权图的单源最短路径问题：**SPFA (Shortest Path Faster Algorithm, 最短路径快速算法) - Bellman–Ford 的队列优化算法

- 能解决负边
- 能解决负环

<!--more-->

# SPFA

### 时间复杂度：$O(\left|V\right|\cdot\left|E\right|)$

$\left|E\right|$ 为边数，$\left|V\right|$ 为顶点数。

### 代码实现：

```cpp
#include <bits/stdc++.h>
#define SIZE 100010 // SIZE需比顶点数大

using namespace std;

struct NODE
{
    int dist, point; // dist储存起点到节点的距离，point为节点序号
    NODE(int first, int second)
    {
        point = first;
        dist = second;
    }
};

int E, V, S;             // E边数，V顶点数，S起点
vector<NODE> edge[SIZE]; // 储存连接关系
queue<int> que;          // 储存节点
int dist[SIZE];          // 储存节点距离
bool vis[SIZE];          // 是否已经访问过该节点的标志

void spfa(int src)
{
    // 初始化，设置距离为无穷大（用最大int表示）
    for (int i = 1; i <= V; i++)
        dist[i] = INT32_MAX;
    // 设置起点到起点的距离为0
    dist[src] = 0;
    // 起点入队
    que.push(src);
    // 标记起点
    vis[src] = true;
    while (!que.empty())
    {
        // 取出队首节点
        int cur = que.front();
        // 弹出队首节点
        que.pop();
        // 取消标记该节点
        vis[cur] = false;
        // 遍历与该节点连接的节点
        for (auto next : edge[cur])
        {
            // 如果src->cur->next的距离小于src->next的距离
            if (dist[cur] + next.dist < dist[next.point])
            {
                // 更新新距离
                dist[next.point] = dist[cur] + next.dist;
                // 如果next节点还未访问，则入队并标记
                if (!vis[next.point])
                {
                    que.push(next.point);
                    vis[next.point] = true;
                }
            }
        }
    }
}

int main(void)
{
    cin >> E >> V >> S;
    while (E--)
    {
        int a, b, dis;
        cin >> a >> b >> dis;
        edge[a].push_back(NODE(b, dis));
        // 若双向都能通过，添加 link[b].push_back(NODE(a, dis));
    }
    spfa(S);
    for (int i = 1; i <= V; i++)
        cout << dist[i] << ' ';
    return 0;
}
```

### 算法过程：

**图例：**

- 圆圈：节点
  - 蓝：初始状态
  - 绿：节点已入队/已标记（SPFA 入队和标记是同步的）
- 箭头：路径
  - 蓝：初始状态
  - 橙：已遍历
- 数字：
  - 圆圈内黑：节点序号
  - 圆圈内白：节点到起点的距离
  - 箭头旁黑：路径长度

------

**初始化：**

> **输入：**
>
> 7 6 1
> 1 2 2
> 1 3 1
> 2 5 1
> 2 4 3
> 3 5 3
> 4 6 2
> 5 6 2

先将节点距离全部初始化为 $\infty$，然后将起点 $1$ 的距离初始化为 $0$（因为自身到自身距离为 $0$）

然后将起点 $1$ 入队，队首为 $1$

<img src="https://assets.zouht.com/img/note/29-01.webp" style="zoom:50%;" />

------

**取出队首节点 $1$：**

- 弹出并取消标记 $1$ 号节点
- 遍历 $1$ 号节点连接的节点 $2$、$3$
  - $2$ 号节点新距离 $0+2=2<\infty$，更新距离。
    - $2$ 号节点未被标记，$2$ 入队并标记
  - $3$ 号节点新距离 $0+1=1<\infty$，更新距离。
    - $3$ 号节点未被标记，$3$ 入队并标记

<img src="https://assets.zouht.com/img/note/29-02.webp" style="zoom:50%;" />

------

**取出队首节点 $3$：**

- 弹出并取消标记 $3$ 号节点
- 遍历 $3$ 号节点连接的节点 $5$
  - $5$ 号节点新距离 $1+3=4<\infty$，更新距离。
    - $5$ 号节点未被标记，$5$ 入队并标记

<img src="https://assets.zouht.com/img/note/29-03.webp" style="zoom:50%;" />

------

**取出队首节点 $2$：**

- 弹出并取消标记 $2$ 号节点
- 遍历 $2$ 号节点连接的节点 $4$、$5$
  - $4$ 号节点新距离 $2+4=5<\infty$，更新距离。
    - $4$ 号节点未被标记，$2$ 入队并标记
  - $5$ 号节点新距离 $2+1=3<4$，更新距离。
    - $3$ 号节点已被标记，跳过

<img src="https://assets.zouht.com/img/note/29-04.webp" style="zoom:50%;" />

------

**取出队首节点 $5$：**

- 弹出并取消标记 $5$ 号节点
- 遍历 $5$ 号节点连接的节点 $6$
  - $6$ 号节点新距离 $3+2=5<\infty$，更新距离。
    - $6$ 号节点未被标记，$6$ 入队并标记

<img src="https://assets.zouht.com/img/note/29-05.webp" style="zoom:50%;" />

------

**取出队首节点 $4$：**

- 弹出并取消标记 $4$ 号节点
- 遍历 $4$ 号节点连接的节点 $6$
  - $6$ 号节点新距离 $5+2=7>5$，跳过

<img src="https://assets.zouht.com/img/note/29-06.webp" style="zoom:50%;" />

------

**取出队首节点 $6$：**

- 弹出并取消标记 $6$ 号节点
- 无节点与 $6$ 号节点相连，无需其他操作

<img src="https://assets.zouht.com/img/note/29-07.webp" style="zoom:50%;" />

------

**算法结束：**

堆空，跳出循环结束算法。

> **输出：**
>
> 0 2 1 5 3 5

### 算法分析：

就代码形状来看，SPFA 和 Dijkstra 非常相似，最大的区别就是一个用的优先队列一个用的普通队列，还有就是标记的方法不太一样。

Dijkstra 用优先队列的目的就是通过贪心，总是先从最短的路径开始，从而节省时间。而 SPFA 就不一样，直接用了个队列，每次如果发现子节点不在队列里就把它丢到队列里，之后出队的时候重新计算一次该节点的距离。也就是有多少个节点指向该节点，就会重复计算多少次。

正因如此，SPFA 可以处理负边权。如下图例子：

<img src="https://assets.zouht.com/img/note/29-08.webp" style="zoom:50%;" />

Dijkstra 由于贪心思想，$1\rightarrow3$ 距离为 $1$ 小于 $2$ 时，就直接将 $3$ 标记为最小了。但是 $2\rightarrow3$ 距离为负数，使开头更远的路总体上能更近。并且由于被标记，即使之后 $3$ 的距离重新更新为了 $0$，也不会重新更新后续节点，因此 $1\rightarrow4$ 的距离会得出错误的结果 $2$。

而 SPFA 会重新更新节点，只要节点距离发生了更新（并且还没有入队），节点就会入队等待后续的更新。因此当 $3$ 的距离第二次被更新为 $0$ 后，$3$ 又进入了队列等待后续更新，于是 $4$ 就能正确更新到新的值 $1$，$1\rightarrow4$ 的距离为 $1$。

