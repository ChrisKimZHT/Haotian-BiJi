**Codeforces Global Round 23**

C. Permutation Operations

https://codeforces.com/contest/1746/problem/C

<!--more-->

2 seconds / 256 megabytes

standard input / standard output

### Problem

You are given a permutation $a$ of size $n$ and you should perform $n$ operations on it. In the $i$-th operation, you can choose a non-empty suffix of $a$ and increase all of its elements by $i$. How can we perform the operations to minimize the number of inversions in the final array?

Note that you can perform operations on the same suffix any number of times you want.

A permutation of size $n$ is an array of size n such that each integer from $1$ to $n$ occurs exactly once in this array. A suffix is several consecutive elements of an array that include the last element of the array. An inversion in an array $a$ is a pair of indices $(i, j)$ such that $i > j$ and $a_{i} < a_{j}$.

### Input

Each test contains multiple test cases. The first line contains the number of test cases $t$ ($1 \le t \le 10^4$). The description of the test cases follows.

The first line of each test case contains a single integer $n$ ($1 \le n \le 10^5$) — the size of the array.

The second line contains $n$ distinct integers $a_{1}, a_{2}, \dots, a_{n}$ ($1 \le a_i \le n$), the initial permutation $a$.

It's guaranteed that the sum of $n$ over all test cases does not exceed $2 \cdot 10^5$.

### Output

For each test case, print $n$ integers $x_{1}, x_{2}, \ldots, x_{n}$ ($1 \le x_{i} \le n$ for each $1 \le i \le n$) indicating that the $i$-th operation must be applied to the suffix **starting at index** $x_{i}$. If there are multiple answers, print any of them.

### Example

input

> 4
> 4
> 1 2 3 4
> 5
> 1 3 2 4 5
> 3
> 2 3 1
> 1
> 1

output

> 1 1 1 1
> 1 4 3 2 1
> 1 3 3
> 1

### Note

In the first test case one of the optimal solutions is to increase the whole array on each operation (that is, choose the suffix starting at index $1$). The final array $[11, 12, 13, 14]$ contains $0$ inversions.

In the second test case, a will be equal to $[2, 4, 3, 5, 6]$, $[2, 4, 3, 7, 8]$, $[2, 4, 6, 10, 11]$, $[2, 8, 10, 14, 15]$ and $[7, 13, 15, 19, 20]$ after the first, second, third, fourth, and fifth operations, respectively. So the final array $a$ has zero inversions.

### 题解

首先我们要能猜到，对任意排列进行 $n$ 次该操作，一定有方法得到一个单调不减序列，即逆序对数目一定能变为 $0$. 下面来解释为什么：

- 对于一个数列 $[a_1,a_2,a_3,a_4,\dots]$，令 $[b_1,b_2,b_3,b_4,\dots]$ 为它的差分数列 $[a_1,a_2-a_1,a_3-a_2,a_4-a_3,\dots]$
- 原数列单调不减，即意味着它的差分数列非负，即 $b_i\geq0$.
- 将 $a_k,a_{k+1},\dots,a_n$ 加 $i$ 的操作，即将差分数列中的 $b_k$ 的值增加 $k$.
- 由于原数列为一个排列，意味着 $a_i>0$，则 $b_i=a_i-a_{i-1}>-a_{i-1}$.

根据以上已知条件，我们可以构造出一种方案，保证操作后，数列为单调不减数列：

- 对于每个 $a_i$ ($i=1,2,\dots,n-1$)，将 $a_{i+1},\dots,a_n$ 加 $a_i$

因为 $b_{i+1}=a_{i+1}-a_{i}>-a_i$，进行上面的操作后，$b_{i+1}+a_i=a_{i+1}>0$，可以满足数列单调不减。同时，由于 $a_i$ 是一个排列，因此 $a_i\neq a_j$ (如果 $i\neq j$) 且 $a_i\in[1,n]$. 我们有 $n$ 次操作，正好一一对应一个 $a_i$.

当然，大家也可能注意到，上面括号内有条件 $i=1,2,\dots,n-1$。若 $i=n$ 时，$a_{i+1}$ 不存在。实际上，这种情况的怎么处理不影响最终结果，可以随意输出。下面代码的处理方式为 `i % n + 1`，这种情况将会输出为 $1$，即整个序列一起增加。

### 代码

- 时间复杂度：$O(n)$
- 空间复杂度：$O(n)$

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN = 1e5 + 10;
int ans[MAXN];

void solve()
{
    int n;
    cin >> n;
    for (int i = 1; i <= n; i++)
    {
        int t;
        cin >> t;
        ans[t] = i % n + 1;
    }
    for (int i = 1; i <= n; i++)
        cout << ans[i] << ' ';
    cout << endl;
}

int main()
{
    int t;
    cin >> t;
    while (t--)
        solve();
    return 0;
}
```

