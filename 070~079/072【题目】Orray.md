**Codeforces Round #827 (Div. 4)**

G. Orray

https://codeforces.com/contest/1742/problem/G

<!--more-->

2 seconds / 256 megabytes

standard input / standard output

### Problem

You are given an array $a$ consisting of $n$ nonnegative integers.

Let's define the prefix OR array $b$ as the array $b_i = a_1~\mathsf{OR}~a_2~\mathsf{OR}~\dots~\mathsf{OR}~a_i$, where $\mathsf{OR}$ represents the [bitwise OR operation](https://en.wikipedia.org/wiki/Bitwise_operation#OR). In other words, the array $b$ is formed by computing the $\mathsf{OR}$ of every prefix of $a$.

You are asked to rearrange the elements of the array $a$ in such a way that its prefix OR array is lexicographically maximum.

An array $x$ is lexicographically greater than an array $y$ if in the first position where $x$ and $y$ differ, $x_i > y_i$.

### Input

The first line of the input contains a single integer $1 \le t \le 100$ — the number of test cases. The description of test cases follows.

The first line of each test case contains a single integer $n$ ($1 \leq n \leq 2 \cdot 10^5$) — the length of the array $a$.

The second line of each test case contains $n$ **nonnegative** integers $a_1, \ldots, a_n$ ($0 \leq a_i \leq 10^9$).

It is guaranteed that the sum of $n$ over all test cases does not exceed $2 \cdot 10^5$.

### Output

For each test case print $n$ integers — any rearrangement of the array $a$ that obtains the lexicographically maximum prefix OR array.

### Example

input:

> 5
> 4
> 1 2 4 8
> 7
> 5 1 2 3 4 5 5
> 2
> 1 101
> 6
> 2 3 4 2 3 4
> 8
> 1 4 2 3 4 5 7 1

output:

> 8 4 2 1 
> 5 2 1 3 4 5 5 
> 101 1 
> 4 3 2 2 3 4 
> 7 1 4 2 3 4 5 1 

### 题解

先考虑暴力做法，每一次取数遍历整个数列，找到与前缀或的上一位做 $\mathsf{OR}$ 操作的结果最大的数，从原序列中删除并加入答案序列（代码实现为直接输出该数并打上标记），代码如下：

```cpp
int pre = 0, tmp = 0;
for (int i = 0; i < n; i++)
{
    int idx = -1;
    for (int j = 0; j < n; j++)
    {
        if (!vis[j] && tmp < (pre | a[j]))
        {
            tmp = (pre | a[j]);
            idx = j;
        }
    }
    pre = tmp;
    cout << a[idx] << ' ';
    vis[idx] = true;
}
```

用这个代码测样例就会发现问题：找到并输出了几个数之后，得到了前几个数的前缀或，再和数列中剩下的数取或得到的结果都是一样的，即 $idx$ 一直为初值 $-1$，作为数组下标然后产生异常。

为了解决这个问题，我们当然可以把第 $7$ 行的 $<$ 改成 $\leq$，然后喜提一个 $O(n^2)$ 的 $\text{TLE}$ 代码。但同时这也是个突破口，提示着正确的解题方式。

注意整数 $a_i$ 最大为 $10^9$，那么 $a_i$ 的二进制表示最多有 $\lfloor\log_2{10^9}\rfloor+1=30$ 位，那么意味着，我们最多找到 $30$ 个顺序对结果有影响的数，其他的数的顺序不影响结果。因为最多通过 $30$ 次或运算，我们就能把前缀或中能变成 $1$ 的位都变成 $1$，后面的数已经没有更多的 $1$ 位了。

那我们在一次迭代中，如果发现 $idx$ 没有更新，就可以停止迭代，然后按随意顺序输出剩下的数，即可得到答案了。这种方式的复杂度为 $O(n)$，完全可以接受。

### 代码

易错点：$|$ 运算符的优先级比 $<$，$>$ 低，因此`tmp < (pre | a[j])`的括号一定不要忘了，否则会产生错误结果。

```cpp
#include <bits/stdc++.h>

using namespace std;

void solve()
{
    int n;
    cin >> n;
    vector<int> a(n);
    vector<bool> vis(n);
    for (int i = 0; i < n; i++)
        cin >> a[i];
    int pre = 0, tmp = 0;
    for (int i = 0; i < n; i++)
    {
        int idx = -1;
        for (int j = 0; j < n; j++)
        {
            if (!vis[j] && tmp < (pre | a[j]))
            {
                tmp = (pre | a[j]);
                idx = j;
            }
        }
        if (idx == -1)
            break;
        pre = tmp;
        cout << a[idx] << ' ';
        vis[idx] = true;
    }
    for (int i = 0; i < n; i++)
        if (!vis[i])
            cout << a[i] << ' ';
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