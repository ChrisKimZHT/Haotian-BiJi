**AtCoder Beginner Contest 246**

D - 2-variable Function

https://atcoder.jp/contests/abc246/tasks/abc246_d

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $400$ points

### Problem Statement

Given an integer $N$, find the smallest integer $X$ that satisfies all of the conditions below.

- $X$ is greater than or equal to $N$.
- There is a pair of non-negative integers $(a, b)$ such that $X=a^3+a^2b+ab^2+b^3$.

### Constraints

- $N$ is an integer.
- $0 \le N \le 10^{18}$

------

### Input

Input is given from Standard Input in the following format:

> $N$

### Output

Print the answer as an integer.

------

### Sample Input 1

> 9

### Sample Output 1

> 15

For any integer $X$ such that $9 \le X \le 14$, there is no $(a, b)$ that satisfies the condition in the statement.
For $X=15$, $(a,b)=(2,1)$ satisfies the condition.

------

### Sample Input 2

> 0

### Sample Output 2

> 0

$N$ itself may satisfy the condition.

------

### Sample Input 3

> 999999999989449206

### Sample Output 3

> 1000000000000000000

Input and output may not fit into a $32$-bit integer type.

### 我的笔记

**直来直去思路：**

$N_{max}=10^{18}$，$X=a^3+a^2b+ab^2+b^3\leq N_{max}$，因此 $\max(a,b)\leq 10^6$

那么直来直去的想法就是 $a$ 从 $0$ 遍历到 $10^6$，对于每个 $a$ 用二分法找到 $b$，时间复杂度 $O(N\log N)$，可以通过。

```cpp
#include <bits/stdc++.h>

using namespace std;

inline long long f(long long a, long long b)
{
    return a * a * a + a * a * b + a * b * b + b * b * b;
}

int main(void)
{
    long long N;
    cin >> N;
    long long ans = INT64_MAX;
    long long delta = INT64_MAX;
    for (long long i = 0; i <= 1e6; i++)
    {
        long long l = i, r = 1e6;
        while (l < r)
        {
            long long mid = (l + r) >> 1;
            if (f(i, mid) >= N)
                r = mid;
            else
                l = mid + 1;
        }
        if (f(i, l) - N < delta)
        {
            delta = f(i, l) - N;
            ans = f(i, l);
        }
    }
    cout << ans << endl;
    return 0;
}
```

**也挺简单的另一种思路（官解思路）：**

$a$ 从 $0$ 遍历到 $10^6$，对于每个 $a$，$b$ 从上一次的大小向 $0$ 遍历，直到 $X<N$. 和上面对比就是找 $b$ 更快了，不需要每一次都二分一遍。提交上去的运行时间也明显降低。

```cpp
// https://atcoder.jp/contests/abc246/editorial/3711
#include <bits/stdc++.h>

using namespace std;

long long f(long long a, long long b)
{
    return (a * a * a + a * a * b + a * b * b + b * b * b);
}

int main()
{
    long long n;
    cin >> n;
    long long x = 8e18;
    long long j = 1000000;
    for (long long i = 0; i <= 1000000; i++)
    {
        while (f(i, j) >= n && j >= 0)
        {
            x = min(x, f(i, j));
            j--;
        }
    }
    cout << x << '\n';
    return 0;
}
```