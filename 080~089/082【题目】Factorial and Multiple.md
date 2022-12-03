**Denso Create Programming Contest 2022 Winter(AtCoder Beginner Contest 280)**

D - Factorial and Multiple

https://atcoder.jp/contests/abc280/tasks/abc280_d

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $400$ points

### Problem Statement

You are given an integer $K$ greater than or equal to $2$.
Find the minimum positive integer $N$ such that $N!$ is a multiple of $K$.

Here, $N!$ denotes the factorial of $N$. Under the Constraints of this problem, we can prove that such an $N$ always exists.

### Constraints

- $2\leq K\leq 10^{12}$
- $K$ is an integer.

------

### Input

The input is given from Standard Input in the following format:

> K

### Output

Print the minimum positive integer $N$ such that $N!$ is a multiple of $K$.

------

### Sample Input 1

> 30

### Sample Output 1

> 5

- $1!=1$
- $2!=2\times 1=2$
- $3!=3\times 2\times 1=6$
- $4!=4\times 3\times 2\times 1=24$
- $5!=5\times 4\times 3\times 2\times 1=120$

Therefore, $5$ is the minimum positive integer $N$ such that $N!$ is a multiple of $30$. Thus, $5$ should be printed.

------

### Sample Input 2

> 123456789011

### Sample Output 2

> 123456789011

------

### Sample Input 3

> 280

### Sample Output 3

> 7

### 题解

（这题比赛的时候用不正常的方法混过了，现在补一遍正解）

该题要分解质因数，这点应该比较好想，不过之后的处理方式才是重点。即：

对于分解好的数 $K=p_0^{a_0}p_1^{a_1}\dots p_n^{a_n}\ (p_0,p_1,\dots,p_n为质数)$，如何求得最小的 $N!$ 满足 $K\mid N!$

我们可以单独考虑每一个质数，对于 $p_i^{a_i}$，取 $N=p_i\cdot a_i$ 一定能满足条件，但是该答案不一定是最小的。具体举例来说：

如 $3^4$，取 $N=3\cdot4=12$，$N!=12\dots\times9\times\dots\times6\times\dots\times3\times\dots$，可以看见，$9$ 里面实际上包含了两个 $3$，因此我们没有必要令 $N=12$，只需要 $N=9$ 即可。

那么如何求到最小的呢？分解质因数后能保证时间复杂度为 $\log$ 级别，因此我们从直接一个个算就行了。使用变量 $cnt$ 计数，计算 $i\cdot p_i\ (i=1,2,\dots)$ 内含有的 $p_i$ 因子个数 $n$，将 $cnt$ 加上 $n$，直到 $cnt\geq a_i$ 时，$i\cdot p_i$ 便是所求的 $N$。具体举例来说：

如 $3^4$，我们需要 $4$ 个 $3$，从 $1\times3$ 开始，$3$ 含有 $1$ 个 $3$，$6$ 含有 $1$ 个 $3$，$9$ 含有 $2$ 个 $3$，此时已经组成了 $4$ 个 $3$，$N=9$

考虑完单个质数之后，整个 $K$ 的答案便是每个质数因子的答案的最大值。

### 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'

using namespace std;

typedef long long ll;
const int MAXN = 1e6 + 10;
int prime[MAXN], primesize = 0;
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

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    init_prime();
    ll K;
    cin >> K;
    ll ans = 0;
    for (int i = 0; i < primesize; i++)
    {
        ll prm = prime[i], cnt = 0;
        if (prm * prm > K)
            break;
        while (!(K % prm))
        {
            K /= prm;
            cnt++;
        }
        ll n = 0;
        while (cnt > 0)
        {
            n += prm;
            int x = n;
            while (!(x % prm))
            {
                x /= prm;
                cnt--;
            }
        }
        ans = max(ans, n);
    }
    cout << max(ans, K) << endl;
    return 0;
}
```

