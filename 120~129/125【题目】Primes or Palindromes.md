**Codeforces Round 315 (Div. 1)**

A. Primes or Palindromes?

https://codeforces.com/contest/568/problem/A

<!--more-->

3 seconds / 256 megabytes

standard input / standard output

## Problem

Rikhail Mubinchik believes that the current definition of prime numbers is obsolete as they are too complex and unpredictable. A palindromic number is another matter. It is aesthetically pleasing, and it has a number of remarkable properties. Help Rikhail to convince the scientific community in this!

Let us remind you that a number is called prime if it is integer larger than one, and is not divisible by any positive integer other than itself and one.

Rikhail calls a number a palindromic if it is integer, positive, and its decimal representation without leading zeros is a palindrome, i.e. reads the same from left to right and right to left.

One problem with prime numbers is that there are too many of them. Let's introduce the following notation: π(n) — the number of primes no larger than n, rub(n) — the number of palindromic numbers no larger than n. Rikhail wants to prove that there are a lot more primes than palindromic ones.

He asked you to solve the following problem: for a given value of the coefficient A find the maximum n, such that π(n) ≤ A·rub(n).

## Input

The input consists of two positive integers p, q, the numerator and denominator of the fraction that is the value of A (, ).

## Output

If such maximum number exists, then print it. Otherwise, print "Palindromic tree is better than splay tree" (without the quotes).

## Examples

Input

> 1 1

Output

> 40

Input

> 1 42

Output

> 1

Input

> 6 4

Output

> 172

## 题解

本题很重要的一点是：质数的增长速度远快于回文数的增长速度。

下图绘制了 $1\sim 10000$ 的质数和回文数的数量的增长速度情况，通过观察可见质数的数量增长得更快。

<img src="https://assets.zouht.com/img/note/125-01.webp" style="zoom:67%;" />

如果要更准确得描述的话，质数数目的增长情况可以近似为：
$$
\pi(x)\sim \frac{x}{\log x}
$$
根据该近似函数，可以知道随着数字的增大，素数的密度逐渐降低。这个其实就是数论中的[素数定理](https://en.wikipedia.org/wiki/Prime_number_theorem).

而回文数的增长速度削减得非常快，在 $9\to 10,\;999\to1000,\;99999\to100000\dots$ 的位置会有一个斜率缩小约 $10$ 倍的拐点，从上图中也能发现 $10$ 和 $1000$ 处的拐点。

综上，回文数的密度降低速度是指数级别的，而质数的密度降低速度是对数级别的。而本题给出的 $A$ 相当于一个放缩参数，范围是缩小 $42$ 倍到放大 $42$ 倍，这个范围非常小。即使把回文数放大最大 $42$ 倍，它也会很快被质数超过，因此解的上限其实很低。

实际测试中，解不会大于 $2\cdot10^6$，因此直接预处理 $1\sim2\cdot10^6$ 的质数和回文数即可。质数的初始化可用[筛法](https://io.zouht.com/5.html)完成，复杂度 $O(n)$，回文数的初始化直接最朴素的方法即可，复杂度 $O(n\lg n)$.

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
// #define int long long

using namespace std;

constexpr int MAXN = 2e6 + 10;
bool not_prime[MAXN];
int prime[MAXN], prime_cnt[MAXN], prime_size;

void init_prime()
{
    not_prime[0] = not_prime[1] = true;
    for (int i = 2; i < MAXN; i++)
    {
        prime_cnt[i] = prime_cnt[i - 1];
        if (!not_prime[i])
        {
            prime[prime_size++] = i;
            prime_cnt[i]++;
        }
        for (int j = 0; j < prime_size && i * prime[j] < MAXN; j++)
        {
            not_prime[i * prime[j]] = true;
            if (i % prime[j] == 0)
                break;
        }
    }
}

bool check_palindromes(int x)
{
    vector<int> digit;
    while (x)
    {
        digit.push_back(x % 10);
        x /= 10;
    }
    for (int i = 0, j = digit.size() - 1; i <= j; i++, j--)
        if (digit[i] != digit[j])
            return false;
    return true;
}

int palindromes_cnt[MAXN];
void init_palindromes()
{
    for (int i = 1; i < MAXN; i++)
        palindromes_cnt[i] = palindromes_cnt[i - 1] + check_palindromes(i);
}

void solve()
{
    init_prime();
    init_palindromes();
    int p, q;
    cin >> p >> q;
    for (int i = MAXN - 1; i >= 0; i--)
    {
        if (prime_cnt[i] * q <= palindromes_cnt[i] * p)
        {
            cout << i << endl;
            return;
        }
    }
    cout << "Palindromic tree is better than splay tree" << endl;
}

signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    int t = 1;
    // cin >> t;
    while (t--)
        solve();
    return 0;
}
```

