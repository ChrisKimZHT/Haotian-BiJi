**AtCoder Beginner Contest 241（Sponsored by Panasonic）**

E - Putting Candies

https://atcoder.jp/contests/abc241/tasks/abc241_e

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $500$ points

### Problem Statement

You are given a sequence $A=(A_0,A_1,\ldots,A_{N-1})$of length $N$.
There is an initially empty dish. Takahashi is going to repeat the following operation $K$ times.

- Let $X$ be the number of candies on the dish. He puts $A_{(X\bmod N)}$ more candies on the dish. Here, $X\bmod N$ denotes the remainder when $X$ is divided by $N$.

Find how many candies are on the dish after the $K$ operations.

### Constraints

- $2 \leq N \leq 2\times 10^5$
- $1 \leq K \leq 10^{12}$
- $1 \leq A_i\leq 10^6$
- All values in input are integers.

------

### Input

Input is given from Standard Input in the following format:

> $N$ $K$
> $A_0$ $A_1$ $\ldots$ $A_{N-1}$

### Output

Print the answer.

------

### Sample Input 1

> 5 3
> 2 1 6 3 1

### Sample Output 1

> 11

The number of candies on the dish transitions as follows.

- In the $1$-st operation, we have $X=0$, so $A_{(0\mod 5)}=A_0=2$ more candies will be put on the dish.
- In the $2$-nd operation, we have $X=2$, so $A_{(2\mod 5)}=A_2=6$ more candies will be put on the dish.
- In the $3$-rd operation, we have $X=8$, so $A_{(8\mod 5)}=A_3=3 $more candies will be put on the dish.

Thus, after the $3$ operations, there will be $11$ candies on the dish. Note that you must **not** print the remainder divided by $N$.

------

### Sample Input 2

> 10 1000000000000
> 260522 914575 436426 979445 648772 690081 933447 190629 703497 47202

### Sample Output 2

> 826617499998784056

The answer may not fit into a $32$-bit integer type.

### 我的笔记

**发现循环**

$X\bmod N$ 的结果只可能在 $0\sim N-1$ 中，因此至少 $N$ 次运算内会出现两次一样的增量，出现一样的增量就代表着之后会开始循环。

以 Sample Input 1 为例，每次的增量为 $2、6、3、1、6、3、1\dots$ 可见出现 $6、3、1$ 的循环。

所以我们可以计算出循环，再算出循环的次数，直接将一次循环的增量乘循环次数即可，省去大量时间。

**计算循环**

当两次 $X\bmod N$ 结果相同的时候，就说明出现了第一次循环。因此用一个数组 pre[ ]，先初始化为 $-1$，然后用来储存上一次出现 $X\bmod N$ 的步数。如果某一步开始发现 pre[$X\bmod N$] 不为 $-1$，就说明开始循环了，那么从 pre[$X\bmod N$] 到该步的上一步就是一次循环的结果。

**计算结果**

rep_beg (begin)代表循环开始处，rep_end 代表循环结束处，rep_sum 代表一次循环的增量，rep_feq (frequence)代表一次循环的周期，rep_tim (time)代表循环次数，rep_rmn (remain)代表剩余的部分。

第一次 `pre[candy[i + 1] % N] != -1` 时：
rep_beg = pre[candy[i + 1] % N]
rep_end = i + 1

因此：
rep_frq = rep_end - rep_beg
rep_tim = (K - rep_beg) / rep_frq;
rep_rmn = (K - rep_beg) % rep_frq;

最后结果 ans = candy[rep_beg] + rep_sum * rep_tim + (candy[rep_beg + rep_rmn] - candy[rep_beg])
即从 0 ~ rep_beg 不属于循环的部分 + 循环一次的增量 * 循环次数 + 最后凑不到一次循环的剩余量，然后化简即可得到代码中的写法。

### 代码

```cpp
#include <bits/stdc++.h>
#define SIZE 200010

using namespace std;

long long A[SIZE], candy[SIZE], pre[SIZE];

int main(void)
{
    long long N, K;
    cin >> N >> K;
    for (int i = 0; i < N; i++)
        cin >> A[i];
    for (int i = 0; i < N; i++)
        pre[i] = -1;
    candy[0] = 0;
    pre[0] = 0;
    long long rep_beg = 0, rep_end = 0;
    for (long long i = 0; i < N; i++)
    {
        candy[i + 1] = candy[i] + A[candy[i] % N];
        if (pre[candy[i + 1] % N] != -1)
        {
            rep_beg = pre[candy[i + 1] % N];
            rep_end = i + 1;
            break;
        }
        pre[candy[i + 1] % N] = i + 1;
    }
    if (K <= rep_beg)
    {
        cout << candy[K] << endl;
    }
    else
    {
        long long rep_sum = candy[rep_end] - candy[rep_beg];          // 循环部分的和
        long long rep_frq = rep_end - rep_beg;                        // 循环部分的周期
        long long rep_tim = (K - rep_beg) / rep_frq;                  // 循环次数
        long long rep_rmn = (K - rep_beg) % rep_frq;                  // 最后剩余的部分
        long long ans = rep_sum * rep_tim + candy[rep_beg + rep_rmn]; // 结果
        cout << ans << endl;
    }
    return 0;
}
```

