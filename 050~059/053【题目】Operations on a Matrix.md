**NOMURA Programming Contest 2022（AtCoder Beginner Contest 253）**

F - Operations on a Matrix 

https://atcoder.jp/contests/abc253/tasks/abc253_f

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $500$ points

### Problem Statement

We have an $N \times M$ matrix, whose elements are initially all $0$.

Process $Q$ given queries. Each query is in one of the following formats.

- `1 l r x` : Add $x$ to every element in the $l$-th, $(l+1)$-th, $\ldots$, and $r$-th column.
- `2 i x`: Replace every element in the $i$-th row with $x$.
- `3 i j`: Print the $(i, j)$-th element.

### Constraints

- $1 \leq N, M, Q \leq 2 \times 10^5$
- Every query is in one of the formats listed in the Problem Statement.
- For each query in the format `1 l r x`, $1 \leq l \leq r \leq M$ and $1 \leq x \leq 10^9$.
- For each query in the format `2 i x`, $1 \leq i \leq N$ and $1 \leq x \leq 10^9$.
- For each query in the format `3 i j`, $1 \leq i \leq N$ and $1 \leq j \leq M$.
- At least one query in the format `3 i j` is given.
- All values in input are integers.

------

### Input

Input is given from Standard Input in the following format:

> $N$ $M$ $Q$
> $\mathrm{Query}_1$
> $\vdots$
> $\mathrm{Query}_Q$

$\mathrm{Query}_i$, which denotes the $i$-th query, is in one of the following formats:

> $1$ $l$ $r$ $x$
> $2$ $i$ $x$
> $3$ $i$ $j$

### Output

For each query in the format `3 i j`, print a single line containing the answer.

------

### Sample Input 1

> 3 3 9
> 1 1 2 1
> 3 2 2
> 2 3 2
> 3 3 3
> 3 3 1
> 1 2 3 3
> 3 3 2
> 3 2 3
> 3 1 2

### Sample Output 1

> 1
> 2
> 2
> 5
> 3
> 4

The matrix transitions as follows.

$\begin{pmatrix} 0 & 0 & 0 \\ 0 & 0 & 0 \\ 0 & 0 & 0 \\ \end{pmatrix} \rightarrow \begin{pmatrix} 1 & 1 & 0 \\ 1 & 1 & 0 \\ 1 & 1 & 0 \\ \end{pmatrix} \rightarrow \begin{pmatrix} 1 & 1 & 0 \\ 1 & 1 & 0 \\ 2 & 2 & 2 \\ \end{pmatrix} \rightarrow \begin{pmatrix} 1 & 4 & 3 \\ 1 & 4 & 3 \\ 2 & 5 & 5 \\ \end{pmatrix}$

------

### Sample Input 2

> 1 1 10
> 1 1 1 1000000000
> 1 1 1 1000000000
> 1 1 1 1000000000
> 1 1 1 1000000000
> 1 1 1 1000000000
> 1 1 1 1000000000
> 1 1 1 1000000000
> 1 1 1 1000000000
> 1 1 1 1000000000
> 3 1 1

### Sample Output 2

> 9000000000

------

### Sample Input 3

> 10 10 10
> 1 1 8 5
> 2 2 6
> 3 2 1
> 3 4 7
> 1 5 9 7
> 3 3 2
> 3 2 8
> 2 8 10
> 3 8 8
> 3 1 10

### Sample Output 3

> 6
> 5
> 5
> 13
> 10
> 0

### 我的笔记

称操作 $1$ 为更新操作，操作 $2$ 为替换操作，操作 $3$ 为查询操作。

一行如果进行了替换操作，那么此行每一个元素之前进行的更新或替换操作都会被覆盖，因此只用考虑之后的操作。我们可以记录该次操作时最后一次替换操作，这样我们只需考虑替换操作和之后的操作即可。

latest 数组第 $i$ 位代表第 $i$ 行最后一次替换操作。

vis vector 数组的第 $i$ 个 vector 容器储存第 $i$ 次替换操作会影响到的查询操作的序号。

ans 数组第 $i$ 位代表第 $i$ 次操作的答案。

- 首先读入数据

  - 当为更新操作的时候，只读入。

  - 当为替换操作的时候，更新矩阵对应行的 `最后一次替换操作` 的操作序号。

  - 当为查询操作的时候，将该 `查询操作的操作序号` 加入到 vis 数组中的 `最后一次替换操作的序号` 的项中。

按这个规则读入数据后，latest 数组和 vis 数组就储存好了上面所说的数据。

- 然后开始计算

  - 如果是更新操作，我们用树状数组维护列的更新情况，树状数组储存前缀和，这样每次只需要修改首尾两个元素，查询时输出求和即可。

  - 如果是替换操作，我们通过读入时创建的 vis 数组，可以知道这次替换操作会影响到哪几次的查询操作，然后将有影响的查询操作的 ans 设置为 `该次替换的替换值` $-$ `此时此列的更新值`。之所以要减去 `此时此列的更新值`，是因为该次替换覆盖了之前所有的更新操作，减去的这个值会与查询操作加上的值相消，下面就是说的这个。

  - 如果是查询操作，我们将 `ans 对应位` $+$ `此时此列的更新值`，与上面减去的更新值相消后，便是从最后一次替换到现在的更新值。

### 源码

```cpp
#include <bits/stdc++.h>
#define int long long

using namespace std;

const int MAXN = 2e5 + 10;
int N, M, Q;
struct DATA
{
    int op, a, b, c;
} q[MAXN];
int latest[MAXN];
int ans[MAXN];
vector<int> vis[MAXN];

int Fenwick_Tree[MAXN];
void update(int pos, int val)
{
    for (int i = pos; i < MAXN; i += i & -i)
        Fenwick_Tree[i] += val;
}
int query(int pos)
{
    int ans = 0;
    for (int i = pos; i; i -= i & -i)
        ans += Fenwick_Tree[i];
    return ans;
}

signed main()
{
    cin >> N >> M >> Q;
    for (int i = 1; i <= Q; i++)
    {
        cin >> q[i].op;
        if (q[i].op == 1)
        {
            cin >> q[i].a >> q[i].b >> q[i].c;
        }
        else if (q[i].op == 2)
        {
            cin >> q[i].a >> q[i].b;
            latest[q[i].a] = i;
        }
        else
        {
            cin >> q[i].a >> q[i].b;
            vis[latest[q[i].a]].push_back(i);
        }
    }
    for (int i = 1; i <= Q; i++)
    {
        if (q[i].op == 1)
        {
            update(q[i].a, q[i].c);
            update(q[i].b + 1, -q[i].c);
        }
        else if (q[i].op == 2)
        {
            for (int v : vis[i])
                ans[v] = q[i].b - query(q[v].b);
        }
        else
        {
            ans[i] += query(q[i].b);
            cout << ans[i] << endl;
        }
    }
    return 0;
}
```

