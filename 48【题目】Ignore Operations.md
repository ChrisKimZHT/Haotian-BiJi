**Monoxer Programming Contest 2022（AtCoder Beginner Contest 249）**

F - Ignore Operations

https://atcoder.jp/contests/abc249/tasks/abc249_f

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $500$ points

### Problem Statement

Takahashi has an integer $x$. Initially, $x = 0$.

There are $N$ operations. The $i$-th operation $(1 \leq i \leq N)$ is represented by two integers $t_i$ and $y_i$ as follows:

- If $t_i = 1$, replace $x$ with $y_i$.
- If $t_i = 2$, replace $x$ with $x + y_i$.

Takahashi may skip any number between $0$ and $K$ (inclusive) of the operations. When he performs the remaining operations once each without changing the order, find the maximum possible final value of $x$

### Constraints

- $1 \leq N \leq 2 \times 10^5$
- $0 \leq K \leq N$
- $t_i \in \{1,2\} \, (1 \leq i \leq N)$
- $|y_i| \leq 10^9 \, (1 \leq i \leq N)$
- All values in input are integers.

------

### Input

Input is given from Standard Input in the following format:

> $N$ $K$
> $t_1$ $y_1$
> $\vdots$
> $t_N$ $y_N$

### Output

Print the answer.

------

### Sample Input 1

> 5 1
> 2 4
> 2 -3
> 1 2
> 2 1
> 2 -3

### Sample Output 1

> 3

If he skips the $5$-th operation, $x$ changes as $0 \rightarrow 4 \rightarrow 1 \rightarrow 2 \rightarrow 3$, so $x$ results in $3$. This is the maximum.

------

### Sample Input 2

> 1 0
> 2 -1000000000

### Sample Output 2

> -1000000000

------

### Sample Input 3

> 10 3
> 2 3
> 2 -1
> 1 4
> 2 -1
> 2 5
> 2 -9
> 2 2
> 1 -6
> 2 5
> 2 -3

### Sample Output 3

> 15

### 我的笔记

对于操作 $1$，需要知道的性质是：如果选择了该操作的数字，那么该操作之前的所有操作都是无意义的，不会影响结果。

**反向遍历所有操作：**

对于操作 $2$，如果数字 $\geq0$，那么当然收下，加到 $added$ 里面。如果数字 $<0$，那么暂存在一个大根堆内。

对于操作 $1$，如果不跳过该操作，计算一次 $ans=\max(ans,y_i+added)$。然后再跳过该操作，$K$ 减一。

然后需要判断堆的大小。

如果堆的大小 $\leq$ 当前还能跳过的次数，那么就可以把所有的都跳过，避免加负数到 $added$ 内。如果堆的大小 $>$ 当前还能跳过的次数，那么就只能加最大的负数到 $added$ 内。

注意如果结束时 $K>=0$，还需计算一次 $ans=\max(ans,y_i+added)$，防止初始为 $0$ 的情况遗漏。

### 代码

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN = 2e5 + 10;
int N, K;
int t[MAXN], y[MAXN];
priority_queue<int> pque;

int main()
{
    cin >> N >> K;
    for (int i = N; i > 0; i--)
        cin >> t[i] >> y[i];
    long long ans = INT64_MIN, added = 0;
    for (int i = 1; i <= N; i++)
    {
        if (t[i] == 2)
        {
            if (y[i] >= 0)
                added += y[i];
            else
                pque.push(y[i]);
        }
        else
        {
            ans = max(ans, y[i] + added);
            K--;
        }
        while (pque.size() > K)
        {
            added += pque.top();
            pque.pop();
        }
        if (K < 0)
            break;
    }
    if (K >= 0)
        ans = max(ans, added);
    cout << ans << endl;
    return 0;
}
```

