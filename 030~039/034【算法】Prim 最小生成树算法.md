**求最小生成树（适合稠密图）：**Prim (普林姆) 算法

<!--more-->

# Prim 算法

### 时间复杂度：$O(\left|E\right|\log\left|V\right|)$

$\left|E\right|$ 为边数，$\left|V\right|$ 为顶点数。

### 代码实现：

```cpp
#include <bits/stdc++.h>
#define E_SIZE 200010
#define V_SIZE 5010

using namespace std;

// 链式前向星模板
struct EDGE
{
    int to, weight, next;
} edge[E_SIZE * 2];
int head[V_SIZE];

int V, E;
int cnt;

void init()
{
    cnt = 0;
    for (int i = 1; i <= V; i++)
        head[i] = -1;
};

void add_edge(int start, int end, int weight)
{
    edge[cnt].to = end;
    edge[cnt].weight = weight;
    edge[cnt].next = head[start];
    head[start] = cnt++;
}

// prim要用到的变量
bool vis[V_SIZE]; // 标记点是否已联通
priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pque; // 小顶堆，用于选择最短的边，使用STL内置的pai'r排序

int main(void)
{
    cin >> V >> E;
    init();
    for (int i = 0; i < E; i++)
    {
        int a, b, dis;
        cin >> a >> b >> dis;
        add_edge(a, b, dis);
    }
    int selected = -1, ans = 0; // selected为已选择的边数，ans为结果
    // 先随机选择一个点作为起点，不妨固定选择点1
    pque.push(make_pair(0, 1));
    while (!pque.empty() && selected != V - 1)
    {
        // 取出堆顶
        pair<int, int> tmp = pque.top();
        // 弹出堆顶
        pque.pop();
        // 若该点已联通，跳过该点
        if (vis[tmp.second])
            continue;
        // 标记该点已联通
        vis[tmp.second] = true;
        // 答案加上刚选择的这条边长
        ans += tmp.first;
        // 选择的边数+1
        selected++;
        // 链式前向星遍历，将该点为起点的边加入优先队列中
        for (int i = head[tmp.second]; i != -1; i = edge[i].next)
            pque.push(make_pair(edge[i].weight, edge[i].to));
    }
    if (selected == V - 1)
        cout << ans << endl;
    else
        cout << "Error" << endl;
    return 0;
}
```

### 算法思路：

该算法的核心思路是，在能到达的边中选择最短的不会成环的边。

用一个例子来描述，绿色节点为已选择的节点，橙色边为可到达的边，绿色边为选择的边。

**初始化：**

<img src="https://assets.zouht.com/img/note/34-01.webp" style="zoom:50%;" />

**从点 $1$ 开始（可以从任意节点开始）：**

- 将 $1\leftrightarrow2,1\leftrightarrow3,1\leftrightarrow4$ 入堆

<img src="https://assets.zouht.com/img/note/34-02.webp" style="zoom:50%;" />

**最短的边为 $1\leftrightarrow3$：**

- 将 $3$ 标记为联通，并将 $1\leftrightarrow3$ 长度加入答案
- 将 $3\leftrightarrow2,3\leftrightarrow5,3\leftrightarrow6,3\leftrightarrow4$ 入堆

<img src="https://assets.zouht.com/img/note/34-03.webp" style="zoom:50%;" />

**后面的过程略：**

<img src="https://assets.zouht.com/img/note/34-04.webp" style="zoom:50%;" />

<img src="https://assets.zouht.com/img/note/34-05.webp" style="zoom:50%;" />

<img src="https://assets.zouht.com/img/note/34-06.webp" style="zoom:50%;" />

<img src="https://assets.zouht.com/img/note/34-07.webp" style="zoom:50%;" />
