**Educational Codeforces Round 137 (Rated for Div. 2)**

D. Problem with Random Tests

https://codeforces.com/contest/1743/problem/D

<!--more-->

4 seconds / 512 megabytes

standard input / standard output

### Problem

You are given a string $s$ consisting of $n$ characters. Each character of $s$ is either 0 or 1.

A substring of $s$ is a contiguous subsequence of its characters.

You have to choose two substrings of $s$ (possibly intersecting, possibly the same, possibly non-intersecting — just any two substrings). After choosing them, you calculate the value of the chosen pair of substrings as follows:

- let $s_1$ be the first substring, $s_2$ be the second chosen substring, and $f(s_i)$ be the integer such that $s_i$ is its binary representation (for example, if $s_i$ is $\mathtt{11010}$, $f(s_i)$ = 26);
- the value is the **bitwise OR** of $f(s_1)$ and $f(s_2)$.

Calculate the maximum possible value you can get, and print it **in binary representation without leading zeroes**.

### Input

The first line contains one integer $n$ — the number of characters in $s$.

The second line contains $s$ itself, consisting of exactly $n$ characters $\mathtt{0}$ and/or $\mathtt{1}$.

**All non-example tests in this problem are generated randomly: every character of $s$ is chosen independently of other characters; for each character, the probability of it being $\mathtt{1}$ is exactly $\frac{1}{2}$**.

This problem has exactly $40$ tests. Tests from $1$ to $3$ are the examples; tests from $4$ to $40$ are generated randomly. In tests from $4$ to $10$, $n = 5$; in tests from $11$ to $20$, $n = 1000$; in tests from $21$ to $40$, $n = 10^6$.

**Hacks are forbidden in this problem.**

### Output

Print the maximum possible value you can get **in binary representation without leading zeroes**.

### Examples

input 1

> 5
> 11010

output 1

> 11111

input 2

> 7
> 1110010

output 2

> 1111110

input 3

> 4
> 0000

output 3

> 0

### Note

In the first example, you can choose the substrings $\mathtt{11010}$ and $\mathtt{101}$. $f(s_1) = 26$, $f(s_2) = 5$, their bitwise OR is $31$, and the binary representation of $31$ is $\mathtt{11111}$.

In the second example, you can choose the substrings $\mathtt{1110010}$ and $\mathtt{11100}$.

### 题解

（下方字符串下标均从第 $1$ 位开始计，写程序时注意调整）

**选择的子串之一一定是原串本身：**

若从左到右第一个 $\mathtt{1}$ 为第 $x$ 位，答案最大位数为 $n-x+1$ 位。当且仅当选择原串时，能达到最大位数。

由此可知，其中一个子串选择原串时，为最好的情况。

**暴力做法：**

根据以上推导，我们已经可以得出一种做法，即其中一个子串选择原串，另一个子串选择原串第 $1$ 位到第 $i$ 位（$i=1,2,\dots,n$），共 $n$ 种情况。另外，需要遍历整个字符串来得出两字符串做位运算的结果，每遍历一遍需要 $n$ 次循环。

子串都从第 $1$ 位开始取的原因：将选择原串的第 $x$ 位到第 $i$ 位，改为选择第 $1$ 位到第 $i$ 位，结果不会变差（只会不变或变好）。

综上，该暴力做法的时间复杂度为：$O(n^2)$，在 $10^6$ 的规模下一定会超时。

**加速上述做法：**

我们可以推断，为了最好的结果，我们选择的子串的起始点一定在第一个连续 $\mathtt{1}$ 区间内，并且这个子串的第一位一定填补了原串的第一个连续 $\mathtt{1}$ 区间后的第一个 $\mathtt{0}$ 位。

因为选择的子串只能填补子串起始点右侧的 $\mathtt{0}$，而越靠左的 $\mathtt{0}$ 位数越高，应当优先填充。如此，我们的子串起始点一定在第一个连续的 $\mathtt{1}$ 区间内。且起始点刚好能填补掉右侧的第一个 $\mathtt{0}$，设它为第 $x$ 位，为了满足这个条件，选择的子串长度为 $n-x+1$.

举一个例子演示上述思路：原串 $\mathtt{01101001}$

我们理应填充处于第 $1$ 位的 $\mathtt{0}$，但是由于它的左侧没有 $\mathtt{1}$，我们没办法填充。因此我们能填充的为第 $4$ 位的 $\mathtt{0}$，我们选择的子串起始点在第一个 $\mathtt{1}$ 区间内，即第 $2$ 位到第 $3$ 位。选择的子串长度为 $8-4+1=5$，这样刚好能覆盖掉第 $4$ 位的 $\mathtt{0}$.

即选择子串为 $\mathtt{11010}$ 或 $\mathtt{10100}$，至于具体选哪个，我们遍历第一个连续 $\mathtt{1}$ 区间的所有情况，找到最优的情况即可。

**能直接遍历的原因：**

上面的思路仍然需要遍历所有情况，但是这个情况数目就不是 $n$ 了，而是第一个连续 $\mathtt{1}$ 区间的长度。

由题意可知，数据为随机生成，每一位为 $0$ 和 $1$ 的概率均为 $1/2$ 。那么连续 $\mathtt{1}$ 区间的长度 $X$ 的期望为：
$$
E(X)=\sum_{i=1}^{n}{\frac{n}{2^n}}=2
$$
那么时间复杂度便为 $O(2n)$，非常合理。

### 代码

```cpp
#include <bits/stdc++.h>

using namespace std;

string str_or(string &a, string &b)
{
    if (a.size() >= b.size())
    {
        string ans = a;
        for (int i = 0; i < b.size(); i++)
            if (b[i] == '1')
                ans[a.size() - b.size() + i] = '1';
        return ans;
    }
    return str_or(b, a);
}

string remove_zero(string &s)
{
    string ans = s;
    while (ans[0] == '0' && ans.size() - 1)
        ans.erase(ans.begin());
    return ans;
}

int main()
{
    int n;
    cin >> n;
    string s;
    cin >> s;
    int one = s.find('1'), zero = s.find('0', one);
    string ans = s;
    if (zero != s.npos)
    {
        for (int i = one; i < zero; i++)
        {
            string sub_s = s.substr(i, n - zero);
            ans = max(ans, str_or(s, sub_s));
        }
    }
    cout << remove_zero(ans) << endl;
    return 0;
}
```

