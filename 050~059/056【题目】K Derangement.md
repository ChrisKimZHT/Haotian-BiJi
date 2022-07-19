**AtCoder Regular Contest 144**

C - K Derangement

https://atcoder.jp/contests/arc144/tasks/arc144_c

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $600$ points

### Problem Statement

You are given positive integers $N$ and $K$. Find the lexicographically smallest permutation $A = (A_1, A_2, \ldots, A_N)$ of the integers from $1$ through $N$ that satisfies the following condition:

- $\lvert A_i - i\rvert \geq K$ for all $i$ ($1\leq i\leq N$).

If there is no such permutation, print `-1`.

### Constraints

- $2\leq N\leq 3\times 10^5$
- $1\leq K\leq N - 1$

------

### Input

Input is given from Standard Input in the following format:

> $N$ $K$

### Output

Print the lexicographically smallest permutation $A = (A_1, A_2, \ldots, A_N)$ of the integers from $1$ through $N$ that satisfies the condition, in the following format:

> $A_1$ $A_2$ $\ldots$ $A_N$

If there is no such permutation, print `-1`.

------

### Sample Input 1

> 3 1

### Sample Output 1

> 2 3 1

Two permutations satisfy the condition: $(2, 3, 1)$ and $(3, 1, 2)$. For instance, the following holds for $(2, 3, 1)$:

- $\lvert A_1 - 1\rvert = 1 \geq K$
- $\lvert A_2 - 2\rvert = 1 \geq K$
- $\lvert A_3 - 3\rvert = 2 \geq K$

------

### Sample Input 2

> 8 3

### Sample Output 2

> 4 5 6 7 8 1 2 3

------

### Sample Input 3

> 8 6

### Sample Output 3

> -1

------

### 解题笔记

首先需要找到存在这种排列的必要条件，即 $N\geq2K$。因为第 $K$ 位要么取  $K-K=0$，要么取 $K+K=2K$，由于 $0$ 不符合题意，则第 $K$ 位必须取 $2K$。要满足这个条件，必须有 $N\geq2K$。

接下来的重点就是找到通项公式。分为四种情况：

- $N<2K$: 无解

- $2K\leq N\leq 3K$:
  $$
  A(i) = 
  \begin{cases}
  i + K & (1\leq i \leq N-K) \\
  i - N + K & (N-K < i\leq N)
  \end{cases}
  $$

- 

- $3K<N\leq 4K$:
  $$
  A(i) = 
  \begin{cases}
  i+K &(1\leq i \leq K)\\
  i-K &(K<i\leq N-2K)\\
  i + K &(N-2K< i\leq N-K)\\
  i-2K &(N-K < i \leq 3K)\\
  i-K &(3K < i \leq N)
  \end{cases}
  $$

- $4K<N$:
  $$
  A(i) = 
  \begin{cases}
  i + K & (1\leq i \leq K) \\
  i - K & (K < i\leq 2K) \\
  A(i) & (2K<i\leq N)
  \end{cases}
  $$
  这个意思是前 $2K$ 位为  $(K+1, \ldots, 2K, 1, \ldots, K)$，剩余的部分的 $N'=N-2K$，再选择符合 $N'$ 大小的公式。不断使用情况四的公式进行递推，一定可以转化为情况二或情况三。

### 代码

```cpp
#include <bits/stdc++.h>

using namespace std;

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    int N, K;
    cin >> N >> K;
    if (2 * K > N)
    {
        cout << -1 << endl;
        return 0;
    }
    int cnt = 0;
    while (N - cnt >= 4 * K)
    {
        for (int i = 1; i <= K; i++)
            cout << cnt + i + K << ' ';
        for (int i = 1; i <= K; i++)
            cout << cnt + i << ' ';
        cnt += 2 * K;
    }
    if (N - cnt <= 3 * K)
    {
        int i = 1;
        for (; i <= (N - cnt) - K; i++)
            cout << i + K + cnt << ' ';
        for (; i <= (N - cnt); i++)
            cout << i - (N - cnt) + K + cnt << ' ';
    }
    else
    {
        int i = 1;
        for (; i <= K; i++)
            cout << i + K + cnt << ' ';
        for (; i <= (N - cnt) - 2 * K; i++)
            cout << i - K + cnt << ' ';
        for (; i <= (N - cnt) - K; i++)
            cout << i + K + cnt << ' ';
        for (; i <= 3 * K; i++)
            cout << i - 2 * K + cnt << ' ';
        for (; i <= (N - cnt); i++)
            cout << i - K + cnt << ' ';
    }
    cout << endl;
    return 0;
}
```

