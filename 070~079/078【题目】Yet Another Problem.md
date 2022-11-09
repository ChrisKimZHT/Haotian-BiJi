**Codeforces Round #832 (Div. 2)**

D. Yet Another Problem

https://codeforces.com/contest/1747/problem/D

<!--more-->

1 seconds / 256 megabytes

standard input / standard output

### Problem

You are given an array $a$ of $n$ integers $a_1, a_2, a_3, \ldots, a_n$.

You have to answer $q$ independent queries, each consisting of two integers $l$ and $r$.

- Consider the subarray $a[l:r]=[a_l, a_{l+1}, \ldots, a_r]$. You can apply the following operation to the subarray any number of times (possibly zero)-
  1. Choose two integers $L$, $R$ such that $l \le L \le R \le r$ and $R - L + 1$ is **odd**.
  2. Replace each element in the subarray from $L$ to $R$ with the $\mathtt{XOR}$ of the elements in the subarray $[L, R]$.
- The answer to the query is the minimum number of operations required to make all elements of the subarray $a[l:r]$ equal to $0$ or $-1$ if it is impossible to make all of them equal to $0$.

You can find more details about $\mathtt{XOR}$ operation [here](https://en.wikipedia.org/wiki/Bitwise_operation#XOR).

### Input

The first line contains two integers $n$ and $q$ $(1 \le n, q \le 2 \cdot 10^5)$ — the length of the array $a$ and the number of queries.

The next line contains $n$ integers $a_1, a_2, \ldots, a_n$ $(0 \le a_i \lt 2^{30})$ — the elements of the array $a$.

The $i$-th of the next $q$ lines contains two integers $l_i$ and $r_i$ $(1 \le l_i \le r_i \le n)$ — the description of the $i$-th query.

### Output

For each query, output a single integer  — the answer to that query.

### Example

input

> 7 6
> 3 0 3 3 1 2 3
> 3 4
> 4 6
> 3 7
> 5 6
> 1 6
> 2 2

output

> -1
> 1
> 1
> -1
> 2
> 0

### Note

In the **first query**, $l = 3, r = 4$, subarray = $[3, 3]$. We can apply operation only to the subarrays of length $1$, which won't change the array; hence it is impossible to make all elements equal to $0$.

In the **second query**, $l = 4, r = 6$, subarray = $[3, 1, 2]$. We can choose the whole subarray $(L = 4, R = 6)$ and replace all elements by their XOR $(3 \oplus 1 \oplus 2)$ = 0, making the subarray $[0, 0, 0]$.

In the **fifth query**, $l = 1, r = 6$, subarray = $[3, 0, 3, 3, 1, 2]$. We can make the operations as follows:

1. Choose $L = 4, R = 6$, making the subarray $[3, 0, 3, 0, 0, 0]$.
2. Choose $L = 1, R = 5$, making the subarray $[0, 0, 0, 0, 0, 0]$.

### 题解

#### 操作前后区间的异或值不变

该题的突破口就在“操作区间长度为奇数”这个条件上，通过分析可以得到上述结论。具体推导如下：

设长度为**奇数** $n$ 的区间 $[a_1,a_2,\dots,a_n]$，对该区间进行一次操作即可得到：
$$
\overbrace{[a_1\oplus a_2\oplus\dots\oplus a_n,\dots]}^{n个}
$$

对于任意数 $x$，$x\oplus x=0$，$0\oplus x=x$，那么我们可以得到：
$$
\overbrace{x\oplus\dots\oplus x}^{n个}=
\begin{cases}
x,n为奇数\\
0,n为偶数
\end{cases}
$$


那么原区间的异或值为 $a_1\oplus a_2\oplus\dots\oplus a_n$，操作后区间的异或值为：
$$
\overbrace{(a_1\oplus a_2\oplus\dots\oplus a_n)\oplus\dots\oplus(a_1\oplus a_2\oplus\dots\oplus a_n)}^{n个}=a_1\oplus a_2\oplus\dots\oplus a_n
$$

即原区间异或值与操作后异或值相同

#### 若区间异或值不为 $0$，则无解

对于一个区间，每一次操作都不会改变区间异或值。若区间能够变为全 $0$，则最终的区间异或值为 $0$，可知原区间异或值为 $0$，即 “若有解，则区间异或值为 $0$”。

注意上述结论，异或值为 $0$ 是有解的**必要条件**，不是充分条件。我们可以化为逆否命题，即上述结论。

#### 进行分类讨论

情况数目很多，需要仔细分类。

- **区间异或值非零：**无解，输出 $-1$
- **区间异或值为零**
  - **若区间全零：**无需操作，即操作次数为 $0$
  - **若区间含非零元素**
    - **区间长度为奇数：**对整个区间进行操作即可，操作次数为 $1$
    - **区间长度为偶数**
      - **区间首（或尾）为零：**对除了尾（或头）的字区间进行操作即可，操作次数为 $1$
      - **区间首尾非零**
        - **区间可分为两个异或值为零的长度为奇数的子区间：**对两个子区间分别进行一次操作即可，操作次数为 $2$
        - **不能分为上述两个子区间：**无解，输出 $-1$

#### 求解区间异或值

可以借鉴前缀和思想，求出区间的前缀异或 $prexor_i$，则区间 $[a_l,\dots,a_r]$ 的异或值为 $prexor_r\oplus prexor_{l-1}$

- 初始化时间复杂度：$O(n)$
- 查询时间复杂度：$O(1)$

#### 判断区间是否全零

可以记录一个数组 $lastpositive$，储存该下标左侧的第一个非零数下标。若 $r$ 下标左侧的第一个非零数下标小于 $l$，即 $lastpositive_r<l$ ，则可判断区间全零。

- 初始化时间复杂度：$O(n)$
- 查询时间复杂度：$O(1)$

#### 判断偶数长度区间是否可分为两个异或值为零的长度为奇数的子区间

可以记录以该下标**为右界**的**长度为奇数**的**异或值为零**的区间的**最大**左界。若 $r$ 下标为右界，有一个符合前面条件的区间 $[a_x,\dots,a_r]$，且 $x\geq l$，那么区间即可分为两段 $[a_l,\dots,a_{x-1}],[a_x,\dots,a_r]$，两段的长度均为奇数，且两段异或值均为零，因此对两段分别进行操作即可将整个区间变为零。

若区间 $[a_x,\dots,a_r]$ 的异或值为零，即 $prexor_r=prexor_{x-1}$，我们可以通过 std::map 来储存该下标左侧的各种 $prexor$ 值的下标，即可快速判断是否存在解。

注意我们要保证切分出来的两个区间均为奇数区间，因此若右界为偶数下标，则左界为奇数下标，反之亦然。

- 初始化时间复杂度：$O(n)$
- 查询时间复杂度：$O(\log{n})$

#### 关于 I/O 方式

该题输入输出量较大，需要取消同步流，其次需要注意将 `endl` 替换为 `\n` 取消刷新缓存区，否则很有可能卡线过。

正常运行时长在 300ms 左右。

### 代码

```cpp
#include <bits/stdc++.h>
 
using namespace std;
 
const int MAXN = 2e5 + 10;
int n, q;
int a[MAXN], prexor[MAXN], last_positive[MAXN], last_same_prexor[MAXN];
map<int, int> odd, even;
 
int main()
{
    ios_base::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    cin >> n >> q;
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
        prexor[i] = prexor[i - 1] ^ a[i];
        last_positive[i] = a[i] ? i : last_positive[i - 1];
        if (i % 2)
        {
            if (even.count(prexor[i]))
                last_same_prexor[i] = even[prexor[i]];
            odd[prexor[i]] = i;
        }
        else
        {
            if (odd.count(prexor[i]))
                last_same_prexor[i] = odd[prexor[i]];
            even[prexor[i]] = i;
        }
    }
    while (q--)
    {
        int l, r;
        cin >> l >> r;
        if (prexor[l - 1] ^ prexor[r])
            cout << -1 << '\n';
        else if (last_positive[r] < l)
            cout << 0 << '\n';
        else if ((r - l + 1) % 2)
            cout << 1 << '\n';
        else if (!a[l] || !a[r])
            cout << 1 << '\n';
        else if (last_same_prexor[r] >= l)
            cout << 2 << '\n';
        else
            cout << -1 << '\n';
    }
    return 0;
}
```

