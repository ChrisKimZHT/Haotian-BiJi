**POJ 3046. Ant Counting**

动态规划典型例题

http://poj.org/problem?id=3046

<!--more-->

**Time Limit:** 1000MS

**Memory Limit:** 65536K

## Description

Bessie was poking around the ant hill one day watching the ants march to and fro while gathering food. She realized that many of the ants were siblings, indistinguishable from one another. She also realized the sometimes only one ant would go for food, sometimes a few, and sometimes all of them. This made for a large number of different sets of ants!

Being a bit mathematical, Bessie started wondering. Bessie noted that the hive has T (1 <= T <= 1,000) families of ants which she labeled 1..T (A ants altogether). Each family had some number Ni (1 <= Ni <= 100) of ants.

How many groups of sizes S, S+1, ..., B (1 <= S <= B <= A) can be formed?

While observing one group, the set of three ant families was seen as {1, 1, 2, 2, 3}, though rarely in that order. The possible sets of marching ants were:

3 sets with 1 ant: {1} {2} {3}
5 sets with 2 ants: {1,1} {1,2} {1,3} {2,2} {2,3}
5 sets with 3 ants: {1,1,2} {1,1,3} {1,2,2} {1,2,3} {2,2,3}
3 sets with 4 ants: {1,2,2,3} {1,1,2,2} {1,1,2,3}
1 set with 5 ants: {1,1,2,2,3}

Your job is to count the number of possible sets of ants given the data above.

## Input

\* Line 1: 4 space-separated integers: T, A, S, and B

\* Lines 2..A+1: Each line contains a single integer that is an ant type present in the hive

## Output

\* Line 1: The number of sets of size S..B (inclusive) that can be created. A set like {1,2} is the same as the set {2,1} and should not be double-counted. Print only the LAST SIX DIGITS of this number, with no leading zeroes or spaces.

## Sample Input

> 3 5 2 3
> 1
> 2
> 2
> 1
> 3

## Sample Output

> 10

## Hint

INPUT DETAILS:

Three types of ants (1..3); 5 ants altogether. How many sets of size 2 or size 3 can be made?


OUTPUT DETAILS:

5 sets of ants with two members; 5 more sets of ants with three members

## 题解和代码

#### 模型构建

我们将 $T$ 种蚂蚁对应为 $T$ 种物品，第 $i$ 种蚂蚁的数量 $cnt_i$ 对应于物品 $i$ 的数量 $cnt_i$，每种蚂蚁都对应体积为 $1$ 的物品，集合的大小就对应于背包容积。

如此，这道题就能构建一个[多重背包模型](https://io.zouht.com/49.html)，不过算的是取法数量而不是最大价值。于是有下面的朴素写法。

#### 朴素写法

- 时间复杂度：$O(A\cdot B)$
- 空间复杂度：$O(T\cdot B)$（可用滚动数组优化为 $O(B)$）

- 状态表示：$dp(i,j):=$ 在前 $i$ 种蚂蚁里选 $j$ 个蚂蚁的选法数。

- 状态计算：$\displaystyle{dp(i,j)=\sum_{k=0}^{\min(j,cnt_i)}{dp(i-1,j-k)}}$

很明显这种算法时间复杂度是不合理的，因为 $A,B$ 的数量级为 $10^5$，相乘得到 $10^{10}$ 肯定是要超时的。但是提交上去却是 AC，可见测试样例比较宽松。

```cpp
#include <iostream>
#define endl '\n'

using namespace std;

const int MAXT = 1010, MAXB = 100010, MOD = 1e6;
int T, A, S, B;
int cnt[MAXT];
int dp[MAXT][MAXB];

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    cin >> T >> A >> S >> B;
    for (int i = 0; i < A; i++)
    {
        int t;
        cin >> t;
        cnt[t]++;
    }
    for (int i = 0; i <= T; i++)
        dp[i][0] = 1;
    // BEGIN
    for (int i = 1; i <= T; i++)
        for (int j = 1; j <= B; j++)
            for (int k = 0; k <= min(j, cnt[i]); k++)
                dp[i][j] = (dp[i][j] + dp[i - 1][j - k]) % MOD;
    // END
    int ans = 0;
    for (int i = S; i <= B; i++)
        ans = (ans + dp[T][i]) % MOD;
    cout << ans << endl;
    return 0;
}
```

#### 时间优化

- 时间复杂度：$O(T\cdot B)$
- 空间复杂度：$O(T\cdot B)$（可用滚动数组优化为 $O(B)$）

我们观察状态计算中的连加循环，这个是优化的关键：
$$
\begin{align}
dp(i,j-1)&=\sum_{k=0}^{\min(j-1,cnt_i)}{dp(i-1,j-1-k)}\\
&=\begin{cases}
    \sum_{k=0}^{cnt_i}{dp(i-1,j-1-k)},\;cnt_i< j\\
    \sum_{k=0}^{j-1}{dp(i-1,j-1-k)},\;j\leq cnt_i
\end{cases}\\
&=\begin{cases}
    \sum_{k=1}^{cnt_i+1}{dp(i-1,j-k)},\;cnt_i< j\\
    \sum_{k=1}^{j}{dp(i-1,j-k)},\;j\leq cnt_i
\end{cases}\\
\end{align}
$$
代入上式，我们能将 $dp(i,j)$ 改写：
$$
\begin{align}
dp(i,j)=&\sum_{k=0}^{\min(j,cnt_i)}{dp(i-1,j-k)}\\
=&\begin{cases}
    \sum_{k=0}^{cnt_i}{dp(i-1,j-k)},\;cnt_i< j\\
    \sum_{k=0}^{j}{dp(i-1,j-k)},\;j\leq cnt_i
\end{cases}\\
\overset{代入}=&\begin{cases}
    dp(i,j-1)+dp(i-1,j)-dp(i-1,j-cnt_i-1),\;cnt_i< j\\
    dp(i,j-1)+dp(i-1,j),\;j\leq cnt_i
\end{cases}\\
\end{align}
$$
如此我们就能省去计算这个连加的循环，时间复杂度来到 $10^8$ 数量级。

```cpp
// 将上面的代码注释之间的循环替换为：
for (int i = 1; i <= T; i++)
{
    for (int j = 1; j <= B; j++)
    {
        if (cnt[i] < j)
            dp[i][j] = ((dp[i][j - 1] + dp[i - 1][j] - dp[i - 1][j - cnt[i] - 1]) % MOD + MOD) % MOD;
        else
            dp[i][j] = (dp[i][j - 1] + dp[i - 1][j]) % MOD;
    }
}
```

#### 空间优化

- 时间复杂度：$O(T\cdot B)$
- 空间复杂度：$O(B)$

使用滚动数组即可，这次不能只用一个一维数组来解决，因为第二层循环时，既要用到循环前的数值，也要用到循环后的数值，因此无论正向还是反向循环，一维数组都是无法实现的。所以只能使用两个一维数组。

```cpp
#include <iostream>
#define endl '\n'

using namespace std;

const int MAXT = 1010, MAXB = 100010, MOD = 1e6;
int T, A, S, B;
int cnt[MAXT];
int dp[2][MAXB];

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    cin >> T >> A >> S >> B;
    for (int i = 0; i < A; i++)
    {
        int t;
        cin >> t;
        cnt[t]++;
    }
    dp[0][0] = dp[1][0] = 1;
    for (int i = 1; i <= T; i++)
    {
        for (int j = 1; j <= B; j++)
        {
            dp[i % 2][j] = (dp[i % 2][j - 1] + dp[(i - 1) % 2][j]) % MOD;
            if (j - cnt[i] - 1 >= 0)
                dp[i % 2][j] = ((dp[i % 2][j] - dp[(i - 1) % 2][j - cnt[i] - 1]) % MOD + MOD) % MOD;
        }
    }
    int ans = 0;
    for (int i = S; i <= B; i++)
        ans = (ans + dp[T % 2][i]) % MOD;
    cout << ans << endl;
    return 0;
}
```

