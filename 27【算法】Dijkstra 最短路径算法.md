**解决赋权图的单源最短路径问题：**Dijkstra (/ˈdaɪkstrəz/) 算法

- 不能解决负边

<!--more-->

# Dijkstra 算法

### 时间复杂度：$O((\left|E\right|+\left|V\right|)\log\left|V\right|)$ 

$\left|E\right|$ 为边数，$\left|V\right|$ 为顶点数。

该时间复杂度为使用二叉堆（优先队列）优化后的，即把 $\left| V\right|$ 优化为 $\log\left| V\right|$。

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
    // 重载<运算符，供优先队列排序使用
    bool operator<(const NODE &x) const
    {
        return dist > x.dist;
    }
};

int E, V, S;               // E边数，V顶点数，S起点
vector<NODE> edge[SIZE];   // 储存连接关系
priority_queue<NODE> pque; // 储存节点，节点距离小的在堆顶
int dist[SIZE];            // 储存节点距离
bool vis[SIZE];            // 是否已经访问过该节点的标志

void dijkstra(int src)
{
    // 初始化，设置距离为无穷大（用最大int表示）
    for (int i = 1; i <= V; i++)
        dist[i] = INT32_MAX;
    // 设置起点到起点的距离为0
    dist[src] = 0;
    // 起点入堆
    pque.push(NODE(src, dist[src]));
    while (!pque.empty())
    {
        // 取出堆顶节点
        NODE cur = pque.top();
        // 弹出堆顶节点
        pque.pop();
        // 如果该节点已经访问过，直接跳过
        if (vis[cur.point])
            continue;
        // 标记该节点已访问过
        vis[cur.point] = true;
        // 遍历与该节点相连的节点
        for (auto next : edge[cur.point])
        {
            // 如果src->cur->next的距离小于src->next的距离
            if (dist[cur.point] + next.dist < dist[next.point])
            {
                // 更新新距离
                dist[next.point] = dist[cur.point] + next.dist;
                // 如果next节点还未访问，则入堆
                if (!vis[next.point])
                    pque.push(NODE(next.point, dist[next.point]));
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
    dijkstra(S);
    for (int i = 1; i <= V; i++)
        cout << dist[i] << ' ';
    return 0;
}
```

### 算法过程：

**图例：**

- 圆圈：节点
  - 蓝：初始状态
  - 绿：节点已入堆
  - 橙：节点已标记
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

然后将起点 $(序号,距离)=(1,0)$ 入堆，堆顶为 $(1,0)$

<img src="https://assets.zouht.com/img/io/27-01.webp" style="zoom: 50%;" />

------

**取出堆顶节点 $(1,0)$：**

- 弹出 $(1,0)$
- 标记 $1$ 号节点
- 遍历 $1$ 号节点连接的节点 $2$、$3$
  - $2$ 号节点新距离 $0+2=2<\infty$，更新距离。
    - $2$ 号节点未被标记，$(2,2)$ 入堆
  - $3$ 号节点新距离 $0+1=1<\infty$，更新距离。
    - $3$ 号节点未被标记，$(3,1)$ 入堆

<img src="https://assets.zouht.com/img/io/27-02.webp" style="zoom: 50%;" />

------

**取出堆顶节点 $(3,1)$：**

- 弹出 $(3,1)$
- 标记 $3$ 号节点
- 遍历 $3$ 号节点连接的节点 $5$
  - $5$ 号节点新距离 $1+3=4<\infty$，更新距离。
    - $5$ 号节点未被标记，$(5,4)$ 入堆

<img src="https://assets.zouht.com/img/io/27-03.webp" style="zoom: 50%;" />

------

**取出堆顶节点 $(2,2)$：**

- 弹出 $(2,2)$
- 标记 $2$ 号节点
- 遍历 $2$ 号节点连接的节点 $4$、$5$
  - $4$ 号节点新距离 $2+3=5<\infty$，更新距离。
    - $4$ 号节点未被标记，$(4,5)$ 入堆
  - $5$ 号节点新距离 $2+1=3<4$，更新距离。
    - $5$ 号节点未被标记，$(5,3)$ 入堆

<img src="https://assets.zouht.com/img/io/27-04.webp" style="zoom: 50%;" />

------

**取出堆顶节点 $(5,3)$：**

- 弹出 $(5,3)$
- 标记 $5$ 号节点
- 遍历 $5$ 号节点连接的节点 $6$
  - $6$ 号节点新距离 $3+2=5<\infty$，更新距离。
    - $6$ 号节点未被标记，$(6,5)$ 入堆

<img src="https://assets.zouht.com/img/io/27-05.webp" style="zoom: 50%;" />

------

**取出堆顶节点 $(5,4)$：**

- 弹出 $(5,4)$
- $5$ 号节点已经标记过，跳过其他步骤

<img src="https://assets.zouht.com/img/io/27-06.webp" style="zoom: 50%;" />

------

**取出堆顶节点 $(4,5)$：**

- 弹出 $(4,5)$
- 标记 $4$ 号节点
- 遍历 $4$ 号节点连接的节点 $6$
  - $6$ 号节点新距离 $5+2=7>5$，不更新距离，跳过其他步骤。

<img src="https://assets.zouht.com/img/io/27-07.webp" style="zoom: 50%;" />

------

**取出堆顶节点 $(6,5)$：**

- 弹出 $(6,5)$
- 标记 $6$ 号节点
- 无节点与 $6$ 号节点相连，无需其他操作

<img src="https://assets.zouht.com/img/io/27-08.webp" style="zoom: 50%;" />

------

**算法结束：**

堆空，跳出循环结束算法。

> **输出：**
>
> 0 2 1 5 3 5

### 算法分析：

Dijkstra 算法的根本思想为贪心思想，从起点开始，先选最短的路径，标记已经能确定的最短路径，再松弛选第二短的路径。在不断标记和松弛的过程中找到起点到所有点的最短路径。

算法每轮操作取的都是堆顶的元素，即从起点到该点距离最短的元素，并且标记该节点为最短。因为其他路径都更长，例如堆中现有路径长 $3、4$，不可能有条路经过长度 $4$ 后反而比 $3$ 要短（除非有条路长度为负数。正因如此负数会扰断 Dijkstra 算法的逻辑，导致错误），所以可以长度为 $3$ 的这条路为最短。

利用已经确定点的最短路径，然后再去不断计算出新的点的最短路径，不断循环下去就可以得出起点到所有点的最短路径。

其中需要注意的是 Dijkstra 算法可以有重复节点入堆，但只有节点第一次出堆才有用，这个用 vis[ ] 数组来实现，这样就能保证不重复计算路径长度。

