**Codeforces Round 893 (Div. 2)**

D. Trees and Segments

https://codeforces.com/contest/1858/problem/D

<!--more-->

3 seconds / 256 megabytes

standard input / standard output

## Problem

The teachers of the Summer Informatics School decided to plant $n$ trees in a row, and it was decided to plant only oaks and firs. To do this, they made a plan, which can be represented as a binary string $s$ of length $n$. If $s_i = 0$, then the $i$-th tree in the row should be an oak, and if $s_i = 1$, then the $i$-th tree in the row should be a fir.

The day of tree planting is tomorrow, and the day after tomorrow an inspector will come to the School. The inspector loves nature very much, and he will evaluate the beauty of the row as follows:

-  First, he will calculate $l_0$ as the maximum number of consecutive oaks in the row (the maximum substring consisting of zeros in the plan $s$). If there are no oaks in the row, then $l_0 = 0$. 
-  Then, he will calculate $l_1$ as the maximum number of consecutive firs in the row (the maximum substring consisting of ones in the plan $s$). If there are no firs in the row, then $l_1 = 0$. 
-  Finally, he will calculate the beauty of the row as $a \cdot l_0 + l_1$ for some $a$ — the inspector's favourite number. 
The teachers know the value of the parameter $a$, but for security reasons they cannot tell it to you. They only told you that $a$ is an integer from $1$ to $n$.

Since the trees have not yet been planted, the teachers decided to change the type of no more than $k$ trees to the opposite (i.e., change $s_i$ from $0$ to $1$ or from $1$ to $0$ in the plan) in order to maximize the beauty of the row of trees according to the inspector.

For each integer $j$ from $1$ to $n$ answer the following question independently: 

-  What is the maximum beauty of the row of trees that the teachers can achieve by changing the type of no more than $k$ trees if the inspector's favourite number $a$ is equal to $j$? 
## Input

The first line contains a single integer $t$ ($1 \le t \le 1000$) — the number of test cases.

The first line of each test case contains two integers $n$ and $k$ ($1 \le n \le 3000$, $0 \le k \le n$) — the number of trees in the row and the maximum number of changes.

The second line contains a string $s$ of length $n$, consisting of zeros and ones — the plan description.

It is guaranteed that the sum of all $n$ values for all test cases does not exceed $3000$.

## Output

For each test case, print $n$ integers, the $j$-th ($1 \le j \le n$) of which is the maximum beauty of the row of trees after no more than $k$ changes if $a = j$ is used to calculate the beauty.

## Example

Input

> 5
> 3 0
> 111
> 4 1
> 0110
> 5 0
> 10000
> 6 2
> 101101
> 7 1
> 0001101

Output

> 3 3 3
> 4 5 7 9
> 5 9 13 17 21
> 6 9 13 17 21 25
> 7 10 13 17 21 25 29

## Note

In the first test case no changes are allowed, so $l_0 = 0$ and $l_1 = 3$ always hold. Thus, regardless of the value of $a$, the beauty of the row of trees will be $3$.

In the second test case for $a \in \{1, 2\}$ the teachers can, for example, change the plan $s$ to $0111$ (by changing $s_4$), and for $a \in \{3, 4\}$ — to $0010$ (by changing $s_2$). In this case, the beauty of the row for each $a$ is calculated as follows:

-  For $a = 1$: $l_0 = 1$, $l_1 = 3$. The beauty of the row is $1\cdot 1 + 3 = 4$. 
-  For $a = 2$: $l_0 = 1$, $l_1 = 3$. The beauty of the row is $2\cdot 1 + 3 = 5$. 
-  For $a = 3$: $l_0 = 2$, $l_1 = 1$. The beauty of the row is $3\cdot 2 + 1 = 7$. 
-  For $a = 4$: $l_0 = 2$, $l_1 = 1$. The beauty of the row is $4\cdot 2 + 1 = 9$. 
It can be shown that the changes described above are optimal for all $a$ from $1$ to $4$.

## 题解

使用动态规划思想。

#### 状态表示

$dp_{i,j,k}:=$ 从 $1$ 到 $i$ 的区间内，花不超过 $j$ 的代价，能取到的连续 $k$ 串的最大长度。

其中，$k$ 可取 $0/1$，分别代表连续的 $0$ 和连续的 $1$ 的长度。

#### 状态计算

首先进行第一次状态转移：
$$
dp_{i+1,j+c,k}=\max\{dp_{i+1,j+c,k},dp_{i,j,k}+1\}\\
其中:
\begin{cases}
c=0,\;\text{if}\;s_i=k\\
c=1,\;\text{if}\;s_i\neq k\\
\end{cases}
$$
经过这一次转移后，状态的含义是：花**正好** $j$ 的代价，能取到的以第 $i$ 位**为结尾**的连续 $k$ 串的长度。

这个 $i,j$ 的含义和上面状态表示预期的含义还有所不同， 得再做一次转移：
$$
dp_{i,j,k}=\max\{dp_{i,j,k},dp_{i-1,j,k},dp_{i,j-1,k}\}
$$
这样将所有状态进行递推取最大值，$i,j$ 的含义就正确了。

复杂度 $O(n^2)$.

#### 计算答案

题目所述的情况中，一定能找到一个分界点，让最长 $0$ 串和最长 $1$ 串分别位于分界点两侧。

上面的计算只计算了分界点左侧的数据，右侧的数据还未知。要解决这个问题，只需要将 $s$ 逆序后再跑一边 dp（相当于反着跑一遍），得到 $dp'$ 即可.

如果字符串总长为 $n$，代价限制为 $k$，分界点定为 $i$，分配给左边的代价为 $j$，那么：

- 如果左边取 $0$，右边取 $1$
  - 左边 $0$ 串最长为：$dp_{i,j,0}$
  - 右边 $1$ 串最长为：$dp'_{n-i,k-j,1}$
- 如果右边取 $0$，左边取 $1$
  - 右边 $0$ 串最长为：$dp'_{n-i,k-j,0}$
  - 左边 $1$ 串最长为：$dp_{i,j,1}$

为了找到最优解，维护一个 $l_i:=$ $0$ 串长度为 $i$ 时，$1$ 串的最长长度。

最后把每种 $a$ 的情况求出来了，即：
$$
\text{answer(a)}=\max_{i=0}^{n}(a\cdot i+l_i)
$$

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
// #define int long long

using namespace std;

void calc(vector<vector<vector<int>>> &dp, string &s, int k)
{
    int n = s.size();
    for (int i = 0; i < n; i++)
    {
        for (int j = 0; j <= k; j++)
        {
            for (int m = 0; m < 2; m++)
            {
                int cost = (s[i] - '0') != m;
                if (j + cost <= k)
                {
                    dp[i + 1][j + cost][m] = max(dp[i + 1][j + cost][m], dp[i][j][m] + 1);
                }
            }
        }
    }
    for (int i = 0; i <= n; i++)
    {
        for (int j = 0; j <= k; j++)
        {
            for (int m = 0; m < 2; m++)
            {
                if (i - 1 >= 0)
                    dp[i][j][m] = max(dp[i][j][m], dp[i - 1][j][m]);
                if (j - 1 >= 0)
                    dp[i][j][m] = max(dp[i][j][m], dp[i][j - 1][m]);
            }
        }
    }
}

void solve()
{
    int n, k;
    cin >> n >> k;
    string s;
    cin >> s;
    vector<vector<vector<int>>> dp1(n + 10, vector<vector<int>>(k + 10, vector<int>(2)));
    vector<vector<vector<int>>> dp2(n + 10, vector<vector<int>>(k + 10, vector<int>(2)));
    calc(dp1, s, k);
    reverse(s.begin(), s.end());
    calc(dp2, s, k);
    vector<int> l(n + 10, INT32_MIN);
    for (int i = 0; i <= n; i++)
    {
        for (int j = 0; j <= k; j++)
        {
            for (int m = 0; m < 2; m++)
            {
                int a = dp1[i][j][m];
                int b = dp2[n - i][k - j][m ^ 1];
                if (m) // a=1, b=0
                    l[b] = max(l[b], a);
                else // a=0, b=1
                    l[a] = max(l[a], b);
            }
        }
    }
    for (int i = n - 1; i >= 0; i--)
        l[i] = max(l[i], l[i + 1]);
    vector<int> ans(n + 10);
    for (int i = 0; i <= n; i++)
        for (int j = 1; j <= n; j++)
            ans[j] = max(ans[j], i * j + l[i]);
    for (int i = 1; i <= n; i++)
        cout << ans[i] << " \n"[i == n];
}

signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    int t = 1;
    cin >> t;
    while (t--)
        solve();
    return 0;
}
```

