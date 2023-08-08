**2023牛客暑期多校训练营7**

C - Beautiful Sequence

https://ac.nowcoder.com/acm/contest/57361/C

<!--more-->

## 题意

给定长度为 $n$ 的序列 $\{a\}_{i=1}^n$ 的异或差分序列 $\{b\}_{i=1}^{n-1}$, 要求 $\{a\}$ 不严格递增且数字范围都在 $\left[0,2^{30}-1\right]$ 的第 $k$ 大的序列。 $1 \leq n \leq 10^6, 1 \leq k<2^{30}$ 。

## 题解

**问题转化**

首先，将 $A_{i}\oplus A_{i+1}=B_i,\;1\leq i<n$ 变形可以得到 $A_{i+1}=A_i\oplus B_i$，即得到了一个 $A_i$ 的递推式，那么：

- $A_1=A_1\oplus0$
- $A_2=A_1\oplus B_1$
- $A_3=A_1\oplus B_1\oplus B_2$
- $\vdots$
- $A_n=A_1\oplus B_1\oplus\cdots\oplus B_{n-1}$

由于 $B_i$ 已经给出，那么说明只要确定了 $A_1$，那么 $A_1\sim A_n$ 就全部确定了。那么题目让我们求字典序第 $k$ 小的 $A$ 序列，就相当于求出第 $k$ 小的合法 $A_1$ 了。

为了方便，令 $C$ 为 $B$ 的前缀异或序列，即 $C_i=B_1\oplus B_2\oplus\cdots\oplus B_{i-1}$. 特别的令 $C_1=0$. 这样，$A$ 序列可以写成：

- $A_1=A_1\oplus C_1$
- $A_2=A_1\oplus C_2$
- $\vdots$
- $A_n=A_1\oplus C_n$

根据限制 $A_1\leq A_2\leq\cdots\leq A_n$，那么就可以转换为：
$$
A_1\oplus C_1\leq A_1\oplus C_2\leq\dots\leq A_1\oplus C_n
$$
综上，本题的问题就是要求解出满足上面这个式子的第 $k$ 小的 $A_1$. 为了方便，下文的 $A_1$ 全部用 $x$ 表示。

**问题求解**

首先考虑一个子问题：$x\oplus C_{i}\leq x\oplus C_{i+1}$

如果 $C_i$ 和 $C_{i+1}$ 的二进制最高的不同位是第 $k$ 位，记这一位的值是 $C_{i,k}$

- $C_{i,k}=0,C_{i+1,k}=1$，那么 $x_{k}$ 必须取 $0$. （$0\oplus0\leq0\oplus1$）
- $C_{i,k}=1,C_{i+1,k}=0$，那么 $x_k$ 必须取 $1$.（$1\oplus1\leq1\oplus0$）
- $C_{i,k}=C_{i+1,k}$，那么 $x_k$ 可以任取 $0/1$（相当于待定）

由于考虑的是二进制最高位，那么这一位满足了 $\leq$，那么原问题也是满足的，如果这一位不满足，那么原问题就是不满足的。

接下来考虑整个问题：$x\oplus C_1\leq x\oplus C_2\leq\dots\leq x\oplus C_n$

可以从左到右链式求解，仍然是用上面的求解方式确定 $x$ 的某一位。可以首先将 $x$ 每一位记做 $-1$ 表示待定，然后从左到右根据限制进行填写。如果取 $0$ 就将 $-1$ 改为 $0$，如果取 $1$ 就将 $-1$ 改为 $1$，如果都能取就保留 $-1$ 表示待定。

不过此时就可能出现冲突，比如 $x_k$ 这一位在之前已经填上了 $0$，但是现在又要填 $1$，这就代表了矛盾，即这种情况下 $x$ 无解，直接输出 $-1$ 即可。

如果有解的话，就会解出一个 $x$，不过这个 $x$ 的某些位可能还保留着 $-1$ 表示待定，需要将 $-1$ 填上数值。由于题目要求的是第 $k$ 小的 $x$，那么就将 $k-1$ 转为二进制，对应着填入 $x$ 中的 $-1$ 位里。例如解出来 $x$ 的二进制位情况：$\underline\ ,\cdots,\underline\ ,0,\underline\ ,1,0,\underline\ $，如果 $k=3$，则填好后的 $x$ 为：$\underline0,\cdots,\underline0,0,\underline1,1,0,\underline0$.

如果 $k$ 的大小过大，$x$ 中的空位根本填不下，那么说明这种情况下无解，直接输出 $-1$ 即可。

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
// #define int long long

using namespace std;

void solve()
{
    int n, k;
    cin >> n >> k;
    vector<int> b(n + 10), pxor(n + 10);
    for (int i = 1; i < n; i++)
    {
        cin >> b[i];
        pxor[i] = pxor[i - 1] ^ b[i];
    }
    vector<int> a1_digit(30, -1);
    for (int i = 1; i < n; i++)
    {
        for (int j = 29; j >= 0; j--)
        {
            int digit_1 = pxor[i - 1] >> j & 1;
            int digit_2 = pxor[i] >> j & 1;
            if (digit_1 != digit_2)
            {
                if (a1_digit[j] == -1)
                {
                    a1_digit[j] = digit_1;
                }
                else if (a1_digit[j] != digit_1)
                {
                    cout << -1 << endl;
                    return;
                }
                break;
            }
        }
    }
    k--;
    for (int i = 0; i <= 29; i++)
    {
        if (a1_digit[i] == -1)
        {
            a1_digit[i] = k & 1;
            k >>= 1;
        }
    }
    if (k)
    {
        cout << -1 << endl;
        return;
    }
    int a1 = 0;
    for (int i = 29; i >= 0; i--)
    {
        a1 = (a1 << 1) + a1_digit[i];
    }
    cout << a1 << ' ';
    for (int i = 1; i < n; i++)
    {
        a1 ^= b[i];
        cout << a1 << ' ';
    }
    cout << endl;
}

signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    int t = 1;
    cin >> t;
    while (t--)
        solve();
    return 0;
}
```

