**Denso Create Programming Contest 2022 Winter(AtCoder Beginner Contest 280)**

E - Critical Hit

https://atcoder.jp/contests/abc280/tasks/abc280_e

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $500$ points

### Problem Statement

There is a monster with initial stamina $N$.
Takahashi repeatedly attacks the monster while the monster's stamina remains $1$ or greater.

An attack by Takahashi reduces the monster's stamina by $2$ with probability $\frac{P}{100}$ and by $1$ with probability $1-\frac{P}{100}$.

Find the expected value, modulo $998244353$ (see Notes), of the number of attacks before the monster's stamina becomes $0$ or less.

### Notes

We can prove that the sought expected value is always a finite rational number. Moreover, under the Constraints of this problem, when the value is represented as $\frac{P}{Q}$ by two coprime integers $P$ and $Q$, we can show that there exists a unique integer $R$ such that $R \times Q \equiv P\pmod{998244353}$ and $0 \leq R \lt 998244353$. Print such $R$.

### Constraints

- $1 \leq N \leq 2\times 10^5$
- $0 \leq P \leq 100$
- All values in the input are integers.

------

### Input

The input is given from Standard Input in the following format:

> $N$ $P$

### Output

Find the expected value, modulo $998244353$, of the number of Takahashi's attacks.

------

### Sample Input 1

> 3 10

### Sample Output 1

> 229596204

An attack by Takahashi reduces the monster's stamina by $2$ with probability $\frac{10}{100}=\frac{1}{10}$ and by $1$ with probability $\frac{100-10}{100}=\frac{9}{10}$.

- The monster's initial stamina is $3$.
- After the first attack, the monster's stamina is $2$ with probability $\frac{9}{10}$ and $1$ with probability $\frac{1}{10}$.
- After the second attack, the monster's stamina is $1$ with probability $\frac{81}{100}$, $0$ with probability $\frac{18}{100}$, and $-1$ with probability $\frac{1}{100}$. With probability $\frac{18}{100}+\frac{1}{100}=\frac{19}{100}$, the stamina becomes $0$ or less, and Takahashi stops attacking after two attacks.
- If the stamina remains $1$ after two attacks, the monster's stamina always becomes $0$ or less by the third attack, so he stops attacking after three attacks.

Therefore, the expected value is $2\times \frac{19}{100}+3\times\left(1-\frac{19}{100}\right)=\frac{281}{100}$. Since $229596204 \times 100 \equiv 281\pmod{998244353}$, print $229596204$.

------

### Sample Input 2

> 5 100

### Sample Output 2

> 3

Takahashi's attack always reduces the monster's stamina by $2$. After the second attack, the stamina remains $5-2\times 2=1$, so the third one is required.

------

### Sample Input 3

> 280 59

### Sample Output 3

> 567484387

### 题解

**题面分析**

该题为一个非常基础的 DP + 模逆元题，只不过题面的 Notes 小节有点坑人，需要重点关注下~~（我就因为这被害了）~~

首先 Notes 中的变量 $P$ 与输入数据的变量 $P$ 是两个东西，不要把这俩当成同一个变量了，否则题目就看不明白了。

其次，要我们输出满足 $R \times Q \equiv P\pmod{998244353}$ 的 $R$ 是个啥东西。这个等式其实不要想复杂了，其实就是 $\frac{P}{Q}$，其中将除法运算改成用模逆元乘法运算即可。

分析后，问题就清晰了，题目就是让我们使用模逆元计算这个期望值罢了。

**动态规划**

我们约定 $dp_i:=$ 造成伤害 $i$ 需要的步数的**期望**。

易得 $dp_0=0$，$dp_1=1$（每一步至少造成 $1$ 点伤害，因此只需 $1$ 步）

接下来的递推方式为：
$$
\begin{align}
dp_i&=(dp_{i-2}+1)\cdot\frac{P}{100}+(dp_{i-1}+1)\cdot(1-\frac{P}{100})\\
&=dp_{i-2}\cdot\frac{P}{100}+dp_{i-1}\cdot(1-\frac{P}{100})+1
\end{align}
$$
具体解释，要达成 $i$ 点伤害，有两种情况：

- 前面达成了 $i-2$ 点伤害，最后一步达成了 $2$ 点伤害，期望为 $(dp_{i-2}+1)\cdot\frac{P}{100}$
- 前面达成了 $i-1$ 点伤害，最后一步达成了 $1$ 点伤害，期望为 $(dp_{i-1}+1)\cdot(1-\frac{P}{100})$

**模逆元**

（关于模逆元：https://io.zouht.com/13.html）

原式转化为：
$$
dp_i=dp_{i-2}\cdot P\cdot\mathrm{inv}(100)+dp_{i-1}\cdot(1-P\cdot\mathrm{inv}(100))+1
$$
可见只用的找 $100$ 的模逆元，完全可以直接算出来它的值为 $828542813$.

### 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'

using namespace std;

typedef long long ll;
const ll MOD = 998244353;
/* ll fastpow(ll a, ll b)
{
    a %= MOD;
    ll ans = 1;
    while (b)
    {
        if (b % 2 == 1)
            ans = ans * a % MOD;
        a = a * a % MOD;
        b >>= 1;
    }
    return ans;
}
inline ll inv(ll x)
{
    return fastpow(x, MOD - 2);
} */
const ll MAXN = 2e5 + 10;
ll dp[MAXN];

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    ll N, P;
    cin >> N >> P;
    // ll inv100 = inv(100);
    ll inv100 = 828542813;
    dp[1] = 1;
    for (int i = 2; i <= N; i++)
    {
        dp[i] = P * inv100 % MOD * dp[i - 2] % MOD +
                (1 - (P * inv100 % MOD) + MOD) % MOD * dp[i - 1] % MOD + 1;
        dp[i] %= MOD;
    }
    cout << dp[N] << endl;
    return 0;
}
```

