**Educational Codeforces Round 138 (Rated for Div. 2)**

D. Counting Arrays

https://codeforces.com/contest/1749/problem/D

<!--more-->

2 seconds / 512 megabytes

standard input / standard output

### Problem

Consider an array $a$ of length $n$ with elements numbered from $1$ to $n$. It is possible to remove the $i$-th element of $a$ if $gcd(a_i, i) = 1$, where gcd denotes the greatest common divisor. After an element is removed, the elements to the right are shifted to the left by one position.

An array $b$ with $n$ integers such that $1 \le b_i \le n - i + 1$ is a **removal sequence for the array $a$** if it is possible to remove all elements of $a$, if you remove the $b_1$-th element, then the $b_2$-th, ..., then the $b_n$-th element. For example, let $a = [42, 314]$:

- $[1, 1]$ is a removal sequence: when you remove the $1$-st element of the array, the condition $gcd(42, 1) = 1$ holds, and the array becomes $[314]$; when you remove the $1$-st element again, the condition $gcd(314, 1) = 1$ holds, and the array becomes empty.
- $[2, 1]$ is not a removal sequence: when you try to remove the $2$-nd element, the condition $gcd(314, 2) = 1$ is false.

An array is **ambiguous** if it has **at least two** removal sequences. For example, the array $[1, 2, 5]$ is ambiguous: it has removal sequences $[3, 1, 1]$ and $[1, 2, 1]$. The array $[42, 314]$ is not ambiguous: the only removal sequence it has is $[1, 1]$.

You are given two integers n and m. You have to calculate the number of **ambiguous** arrays $a$ such that the length of $a$ is from $1$ to $n$ and each $a_i$ is an integer from $1$ to $m$.

### Input

The only line of the input contains two integers $n$ and $m$ ($2 \le n \le 3 \cdot 10^5$; $1 \le m \le 10^{12}$).

### Output

Print one integer — the number of ambiguous arrays $a$ such that the length of $a$ is from $1$ to $n$ and each $a_i$ is an integer from $1$ to $m$. Since the answer can be very large, print it modulo $998244353$.

### Examples

| Input             | Output    |
| ----------------- | --------- |
| 2 3               | 6         |
| 4 2               | 26        |
| 4 6               | 1494      |
| 1337 424242424242 | 119112628 |

### 题解

为下文表述简单，我们称 ambiguous arrays 为多义数列，非多义数列称为单义数列，removal sequences 为消去序列。

**多义数列 $=$ 总数列 $-$ 单义数列：**

一个数列，要么是单义，要么是多义，因此可得上述等式。

单义数列的限制条件明显比多义数列严格，因此其数目更容易求得，所以我们反向求解。

其中总数列的数目更简单，即为：$m^n$

**单义数列的消去序列全为 $1$：**

对于任意正整数 $x$，$\gcd(1,x)=1$. 由此可知，任意数列一定有一个全为 $1$ 的消去序列。

单义数列只有一个消去序列，因此其一定全为 $1$.

**单义数列的限制条件：**

单义数列不可有其他的消去序列，因此在整个消去过程当中，所有数都要满足 $\gcd(a_i,i)\neq1$.

单义数列的唯一消去序列全为 $1$，那么其消去过程就是：每次消去第一位，右侧剩余位左移一位。由此可知，处于第 $i$ 位的元素，在消去过程中，会路过第 $i-1,i-2,\dots,2$ 位，最后到达第 $1$ 位。

结合上述两个条件，得到：数列中第 $i$ 个数 $a_i$ 要满足 $\gcd(a_i,j)\neq1$ ($j=2,\dots,i$).

这个条件再进一步简化可以得到：数列中第 $i$ 个数 $a_i$ 一定是 $1\sim i$ 中的所有质数的积的倍数。

**单义数列的数目：**

不妨令单义数列长度为 $l$，$1\sim i$ 中的所有质数的积为 $pm_i$，令 $pm_1=1$。

第一位可选的数为 $pm_1=1$ 的倍数：共 $\lfloor m/1\rfloor$ 个.

第二位可选的数为 $pm_2=2$ 的倍数：共 $\lfloor m/2\rfloor$ 个.

第三位可选的数为 $pm_3=6$ 的倍数：共 $\lfloor m/6\rfloor$ 个.

$\vdots$

综上，长度为 $l$ 的答案即为：
$$
\prod_{i=1}^{l}{\lfloor\frac{m}{pm_i}\rfloor}
$$
长度 $1\sim l$ 总答案数为：
$$
\sum_{l=1}^{n}{\prod_{i=1}^{l}{\lfloor\frac{m}{pm_i}\rfloor}}
$$
若像上面这么算，时间复杂度 $O(n^2)$，肯定超时，但长度 $l$ 和 $l-1$ 之间可以明显找到递推方式，将时间复杂度简化为 $O(n)$. 在此不多赘述。

### 代码

- 代码中使用线性筛法生成质数表，时间复杂度 $O(n)$.
- 请注意代码中每一个取模的地点，漏掉的话，很有可能出现溢出问题（尤其是 cout 输出时取模）

```cpp
#include <bits/stdc++.h>

using namespace std;

typedef long long ll;
const ll MOD = 998244353, MAXN = 3e5 + 10;
bool not_prime[MAXN];
int prime[MAXN], idx;
void init_prime()
{
    not_prime[0] = not_prime[1] = true;
    for (int i = 2; i < MAXN; i++)
    {
        if (!not_prime[i])
            prime[idx++] = i;
        for (int j = 0; j < idx && prime[j] * i < MAXN; j++)
        {
            not_prime[prime[j] * i] = true;
            if (!(i % prime[j]))
                break;
        }
    }
}

int main()
{
    init_prime();
    ll n, m;
    cin >> n >> m;
    ll ans = 0, cnt = 1, prime_mul = 1;
    for (ll i = 1; i <= n; i++)
    {
        if (!not_prime[i])
            prime_mul *= i;
        if (prime_mul > m)
            break;
        cnt = cnt * (m / prime_mul % MOD) % MOD;
        ans = (ans + cnt) % MOD;
    }
    ll all = 0, pow_m = 1;
    for (ll i = 1; i <= n; i++)
    {
        pow_m = pow_m * (m % MOD) % MOD;
        all = (all + pow_m) % MOD;
    }
    cout << (all - ans + MOD) % MOD << endl;
    return 0;
}
```

