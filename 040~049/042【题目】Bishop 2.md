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

### ????????????

??????????????????????????? BFS????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????? +1??????????????????????????????????????????????????? WA?????? 37 ??? WA ?????? 9 ?????????????????????????????????????????????

?????????????????? BFS ?????????????????????

- ?????????????????????????????????????????????????????????
- BFS ???????????????????????? +1???????????????????????? BFS ?????????????????????????????????
- ?????????????????????????????????????????????????????????????????????????????????????????????????????????

?????????????????? BFS ????????????????????????????????????????????? BFS ????????????????????????????????????????????????????????????????????????????????????????????????????????????**01-BFS**

01-BFS ??????????????????

- ????????????????????? deque ????????????????????? queue
- ??????????????????????????????????????????????????????????????????????????????
- ????????????????????????????????????????????????????????????????????????

????????????????????????????????????????????????????????? ??????????????? BFS ???????????????????????????????????? ??????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????? BFS ??????????????????????????????????????????????????????????????????????????????????????????????????????

### ??????

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

