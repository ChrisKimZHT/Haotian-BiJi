**Codeforces Round #828 (Div. 3)**

E1. Divisible Numbers (easy version)

https://codeforces.com/contest/1744/problem/E1

E2. Divisible Numbers (hard version)

https://codeforces.com/contest/1744/problem/E2

<!--more-->

4 seconds / 256 megabytes

standard input / standard output

### Problem

You are given $4$ positive integers $a$, $b$, $c$, $d$ with $a < c$ and $b < d$. Find any pair of numbers $x$ and $y$ that satisfies the following conditions:

- $a < x \leq c$, $b < y \leq d$,
- $x \cdot y$ is divisible by $a \cdot b$.

Note that required $x$ and $y$ may not exist.

### Input

The first line of the input contains a single integer $t$ ($1 \leq t \leq 10$), the number of test cases.

The descriptions of the test cases follow.

**E1: easy version**

The only line of each test case contains four integers $a$, $b$, $c$ and $d$ ($1 \leq a < c \leq 10^5$, $1 \leq b < d \leq 10^5$).

**E2: hard version**

The only line of each test case contains four integers $a$, $b$, $c$ and $d$ ($1 \leq a < c \leq 10^9$, $1 \leq b < d \leq 10^9$).

### Output

For each test case print a pair of numbers $a < x \leq c$ and $b < y \leq d$ such that $x \cdot y$ is divisible by $a \cdot b$. If there are multiple answers, print any of them. If there is no such pair of numbers, then print -1 -1.

### Example

**E1: easy version**

input

> 5
> 1 1 2 2
> 3 4 5 7
> 8 9 15 18
> 12 21 14 24
> 36 60 48 66

output

> 2 2
> 4 6
> 12 12
> -1 -1
> -1 -1

**E2: hard version**

input

> 10
> 1 1 2 2
> 3 4 5 7
> 8 9 15 18
> 12 21 14 24
> 36 60 48 66
> 1024 729 373248 730
> 1024 729 373247 730
> 5040 40320 40319 1000000000
> 999999999 999999999 1000000000 1000000000
> 268435456 268435456 1000000000 1000000000

output

> 2 2
> 4 6
> 12 12
> -1 -1
> -1 -1
> 373248 730
> -1 -1
> 15120 53760
> -1 -1
> 536870912 536870912

### E1 题解

简单版的数据规模为 $10^5$，可以考虑 $O(n)$ 级别的做法

#### 算法思路

对于一组 $a$, $b$, $c$, $d$，我们可以在区间 $(a,c]$ 遍历取 $x$，然后再找到满足 $a \cdot b \mid x \cdot y$ 的最小的 $y$ 值。

之所以要找最小的 $y$，是因为除了上面之外还有一个限制条件 $y\in(b,d]$. 若最小的 $y$ 都 $>d$，则可以断言对于这个 $x$，没有满足条件的 $y$；若最小的 $y$ $\leq b$，我们可以将 $y$ 扩大到 $\lfloor b/y\rfloor+1$ 倍，若扩大后 $y\leq d$ 则可满足；若最小的 $y$ 已经满足条件，则无需其他操作。

如果对于任意 $x$，存在一个 $y$ 满足条件，则输出答案；如果遍历了所有的 $x$ 的情况都没有满足条件的 $y$，则输出 -1 -1.

#### 最小的 $y$ 的求法

由 $a \cdot b \mid x \cdot y$ 得：
$$
x \cdot y = k \cdot a \cdot b
$$
变形得：
$$
y=\frac{k\cdot a\cdot b}{x}
$$
又因为 $y$ 要是整数，因此我们要构造一个 $k$，使 $x\mid k\cdot a\cdot b$，由于我们要求最小的 $y$，即我们要求最小的 $k$：
$$
k=\frac{x}{\gcd(a\cdot b,x)}
$$
综上：
$$
y=\frac{a\cdot b}{\gcd(a\cdot b,x)}
$$

#### 代码

- 时间复杂度：$O(n)$
- 空间复杂度：$O(1)$

```cpp
void solve()
{
    ll a, b, c, d;
    cin >> a >> b >> c >> d;
    for (ll x = a + 1; x <= c; x++)
    {
        ll y = a * b / __gcd(a * b, x); // 计算最小的y
        y *= b / y + 1; // 将y扩大
        if (y <= d)
        {
            cout << x << ' ' << y << endl;
            return;
        }
    }
    cout << -1 << ' ' << -1 << endl;
    return;
}
```

### E2 题解

困难版的数据规模为 $10^9$，可以考虑 $O(\log n)$ 级别的做法。

#### 算法思路

我们要求满足 $a \cdot b \mid x \cdot y$ 的 $x\cdot y$，那我们可以直接把 $a\cdot b$ 拆成两个数 $x$ 和 $y$，然后扩大 $x$ 和 $y$ 到符合条件的区间即可，扩大方式和上面一样：$x$ 扩大到 $\lfloor a/x\rfloor+1$ 倍，$y$ 扩大到 $\lfloor b/y\rfloor+1$ 倍。由于我们不确定 $x$ 和 $y$ 的大小关系，因此我们正反都要算一遍。

若存在一组满足条件的 $x$, $y$，则输出答案；若遍历所有的情况后都没有满足条件的 $x$, $y$，则输出 -1 -1.

#### 拆分方式

我们将 $a$ 和 $b$ 的分解因数，然后在 $a$ 的因数中取 $i$，$b$ 的因数中取 $j$，然后我们就可以保证 $i\cdot j\mid a\cdot b$，$\frac{a\cdot b}{i\cdot j}\mid a\cdot b$.

则我们可以令：
$$
x=i\cdot j,\ y=\frac{a\cdot b}{i\cdot j}.
$$

#### 代码

- 时间复杂度：$O(\log_2^2n)$
- 空间复杂度：$O(\log_2n)$

```cpp
void solve()
{
    ll a, b, c, d;
    cin >> a >> b >> c >> d;
    vector<ll> fact_a, fact_b;
    for (ll i = 1; i * i <= a; i++)
    {
        if (!(a % i))
        {
            fact_a.push_back(i);
            if (a / i != i)
                fact_a.push_back(a / i);
        }
    }
    for (ll i = 1; i * i <= b; i++)
    {
        if (!(b % i))
        {
            fact_b.push_back(i);
            if (b / i != i)
                fact_b.push_back(b / i);
        }
    }
    for (auto i : fact_a)
    {
        for (auto j : fact_b)
        {
            ll p = i * j, q = a * b / (i * j);
            ll x = (a / p + 1) * p, y = (b / q + 1) * q;
            if (x <= c && y <= d)
            {
                cout << x << ' ' << y << endl;
                return;
            }
            x = (a / q + 1) * q, y = (b / p + 1) * p;
            if (x <= c && y <= d)
            {
                cout << x << ' ' << y << endl;
                return;
            }
        }
    }
    cout << -1 << ' ' << -1 << endl;
    return;
}
```

