**Codeforces Round #841 (Div. 2) and Divide by Zero 2022**

C. Even Subarrays

https://codeforces.com/contest/1731/problem/C

<!--more-->

2.5 seconds / 256 megabytes

standard input / standard output

### Problem

You are given an integer array $a_1, a_2, \dots, a_n$ ($1 \le a_i \le n$).

Find the number of subarrays of $a$ whose $\operatorname{XOR}$ has an even number of divisors. In other words, find all pairs of indices $(i, j)$ ($i \le j$) such that $a_i \oplus a_{i + 1} \oplus \dots \oplus a_j$ has an even number of divisors.

For example, numbers $2$, $3$, $5$ or $6$ have an even number of divisors, while $1$ and $4$ — odd. Consider that $0$ has an odd number of divisors in this task.

Here $\operatorname{XOR}$ (or $\oplus$) denotes the [bitwise XOR operation](https://en.wikipedia.org/wiki/Bitwise_operation#XOR).

~~Print the number of subarrays but multiplied by 2022...~~ Okay, let's stop. Just print the actual answer.

### Input

Each test contains multiple test cases. The first line contains the number of test cases $t$ ($1 \leq t \leq 10^4$). Description of the test cases follows.

The first line of each test case contains a single integer $n$ ($2 \leq n \leq 2 \cdot 10^5$) — the length of the array $a$.

The second line contains $n$ integers $a_1, a_2, \dots, a_n$ ($1 \leq a_i \leq n$).

It is guaranteed that the sum of $n$ over all test cases does not exceed $2 \cdot 10^5$. 

### Output

For each test case, print the number of subarrays, whose $\operatorname{XOR}$ has an even number of divisors.

### Example

Input

> 4
> 3
> 3 1 2
> 5
> 4 2 1 5 3
> 4
> 4 4 4 4
> 7
> 5 7 3 7 1 7 3

Output

> 4
> 11
> 0
> 20

### Note

In the first test case, there are $4$ subarrays whose $\operatorname{XOR}$ has an even number of divisors: $[3]$, $[3,1]$, $[1,2]$, $[2]$.

In the second test case, there are $11$ subarrays whose $\operatorname{XOR}$ has an even number of divisors: $[4,2]$, $[4,2,1]$, $[4,2,1,5]$, $[2]$, $[2,1]$, $[2,1,5]$, $[2,1,5,3]$, $[1,5,3]$, $[5]$, $[5,3]$, $[3]$.

In the third test case, there is no subarray whose $\operatorname{XOR}$ has an even number of divisors since $\operatorname{XOR}$ of any subarray is either $4$ or $0$.

### 题解

先从最朴素的角度出发思考。

#### 朴素思路

由于偶数次异或同一个数，原数值不变：$x=x\,\operatorname{XOR}\,a\,\operatorname{XOR}\,a$. 我们可以效仿前缀和，预处理前缀 XOR 得到 $prexor_i$ 序列，子序列的 XOR 值就为：
$$
\underset{i=l}{\overset{r}{\operatorname{XOR}}}\,a_i=prexor_r\,\operatorname{XOR}\,prexor_{l-1}
$$
然后用二重循环暴力遍历所有子序列，即可解决该题，时间复杂度  $O(n^2)$ . 那现在面临的新问题就是：

#### 什么数的因子数为偶数？

对于一个数 $n$，如果有因子 $a$，那么一定对应令一个因子 $b=n/a$

也就是说，$n$ 的因子一定能分成 $<\sqrt n$ 和 $>\sqrt n$ 这两类，并且这两类中的因子一一对应、数量相等，将每一类的数量记为 $k$.

除了上述两类因子，还有一类比较特别，为 $=\sqrt n$，只有完全平方数具有这种因子，这种因子只有 $1$ 个。

综上，我们可以知道，完全平方数的因子个数为 $2k+1$，非完全平方数的因子个数为 $2k$. 

即：完全平方数的因子数为奇数，非完全平方数为偶数。

解决了这个问题，我们就获得了一个 $O(n^2)$ 的超时算法，不过我们可以在此基础上继续思考。

上述思路，我们遍历得到了每个子序列的 XOR 值，然后判断是否是完全平方数。我们可以发现，子序列数量为 $n^2=10^{10}$ 级别，而完全平方数的数量为 $\sqrt n=500$ 级别。那么我们可以逆向思维：

#### 对于每个完全平方数，是否存在子序列的 XOR 值等于它？

我们在上文第一节讲的异或的特性，可以知道，如果我们已知 $a,c$，要求一个数 $b$，使它满足：$a\,\operatorname{XOR}\,b=c$，我们可以变形得到：$b=c\,\operatorname{XOR}\,a$

对于本题，该式便是：
$$
b=prexor_i\,\operatorname{XOR}\,k^2
$$
其中，$presum_i$ 为第一层循环，代表子序列右端点。

$k^2$ 代表枚举的完全平方数，其中 $k\leq\sqrt n$. 

$b$ 代表着满足子序列 XOR 为 $k^2$ 的子序列左端点。

通过该式，如果子序列存在 $presum_j=b$ 且 $j<i$，则说明改子序列 XOR 值可以为 $k^2$. 该思路，需要遍历右端点 ，规模 $n$，需要遍历 $k$，规模 $\sqrt n$，时间复杂度为 $O(n\sqrt n)$ 满足题目要求。

下面剩下的问题就是：

#### 是否存在子序列左端点满足条件？

我们在遍历右端点时，可以顺便存储下来前面出现过的所有左端点 XOR 值的数量，通过数组 $cntprexor_i$ 来储存，数组第 $i$ 位代表 $i$ 出现的次数。

查询时，我们只需要查询 $cntprexor_b$，它便是该右端点满足条件的左端点的数目，也就是子序列的数目。

#### 这个数组长度得开多大？

如果使用 map 映射当然可以规避这个问题，但 map 有 $O(\log n)$ 的时间复杂度，总复杂度 $O(n\sqrt n\log n)$ 会导致超时。只能用数组这种 $O(1)$ 的方法。

题目限制了 $1 \leq a_i \leq n$，那么所有子序列的 XOR 值 $<2n$，我们这个数组只需要开 $2n$ 的长度就行了。简单推导：

对于一个数 $n$，将其写为二进制，XOR 操作最多能将其二进制位的每一位都变成 $1$，不可能使其二进制进位。而 $2n$ 相较于 $n$，二进制进位了，所以可以得出，XOR 值一定 $<2n$.

### 代码

```cpp
#include <bits/stdc++.h>
#define int long long
#define endl '\n'

using namespace std;

const int MAXN = 2e5 + 10;
int n;
int a[MAXN], cnt_prexor[MAXN * 2];

void solve()
{
    memset(cnt_prexor, 0, sizeof(cnt_prexor));
    cnt_prexor[0] = 1;
    cin >> n;
    for (int i = 0; i < n; i++)
        cin >> a[i];
    int cnt_odd = 0, cur_xor = 0;
    for (int i = 0; i < n; i++)
    {
        cur_xor ^= a[i];
        for (int j = 0; j * j < 2 * n; j++)
        {
            int l_prexor = cur_xor ^ (j * j);
            if (l_prexor < 2 * n)
                cnt_odd += cnt_prexor[l_prexor];
        }
        cnt_prexor[cur_xor]++;
    }
    int ans = (n + 1) * n / 2 - cnt_odd;
    cout << ans << endl;
}

signed main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    int t;
    cin >> t;
    while (t--)
        solve();
    return 0;
}
```

