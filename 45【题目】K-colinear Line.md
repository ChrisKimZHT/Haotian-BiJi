**UNIQUE VISION Programming Contest 2022（AtCoder Beginner Contest 248）**

E - K-colinear Line

https://atcoder.jp/contests/abc248/tasks/abc248_e

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $500$ points

### Problem Statement

You are given $N$ points in the coordinate plane. For each $1\leq i\leq N$, the $i$-th point is at the coordinates $(X_i, Y_i)$.

Find the number of lines in the plane that pass $K$ or more of the $N$ points.
If there are infinitely many such lines, print `Infinity`.

### Constraints

- $1 \leq K \leq N \leq 300$
- $\lvert X_i \rvert, \lvert Y_i \rvert \leq 10^9$
- $X_i\neq X_j$ or $Y_i\neq Y_j$, if $i\neq j$.
- All values in input are integers.

------

### Input

Input is given from Standard Input in the following format:

> $N$ $K$
> $X_1$ $Y_1$
> $X_2$ $Y_2$
> $\vdots$
> $X_N$ $Y_N$

### Output

Print the number of lines in the plane that pass $K$ or more of the $N$ points, or `Infinity` if there are infinitely many such lines.

------

### Sample Input 1

> 5 2
> 0 0
> 1 0
> 0 1
> -1 0
> 0 -1

### Sample Output 1

> 6

The six lines $x=0$, $y=0$, $y=x\pm 1$, and $y=-x\pm 1$ satisfy the requirement.
For example, $x=0$ passes the first, third, and fifth points.

Thus, $6$ should be printed.

------

### Sample Input 2

> 1 1
> 0 0

### Sample Output 2

> Infinity

Infinitely many lines pass the origin.

Thus, `Infinity` should be printed.

### 我的笔记

由于 $1 \leq N \leq 300$，范围很小，所以完全可以通过遍历来得出答案。

首先在 $N$ 个点中遍历选择两个点 $i$、$j$ ，因为两点可以确定一条直线，因此 $i$、$j$ 确定一条直线。然后再遍历选择第三个点 $k$，$i$、$k$ 也可确定一条直线。若这两条直线斜率相同，那么说明 $i$、$j$、$k$ 共线。遍历完后即可得出该线一共过几个点，若 $\geq K$，则符合条件。这个解法有三层循环，时间复杂度 $O(N^3)$。

因为 $i,j$ 和 $j,i$ 确定的同一条直线，所以遍历时需要注意保证 $i<j<k$，这样就能避免重复遍历同一条直线。同时为了防止平行线重复计算，每一次遍历得到的重合的直线，都用 vis 数组做标记。

### 源码

```cpp
#include <bits/stdc++.h>

using namespace std;

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    const int MAXN = 310;
    int N, K;
    cin >> N >> K;
    int X[MAXN], Y[MAXN];
    for (int i = 0; i < N; i++)
        cin >> X[i] >> Y[i];
    if (K == 1)
    {
        cout << "Infinity" << endl;
    }
    else
    {
        bool vis[MAXN][MAXN]{};
        int ans = 0;
        for (int i = 0; i < N - 1; i++)
        {
            for (int j = i + 1; j < N; j++)
            {
                if (!vis[i][j])
                {
                    vector<int> parallel;
                    parallel.push_back(i);
                    parallel.push_back(j);
                    for (int k = j + 1; k < N; k++)
                    {
                        if (((X[j] - X[i]) * (Y[k] - Y[i])) == ((Y[j] - Y[i]) * (X[k] - X[i])))
                        {
                            parallel.push_back(k);
                        }
                    }
                    for (int k = 0; k < parallel.size() - 1; k++)
                    {
                        for (int l = k + 1; l < parallel.size(); l++)
                        {
                            vis[parallel[k]][parallel[l]] = true;
                        }
                    }
                    if (parallel.size() >= K)
                        ans++;
                }
            }
        }
        cout << ans << endl;
    }
    return 0;
}
```

