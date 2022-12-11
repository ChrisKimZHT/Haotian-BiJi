**AtCoder Beginner Contest 281**

E - Least Elements

https://atcoder.jp/contests/abc281/tasks/abc281_e

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $500$ points

### Problem Statement

You are given an integer sequence $A = (A_1, \dots, A_N)$ of length $N$, and integers $M$ and $K$.
For each $i = 1, \dots, N - M + 1$, solve the following independent problem.

> Find the sum of the first $K$ values in the sorted list of the $M$ integers $A_i, A_{i + 1}, \dots, A_{i + M - 1}$ in ascending order.

### Constraints

- $1 \leq K \leq M \leq N \leq 2 \times 10^5$
- $1 \leq A_i \leq 10^9$
- All values in the input are integers.
### Input

The input is given from Standard Input in the following format:

> $N$ $M$ $K$
> $A_1$ $A_2$ $\ldots$ $A_N$

### Output

Let $\mathrm{answer}_k$ be the answer to the problem for $i = k$, and print them in the following format:

> $\mathrm{answer}_1$ $\mathrm{answer}_2$ $\ldots$ $\mathrm{answer}_{N-M+1}$

### Sample Input 1

> 6 4 3
> 3 1 4 1 5 9

### Sample Output 1

> 5 6 10

- For $i = 1$, sorting $A_i, A_{i+1}, A_{i+2}, A_{i+3}$ in ascending order yields $1, 1, 3, 4$, where the sum of the first three values is $5$.
- For $i = 2$, sorting $A_i, A_{i+1}, A_{i+2}, A_{i+3}$ in ascending order yields $1, 1, 4, 5$, where the sum of the first three values is $6$.
- For $i = 3$, sorting $A_i, A_{i+1}, A_{i+2}, A_{i+3}$ in ascending order yields $1, 4, 5, 9$, where the sum of the first three values is $10$.
### Sample Input 2

> 10 6 3
> 12 2 17 11 19 8 4 3 6 20

### Sample Output 2

> 21 14 15 13 13

### 题解

该题算是一种滑动窗口，如果我们在每次滑动窗口时，都进行排序、累加求和，那么肯定是不符合时间限制的。

首先需要意识到，窗口每次滑动时，两次的结果是有某种关联的，我们应该找滑动时的变化规律，而不是每次重新计算。

**维护内容**

我们维护两个多重集合 $l,r$（即元素可重复的集合）和求和结果 $sum$：

- $l$ 的大小为 $K$，储存长度为 $M$ 的滑窗中，最小的 $K$个元素。$l$ 中元素之和 $sum$ 即为我们需要的答案。

- $r$ 的大小为 $M-K$，储存滑窗中剩余的元素。

**初始化**

首先需要初始化 $l,r,sum$ 在滑动窗口起始位置的值，这个暴力求解即可。

**变化规律**

我们的滑动窗口向右移动一位，将会造成一个元素离开和一个元素加入，我们分别记为 $out$ 和 $in$.

对 $out$ 和 $in$ 的大小做分类讨论，可以得到以下四种情况：

- $out>l_{max}$ 且 $in>l_{max}$
- $out<r_{min}$ 且 $in<r_{min}$
- $out<r_{min}$ 且 $in>l_{max}$
- $out>l_{max}$ 且 $in<r_{min}$

情况一：$out>l_{max}$ 且 $in>l_{max}$

离开和加入的元素均比 $l$ 大。需要在 $r$ 中删除 $out$，加入 $in$，$sum$ 不变。

情况二：$out<r_{min}$ 且 $in<r_{min}$

离开和加入的元素均比 $r$ 小。需要在 $l$ 中删除 $out$，加入 $in$，更新 $sum$ 为 $sum+in-out$。

情况三：$out<r_{min}$ 且 $in>l_{max}$

离开的元素比 $r$ 小，加入的元素比 $l$ 大。需要在 $l$ 中删除 $out$，在 $r$ 中加入 $in$，然后将 $r_{min}$ 移动给 $l$，更新 $sum$ 为 $sum+r_{min}-out$

情况四：$out>l_{max}$ 且 $in<r_{min}$

离开的元素比 $l$ 大，加入的元素比 $r$ 小。需要在 $r$ 中删除 $out$，在 $l$ 中加入 $in$，然后将 $l_{max}$ 移动给 $r$，更新 $sum$ 为 $sum+in-l_{max}$

**特殊情况**

我们上面的分类是假定了 $l,r$ 非空的，当不满足这个条件时就会出问题。

例如 $M=K$ 时就会造成 $r$ 为空，此时需要特判当 $r$ 为空时，进行情况二的操作。

### 代码

```cpp
#include <bits/stdc++.h>
#define int long long
#define endl '\n'

using namespace std;

signed main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    int N, M, K;
    cin >> N >> M >> K;
    vector<int> A(N);
    for (int i = 0; i < N; i++)
        cin >> A[i];
    multiset<int> l, r;
    int sum = 0;
	// 初始化l,r,sum的值
    for (int i = 0; i < M; i++)
        l.insert(A[i]);
    while (l.size() > K)
    {
        int mx = *l.rbegin();
        r.insert(mx);
        l.extract(mx);
    }
    for (auto &elem : l)
        sum += elem;
    cout << sum << ' ';
    // 开始递推接下来的值
    for (int i = 1; i + M - 1 < N; i++)
    {
        int out = A[i - 1], in = A[i + M - 1];
        int lmax = *l.rbegin(), rmin = *r.begin();
        if (r.empty())
        {
            l.extract(out);
            l.insert(in);
            sum += in - out;
        }
        else if (out > lmax && in > lmax)
        {
            r.extract(out);
            r.insert(in);
        }
        else if (out < rmin && in < rmin)
        {
            l.extract(out);
            l.insert(in);
            sum += in - out;
        }
        else if (out < rmin && in > lmax)
        {
            r.insert(in);
            int new_rmin = *r.begin();
            r.extract(new_rmin);
            l.insert(new_rmin);
            l.extract(out);
            sum += new_rmin - out;
        }
        else // if (out > lmax && in < rmin)
        {
            l.insert(in);
            int new_lmax = *l.rbegin();
            l.extract(new_lmax);
            r.insert(new_lmax);
            r.extract(out);
            sum += in - new_lmax;
        }
        cout << sum << ' ';
    }
    cout << endl;
    return 0;
}
```

