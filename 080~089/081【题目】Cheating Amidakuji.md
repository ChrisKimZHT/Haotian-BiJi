**TOYOTA SYSTEMS Programming Contest 2022(AtCoder Beginner Contest 279)**

E - Cheating Amidakuji

https://atcoder.jp/contests/abc279/tasks/abc279_e

<!--more-->

## Problem Statement

You are given a sequence of length $M$ consisting of integers between $1$ and $N-1$, inclusive: $A=(A_1,A_2,\dots,A_M)$. Answer the following question for $i=1, 2, \dots, M$.

- There is a sequence $B=(B_1,B_2,\dots,B_N)$. Initially, we have $B_j=j$ for each $j$. Let us perform the following operation for $k=1, 2, \dots, i-1, i+1, \dots, M$ in this order (in other words, for integers $k$ between $1$ and $M$ except $i$ in ascending order).
- After all the operations, let $S_i$ be the value of $j$ such that $B_j=1$. Find $S_i$.

## Constraints

- $2 \leq N \leq 2\times 10^5$
- $1 \leq M \leq 2\times 10^5$
- $1 \leq A_i \leq N-1\ (1\leq i \leq M)$
- All values in the input are integers.

## Input

The input is given from Standard Input in the following format:

> $N$ $M$
> $A_1$ $A_2$ $\dots$ $A_M$

## Output

Print $M$ lines. The $i$-th line $(1\leq i \leq M)$ should contain the value $S_i$ as an integer.

## Sample Input 1

> 5 4
> 1 2 3 2

## Sample Output 1

> 1
> 3
> 2
> 4

For $i = 2$, the operations change $B$ as follows.

- Initially, $B = (1,2,3,4,5)$.
- Perform the operation for $k=1$. That is, swap the values of $B_1$ and $B_2$, making $B = (2,1,3,4,5)$.
- Perform the operation for $k=3$. That is, swap the values of $B_3$ and $B_4$, making $B = (2,1,4,3,5)$.
- Perform the operation for $k=4$. That is, swap the values of $B_2$ and $B_3$, making $B = (2,4,1,3,5)$.

After all the operations, we have $B_3=1$, so $S_2 = 3$.

Similarly, we have the following.

- For $i=1$: performing the operation for $k=2,3,4$ in this order makes $B=(1,4,3,2,5)$, so $S_1=1$.
- For $i=3$: performing the operation for $k=1,2,4$ in this order makes $B=(2,1,3,4,5)$, so $S_3=2$.
- For $i=4$: performing the operation for $k=1,2,3$ in this order makes $B=(2,3,4,1,5)$, so $S_4=4$.

## Sample Input 2

> 3 3
> 2 2 2

## Sample Output 2

> 1
> 1
> 1

## Sample Input 3

> 10 10
> 1 1 1 9 4 4 2 1 3 3

## Sample Output 3

> 2
> 2
> 2
> 3
> 3
> 3
> 1
> 3
> 4
> 4

## 题解

**简化版问题**

我们先不考虑被跳过的那个 $i$，只思考：给出一个交换操作序列，如何维护 $1$ 在每一次操作时的位置。

这个问题非常简单就能想到结果。不妨设这次操作将要交换第 $t$ 个和第 $t+1$ 个数，$1$ 目前所在的位置为 $p$，若：

- $p=t$ ：$1$ 会与其后面的那个数交换，即 $p$ 增大 $1$.
- $p=t+1$ : $1$ 会与其前面的那个数交换，即 $p$ 减小 $1$.
- 其他情况 : 交换操作与 $1$ 无关，即 $p$ 的大小不变。

由此，我们就能在 $O(n)$ 时间内，求出按 $k=1,2,\dots,M$ 顺序执行每一步操作后，$1$ 目前所在的位置 $pos_k$ **（对应下方代码 A 部分）**

**将原问题拆分**

原问题问的是 $k=1, 2, \dots, i-1, i+1, \dots, M$ 操作后，$1$ 目前所在的位置。那么我们可以将问题拆分为：

- 进行 $k=1, 2, \dots, i-1$ 操作后，$1$ 目前所在的位置。这个问题就是上文的问题，我们可以在 $O(1)$ 时间内取得结果，即 $pos_{i-1}$.
- $1$ 目前在 $pos_{i-1}$ 位置，再经过 $k=i+1, \dots, M$ 这些操作后，$1$ 的新位置在哪？

下面我们再解决第二个问题，整道题目就做出来了。

**如何快速求得每个数经过操作后的新位置**

这个问题有一个巧妙地思路，我们初始化数列的数值等于其下标，如 $[1,2,3,4,\dots]$，然后进行操作后，数列的数值就是这个位置经过操作后的新位置，比如结果是 $[2,5,1,4,3]$，就说明第 $1$ 个位置操作后在第 $2$ 个位置、第 $2$ 个位置操作后在第 $5$ 个位置、$\dots$**（对应下方代码 B 部分）**

为了能够实现递推，减少复杂度，我们是反向进行操作的，最后记得把答案再倒过来输出就行。**（对应下方代码 C 部分）**

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'

using namespace std;

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    int N, M;
    cin >> N >> M;
    vector<int> A(M + 1);
    for (int i = 1; i <= M; i++)
        cin >> A[i];
    /* Part A */
    vector<int> pos(M + 1);
    pos[0] = 1;
    for (int i = 1; i <= M; i++)
    {
        if (A[i] == pos[i - 1])
            pos[i] = pos[i - 1] + 1;
        else if (A[i] == pos[i - 1] - 1)
            pos[i] = pos[i - 1] - 1;
        else
            pos[i] = pos[i - 1];
    }
    /* Part B */
    vector<int> where(N + 1), ans;
    for (int i = 1; i <= N; i++)
        where[i] = i;
    for (int i = M; i >= 1; i--)
    {
        ans.push_back(where[pos[i - 1]]);
        swap(where[A[i]], where[A[i] + 1]);
    }
    /* Part C */
    for (int i = ans.size() - 1; i >= 0; i--)
        cout << ans[i] << endl;
    return 0;
}
```

