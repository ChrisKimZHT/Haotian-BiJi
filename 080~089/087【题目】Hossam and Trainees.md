**Codeforces Round #837 (Div. 2)**

C. Hossam and Trainees

https://codeforces.com/contest/1771/problem/B

<!--more-->

3 seconds / 256 megabytes

standard input / standard output

### Problem

Hossam has $n$ trainees. He assigned a number $a_i$ for the $i$-th trainee. 

A pair of the $i$-th and $j$-th ($i \neq j$) trainees is called successful if there is an integer $x$ ($x \geq 2$), such that $x$ divides $a_i$, and $x$ divides $a_j$.

Hossam wants to know if there is a successful pair of trainees.

Hossam is very tired now, so he asks you for your help!

### Input

The input consists of multiple test cases. The first line contains a single integer $t$ ($1 \le t \le 10^5$), the number of test cases. A description of the test cases follows.

The first line of each test case contains an integer number $n$ ($2 \le n \le 10^5$).

The second line of each test case contains $n$ integers, the number of each trainee $a_1, a_2, \dots, a_n$ ($1 \le a_i \le 10^9$).

It is guaranteed that the sum of $n$ over all test cases does not exceed $10^5$.

### Output

Print the answer — "YES" (without quotes) if there is a successful pair of trainees and "NO" otherwise. You can print each letter in any case.

### Example

Input

> 2
> 3
> 32 48 7
> 3
> 14 5 9

Output

> YES
> NO

### Note

In the first example, the first trainee and the second trainee make up a successful pair: 

$a_1 = 32, a_2 = 48$, you can choose $x = 4$.

### 题解

#### 典型错解 - 看起来仿佛是 $O(n)$

拿到题目一眼看到就想到把整个数列求一个和得到 $mul$，再吧整个数列求一个最小公倍数得到 $lcm$，如果 $mul=lcm$ 则输出 NO，否则输出 YES. 这个解法在数学上是没有任何问题的，复杂度也是 $O(n)$，样例也是能通过的。

但是该解发存在的问题就是连续乘法会让数字大小呈指数趋势增长，$mul$ 和 $lcm$ 这两个变量会以非常快的速度溢出然后错误。如果使用高精度算法，该数字也会变得非常巨大，长度可以达到 $9\times10^5$，是不可能在合理的时间计算出结果的。

#### 正确解法 - $O(n\log n)$

将数列中每个数分解质因数，如果有任意两个数含有同一个质因子，则输出 YES，如果所有数都没有公共的质因子，则输出 NO.

- 从头到尾遍历数列，使用集合 $s$ 来储存出现过的质因子：

  - 对于每个数，分解质因数得到：$n=p_1^{a_1}p_2^{a_2}\dots p_n^{a_n}$

  - 考虑其所有的质因子：$p_1,p_2,\dots,p_n$
    - 若质因子 $p_i$ 已经在 $s$ 中：直接输出 YES，结束本次计算
    - 若质因子 $p_i$ 不在 $s$ 中：将 $p_i$ 加入 $s$

- 若遍历完整个数列后还没有找到相同的质因子，则输出 NO，结束计算

对于一个数 $n$ ，其质因子个数一定 $\leq\log_2{n}$，当 $n=10^9$ 时，$\log_2{n}=29.89$，是很小的规模。因此该算法的最差时间复杂度为 $O(n\log{n})$，可以满足时限要求。

### 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'

using namespace std;

const int MAXN = 1e5 + 10;
int a[MAXN], prime[MAXN], primesize = 0;
bool not_prime[MAXN];
void init_prime()
{
    not_prime[0] = not_prime[1] = true;
    for (int i = 2; i < MAXN; i++)
    {
        if (!not_prime[i])
            prime[primesize++] = i;
        for (int j = 0; j < primesize && i * prime[j] < MAXN; j++)
        {
            not_prime[i * prime[j]] = true;
            if (i % prime[j] == 0)
                break;
        }
    }
}

void solve()
{
    int n;
    cin >> n;
    for (int i = 0; i < n; i++)
        cin >> a[i];
    set<int> s;
    for (int i = 0; i < n; i++)
    {
        for (int j = 0; prime[j] * prime[j] <= a[i]; j++)
        {
            if (!(a[i] % prime[j]))
            {
                if (s.find(prime[j]) != s.end())
                {
                    cout << "YES" << endl;
                    return;
                }
                s.insert(prime[j]);
                while (!(a[i] % prime[j]))
                    a[i] /= prime[j];
            }
        }
        if (a[i] > 1)
        {
            if (s.find(a[i]) != s.end())
            {
                cout << "YES" << endl;
                return;
            }
            s.insert(a[i]);
        }
    }
    cout << "NO" << endl;
    return;
}

signed main()
{
    init_prime();
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

