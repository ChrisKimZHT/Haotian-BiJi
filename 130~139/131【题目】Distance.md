**2023牛客暑期多校训练营6**

B - Distance

https://ac.nowcoder.com/acm/contest/57360/B

<!--more-->

## 题意

给定长度为 $n$ 的两个序列 $\{s\}_{i=1}^n$ 和 $\{t\}_{i=1}^n$, 从中选出两个大小相等的集合 $A \subseteq S, B \subseteq T$, 每次可以选择 $x \in A$ 或 $y \in B$ 并执行 $x \leftarrow x+1$ 或 $y \leftarrow y+1$。记让 $A=B$ 的最小操作次数为 $C(A, B)$, 求 $\sum_{A \subseteq S} \sum_{B \subseteq T} C(A, B) \bmod 998244353$ 。 $1 \leq n \leq 3 \times 10^3, 1 \leq a_i, b_i \leq 10^9$ 。

## 题解

可以选择 $A$ 或 $B$ 中的任意一个数对它 $+1$，由于题目目标是将 $A,B$ 变成一样的，所以这个操作其实等价于：选择 $A$ 中的任意一个数 $a_i$ 对它 $+1$ 或 $-1$. 那么将 $a_i$ 变成 $x$ 的代价就是 $|a_i-x|$.

对于一对大小为 $m$ 的 $A,B$，首先分别对其排序，根据上面的转化，那么将 $A,B$ 变成一样的总代价就是 $C(A,B)=\sum_{i=0}^{m}|a_i-b_i|$，即考虑按下标一一对应进行变化时代价是最小的。

其实不按照下标一一对应，也可以是代价一样最小的变换方式。但是按下标一一对应进行变换能够方便后续的计算，因此我们不妨设变换方式就是按照下标一一对应。

然后，该题思路是对于枚举每一对 $s_i,t_j$，计算当它们在 $A,B$ 同一下标时对答案的贡献。若用 $cnt$ 代表 $s_i,t_j$ 在 $A,B$ 同一下标的情况总数，那么对答案的贡献便是：$cnt\cdot|s_i-t_j|$. 因此，现在问题的关键就变成了怎么计算这个情况总数 $cnt$.

考虑下面这个示例，被框住的数字是目前正在考虑的数字：
$$
\begin{matrix}
s: & 1 & 3 & \boxed4 & 6 & 9\\
t: & 3 & \boxed6 & 7 & 9 & 10
\end{matrix}
$$
那么能让 $4,6$ 处于同一下标的子区间可能性有：

- 左边选 $0$ 个，右边选 $0$ 个：长度为 $1$，选中数字处于下标 $1$，共 $1$ 种。
- 左边选 $0$ 个，右边选 $1$ 个：长度为 $2$，选中数字处于下标 $1$，共 $6$ 种。
- 左边选 $0$ 个，右边选 $2$ 个：长度为 $3$，选中数字处于下标 $1$，共 $3$ 种。
- 左边选 $1$ 个，右边选 $0$ 个：长度为 $2$，选中数字处于下标 $2$，共 $2$ 种。
- 左边选 $1$ 个，右边选 $1$ 个：长度为 $3$，选中数字处于下标 $2$，共 $12$ 种。
- 左边选 $1$ 个，右边选 $2$ 个：长度为 $4$，选中数字处于下标 $2$，共 $6$ 种。

共 $30$ 种情况，因此这种情况的贡献为 $|4-6|\times30=60$.

接下来分析一下为什么是这么多。对于一对 $i,j$，$s_i$ 左侧有 $i-1$ 个数，右侧有 $n-i$ 个数，$t_j$ 左侧有 $j-1$ 个数，右侧有 $n-j$ 个数。

那么选择的区间左侧最多放 $\min\{i-1,j-1\}$ 个数，右侧最多放 $\min\{n-i,n-j\}$ 个数，否则就会破坏下标对应关系。

（左右侧的计算是等价的，下面都只以右侧为例）那么右侧的选择数的情况有：
$$
\sum_{k=0}^{\min\{n-i,n-j\}}C_{n-i}^{k}
$$
但上面只考虑了选择的方式，实际上还要考虑选出来的数怎么对应。例如上面这个例子，如果 $s_i$ 右侧选择 $6,9$，那么 $t_j$ 右侧可以选择 $7,9/7,10/9,10$ 这三种进行对应。

因此，右侧的情况数应该是：
$$
\sum_{k=0}^{\min\{n-i,n-j\}}C_{n-i}^{k}C_{n-j}^{k}
$$
计算这个情况数的时间复杂度是 $O(n)$ 的，那总时间复杂度会来到 $O(n^3)$ 而超时，因此必须要化简。

根据[范德蒙德卷积公式](https://oi-wiki.org/math/combinatorics/vandermonde-convolution/)：
$$
\sum_{i=0}^{k}C_{n}^{i}C_{m}^{k-i}=C_{n+m}^{k}
$$
它有推论：
$$
\sum_{i=0}^{k}C_{n}^{i}C_{m}^{i}=C_{n+m}^{m}
$$
这个推论正好和上面的情况数对应上了，于是情况数转化成：
$$
\sum_{k=0}^{\min\{n-i,n-j\}}C_{n-i}^{k}C_{n-j}^{k}=C_{(n-i)+(n-j)}^{n-i}
$$
这下就能在 $O(1)$ 时间得到一对 $s_i,t_j$ 的贡献了，那么该题的总答案为：
$$
\text{answer}=\sum_{i=1}^{n}\sum_{j=1}^{n}\left(|s_i-t_j|\cdot C_{(i-1)+(j-1)}^{i-1} \cdot C_{(n-i)+(n-j)}^{n-i}\right)
$$
预处理组合数后，时间复杂度为 $O(n^2)$.

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

constexpr int MAXN = 4e3 + 10, MOD = 998244353;
int comb[MAXN][MAXN];

void init_comb()
{
    for (int i = 0; i < MAXN; i++)
        comb[i][0] = 1;
    for (int i = 1; i < MAXN; i++)
        for (int j = 1; j < MAXN; j++)
            comb[i][j] = (comb[i - 1][j] + comb[i - 1][j - 1]) % MOD;
}

int n;
int s[MAXN], t[MAXN];

void solve()
{
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> s[i];
    for (int i = 1; i <= n; i++)
        cin >> t[i];
    sort(s + 1, s + 1 + n);
    sort(t + 1, t + 1 + n);
    int ans = 0;
    for (int i = 1; i <= n; i++)
    {
        for (int j = 1; j <= n; j++)
        {
            int li = i - 1, lj = j - 1;
            int ri = n - i, rj = n - j;
            int lcnt = comb[li + lj][li];
            int rcnt = comb[ri + rj][ri];
            int delta = abs(s[i] - t[j]) % MOD;
            ans = (ans + lcnt * rcnt % MOD * delta % MOD) % MOD;
        }
    }
    cout << ans << endl;
}

signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    init_comb();
    int t = 1;
    // cin >> t;
    while (t--)
        solve();
    return 0;
}
```

