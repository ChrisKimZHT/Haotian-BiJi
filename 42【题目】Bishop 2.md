**AtCoder Beginner Contest 246**

E - Bishop 2

https://atcoder.jp/contests/abc246/tasks/abc246_e

<!--more-->

Time Limit: 6 sec / Memory Limit: 2048 MB

Score : $500$ points

### Problem Statement

We have an $N \times N$ chessboard. Let $(i, j)$ denote the square at the $i$-th row from the top and $j$-th column from the left of this board.
The board is described by $N$ strings $S_i$.
The $j$-th character of the string $S_i$, $S_{i,j}$, means the following.

- If $S_{i,j}=$ `.`, the square $(i, j)$ is empty.
- If $S_{i,j}=$ `#`, the square $(i, j)$ is occupied by a white pawn, which cannot be moved or removed.

We have put a white bishop on the square $(A_x, A_y)$.
Find the minimum number of moves needed to move this bishop from $(A_x, A_y)$ to $(B_x, B_y)$ according to the rules of chess (see Notes).
If it cannot be moved to $(B_x, B_y)$, report `-1` instead.

### Notes

A white [bishop](https://en.wikipedia.org/wiki/Bishop_(chess)) on the square $(i, j)$ can go to the following positions in one move.

- For each positive integer $d$, it can go to $(i+d,j+d)$ if all of the conditions are satisfied.
  - The square $(i+d,j+d)$ exists in the board.
  - For every positive integer $l \le d$, $(i+l,j+l)$ is not occupied by a white pawn.
- For each positive integer $d$, it can go to $(i+d,j-d)$ if all of the conditions are satisfied.
  - The square $(i+d,j-d)$ exists in the board.
  - For every positive integer $l \le d$, $(i+l,j-l)$ is not occupied by a white pawn.
- For each positive integer $d$, it can go to $(i-d,j+d)$ if all of the conditions are satisfied.
  - The square $(i-d,j+d)$ exists in the board.
  - For every positive integer $l \le d$, $(i-l,j+l)$ is not occupied by a white pawn.
- For each positive integer $d$, it can go to $(i-d,j-d)$ if all of the conditions are satisfied.
  - The square $(i-d,j-d)$ exists in the board.
  - For every positive integer $l \le d$, $(i-l,j-l)$ is not occupied by a white pawn.

### Constraints

- $2 \le N \le 1500$
- $1 \le A_x,A_y \le N$
- $1 \le B_x,B_y \le N$
- $(A_x,A_y) \neq (B_x,B_y)$
- $S_i$ is a string of length $N$ consisting of `.` and `#`.
- $S_{A_x,A_y}=$ `.`
- $S_{B_x,B_y}=$ `.`

------

### Input

Input is given from Standard Input in the following format:

> $N$
> $A_x$ $A_y$
> $B_x$ $B_y$
> $S_1$
> $S_2$
> $\vdots$
> $S_N$

### Output

Print the answer.

------

### Sample Input 1

> ```
> 5
> 1 3
> 3 5
> ....#
> ...#.
> .....
> .#...
> #....
> ```

### Sample Output 1

> 3

We can move the bishop from $(1,3)$ to $(3,5)$ in three moves as follows, but not in two or fewer moves.

- $(1,3) \rightarrow (2,2) \rightarrow (4,4) \rightarrow (3,5)$

------

### Sample Input 2

> ```
> 4
> 3 2
> 4 2
> ....
> ....
> ....
> ....
> ```

### Sample Output 2

> -1

There is no way to move the bishop from $(3,2)$ to $(4,2)$.

### Sample Input 3

> ```
> 18
> 18 1
> 1 18
> ..................
> .####.............
> .#..#..####.......
> .####..#..#..####.
> .#..#..###...#....
> .#..#..#..#..#....
> .......####..#....
> .............####.
> ..................
> ..................
> .####.............
> ....#..#..#.......
> .####..#..#..####.
> .#.....####..#....
> .####.....#..####.
> ..........#..#..#.
> .............####.
> ..................
> ```

### Sample Output 3

> 9

### 我的笔记

拿到题第一感觉就是 BFS，并且要储存每一步的方向。当上一步的方向和这一步相同的时候距离不变，如果不同的时候距离 +1。按这个思路写了之后无论怎么改都是 WA，从 37 个 WA 降到 9 个还是不行，说明思路肯定不对。

想了下，这个 BFS 有几个特殊点：

- 同一个点可走多次，不同方向可以各走一次
- BFS 的步数并不是每次 +1，因此有可能出现 BFS 深度靠后的反而距离较近
- 如果一个点从另一个方向到达距离更近，那么从这个点出发的距离都要更新一遍

总之跟普通的 BFS 有挺大的区别，刚开始想用普通的 BFS 打各种补丁来解决这个问题都不行，到最后发现这有个专门的方案解决这个问题：**01-BFS**

01-BFS 有几个区别：

- 使用的双向队列 deque 而不是普通队列 queue
- 每个点的四个方向的距离分别储存，并且各仅可以访问一次
- 如果方向相同，将下一个点进入队首，不同则进入队尾

最重要的就是第一和第三点，第三点解决了 “可能出现 BFS 深度靠后的反而距离较近” 这个问题，因为方向相同走到的点都进入的队首，优先取出，方向不同的点进入队尾，延迟取出。这样就保证了 BFS 深度靠前的距离较近，也就是保证了第一次到达终点时，一定是最短的路径。

### 源码

```cpp
#include <bits/stdc++.h>

using namespace std;

int N;
int Ax, Ay, Bx, By;
bool board[1510][1510];
int dist[1510][1510][4];
bool vis[1510][1510][4];
pair<int, int> dir[4] = {{1, 1}, {1, -1}, {-1, 1}, {-1, -1}};
deque<array<int, 3>> dque;

int bfs()
{
    dist[Ax][Ay][0] = dist[Ax][Ay][1] = dist[Ax][Ay][2] = dist[Ax][Ay][3] = 0;
    vis[Ax][Ay][0] = vis[Ax][Ay][1] = vis[Ax][Ay][2] = vis[Ax][Ay][3] = true;
    for (int i = 0; i < 4; i++)
    {
        int nxt_x = Ax + dir[i].first;
        int nxt_y = Ay + dir[i].second;
        if (nxt_x < 1 || nxt_x > N || nxt_y < 1 || nxt_y > N || board[nxt_x][nxt_y])
            continue;
        dist[nxt_x][nxt_y][i] = 1;
        dque.push_front({nxt_x, nxt_y, i});
    }
    while (!dque.empty())
    {
        auto cur = dque.front();
        dque.pop_front();
        if (cur[0] == Bx && cur[1] == By)
            return dist[cur[0]][cur[1]][cur[2]];
        if (vis[cur[0]][cur[1]][cur[2]])
            continue;
        vis[cur[0]][cur[1]][cur[2]] = true;
        for (int i = 0; i < 4; i++)
        {
            int nxt_x = cur[0] + dir[i].first;
            int nxt_y = cur[1] + dir[i].second;
            if (nxt_x < 1 || nxt_x > N || nxt_y < 1 || nxt_y > N || board[nxt_x][nxt_y])
                continue;
            int tmp = dist[cur[0]][cur[1]][cur[2]];
            if (cur[2] != i)
                tmp++;
            if (tmp < dist[nxt_x][nxt_y][i])
            {
                dist[nxt_x][nxt_y][i] = tmp;
                if (cur[2] == i)
                    dque.push_front({nxt_x, nxt_y, i});
                else
                    dque.push_back({nxt_x, nxt_y, i});
            }
        }
    }
    return -1;
}

int main()
{
    memset(dist, 0x3f, sizeof(dist));
    cin >> N;
    cin >> Ax >> Ay >> Bx >> By;
    for (int i = 1; i <= N; i++)
    {
        string row;
        cin >> row;
        for (int j = 0; j < N; j++)
        {
            if (row[j] == '#')
                board[i][j + 1] = true;
        }
    }
    cout << bfs() << endl;
    return 0;
}
```

