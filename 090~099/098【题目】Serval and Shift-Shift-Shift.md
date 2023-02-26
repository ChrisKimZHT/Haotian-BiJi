**Codeforces Round #853 (Div. 2)**

D. Serval and Shift-Shift-Shift

https://codeforces.com/contest/1789/problem/D

<!--more-->

2 seconds / 256 megabytes

standard input / standard output

## Problem

Serval has two $n$-bit binary integer numbers $a$ and $b$. He wants to share those numbers with Toxel.

Since Toxel likes the number $b$ more, Serval decides to change $a$ into $b$ by some (possibly zero) operations. In an operation, Serval can choose any positive integer $k$ between $1$ and $n$, and change $a$ into one of the following number:

-  $a\oplus(a\ll k)$ 
-  $a\oplus(a\gg k)$ 

In other words, the operation moves every bit of $a$ left or right by $k$ positions, where the overflowed bits are removed, and the missing bits are padded with $0$. The bitwise XOR of the shift result and the original $a$ is assigned back to $a$.

Serval does not have much time. He wants to perform no more than $n$ operations to change $a$ into $b$. Please help him to find out an operation sequence, or determine that it is impossible to change $a$ into $b$ in at most $n$ operations. You do not need to minimize the number of operations. 

In this problem, $x\oplus y$ denotes the [bitwise XOR operation](https://en.wikipedia.org/wiki/Bitwise_operation#XOR) of $x$ and $y$. $a\ll k$ and $a\gg k$ denote the [logical left shift](https://en.wikipedia.org/wiki/Logical_shift) and [logical right shift](https://en.wikipedia.org/wiki/Logical_shift).

## Input

Each test contains multiple test cases. The first line contains the number of test cases $t$ ($1\le t\le2\cdot10^{3}$). The description of the test cases follows.

The first line of each test case contains a single integer $n$ ($1\le n\le2\cdot10^{3}$) — the number of bits in numbers $a$ and $b$.

The second and the third line of each test case contain a binary string of length $n$, representing $a$ and $b$, respectively. The strings contain only characters 0 and 1.

It is guaranteed that the sum of $n$ over all test cases does not exceed $2\cdot10^{3}$.

## Output

For each test case, if it is impossible to change $a$ into $b$ in at most $n$ operations, print a single integer $-1$.

Otherwise, in the first line, print the number of operations $m$ ($0\le m\le n$).

If $m>0$, in the second line, print $m$ integers $k_{1},k_{2},\dots,k_{m}$ representing the operations. If $1\le k_{i}\le n$, it means logical left shift $a$ by $k_{i}$ positions. If $-n\le k_{i}\le-1$, it means logical right shift $a$ by $-k_{i}$ positions.

If there are multiple solutions, print any of them.

## Example

**Input**

> 3
> 5
> 00111
> 11000
> 1
> 1
> 1
> 3
> 001
> 000

**Output**

> 2
> 3 -2
> 0
> -1

**Note**

In the first test case:

The first operation changes $a$ into $\require{cancel}00111\oplus\cancel{001}11\underline{000}=11111$.

The second operation changes $a$ into $\require{cancel}11111\oplus\underline{00}111\cancel{11}=11000$.

The bits with strikethroughs are overflowed bits that are removed. The bits with underline are padded bits.

In the second test case, $a$ is already equal to $b$, so no operations are needed.

In the third test case, it can be shown that $a$ cannot be changed into $b$.

## 题解

这道题是一道构造题，我们的思路就是如何构造一种通法来解决这个问题。

#### 不成立的情形

按位异或时，每一个二进制位具有以下特性（$\neg$ 符号指取反）：
$$
\begin{align}
0\oplus bit&=bit\\
1\oplus bit&=\neg bit
\end{align}
$$
那么我们可以发现，只有 $1$ 能对二进制位进行改变，$0$ 无法产生变化。那么，如果有一个串全为 $0$，我们无论如何都没法改变它的值。

由此我们就能推出不成立的情形，即：若 $a\neq b$ 且 $a$ 或 $b$ 全零，则这种情况不成立，输出 $-1$.

#### 如何构造通解

首先注意到 $n$ 的规模仅有 $2\cdot 10^3$，足够我们可以模拟每次操作。并且题目没有要求将步数最小化，那么就没必要研究各种情况的特点，直接考虑所有情况的通用解法就行。

如 $a=10010,\ b=10101$，先右移两位进行操作：
$$
10010\\
\quad\quad10010\\
\overline{10110}\\
$$

再右移三位进行操作：

$$
10110\\
\quad\quad\quad10110\\
\overline{10100}\\
$$
再右移四位进行操作：
$$
10100\\
\quad\quad\quad\quad10100\\
\overline{10101}\\
$$
我们可以发现，操作的实质就是用最左边一位的 $1$ 右移到对应位置，从左到右把和 $b$ 不同的位变成相同的位，最后就能将 $a$ 变为 $b$.

在调整一位的适合，可能会有副作用，导致右边有已经一致的位又变得不一致，但这也没有关系，因为我们是从左到右进行操作的，发生变化的只有可能是右侧部分，每一次操作后我们能保证左侧部分已经一致。

此时还有一个问题，如果 $a$ 的最左边一位不是 $1$，就没法用它来调整右侧位了。此时我们可以首先构造最左侧的 $1$，即用最右侧的 $1$ 左移到最左侧，把最左侧变成 $1$：
$$
\quad\quad\quad\quad00011\\
00011\\
\quad\quad\quad\quad\overline{10011}\\
$$
这样就成功构造最左侧的 $1$ 了，如果答案 $b$ 最左侧不是 $1$，我们在进行完所有操作后，还得把最左侧的 $1$ 还原成 $0$，方法也是一样：
$$
\quad\quad\quad\quad10011\\
10011\\
\quad\quad\quad\quad\overline{00011}\\
$$

#### 如何确保步数限制

题目要求步数限制不超过 $n$，由于上面这个思路定死了：用最左侧的 $1$ 从左到右进行操作。所以有些情况会超出步数限制：$1$ 步构造最左侧的 $1$，$n-1$ 步调整右侧位，$1$ 步还原最左侧的 $1$。超过步数限制时，此时就得反着来，用最右侧的 $1$ 从右到左进行操作，就能符合步数限制。

反着来时，没有必要再反着写一遍代码，直接把串 $a,b$ 逆序跑一遍原算法，把结果取相反数即可，更加方便。

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'

using namespace std;

vector<int> getans(string a, string b)
{
    vector<int> ans;
    int n = a.size();
    if (a[0] == '0')
    {
        int t = n - 1;
        while (a[t] == '0')
            t--;
        ans.push_back(t);
        a[0] = '1';
    }
    for (int i = 1; i < n; i++)
    {
        if (a[i] != b[i])
        {
            ans.push_back(-i);
            string t = a;
            for (int j = 0; j < n; j++)
            {
                if (i + j >= n)
                    break;
                if (t[j] == a[i + j])
                    a[i + j] = '0';
                else
                    a[i + j] = '1';
            }
        }
    }
    if (b[0] == '0')
    {
        int t = n - 1;
        while (a[t] == '0')
            t--;
        ans.push_back(t);
        a[0] = '0';
    }
    return ans;
}

void solve()
{
    int n;
    cin >> n;
    string a, b;
    cin >> a >> b;
    if (a == b)
    {
        cout << 0 << endl;
        return;
    }
    if (a.find('1') == a.npos ||
        b.find('1') == b.npos)
    {
        cout << -1 << endl;
        return;
    }
    vector<int> ans = getans(a, b);
    if (ans.size() <= n)
    {
        cout << ans.size() << endl;
        for (auto ele : ans)
            cout << ele << ' ';
        cout << endl;
        return;
    }
    reverse(a.begin(), a.end());
    reverse(b.begin(), b.end());
    ans = getans(a, b);
    cout << ans.size() << endl;
    for (auto ele : ans)
        cout << -ele << ' ';
    cout << endl;
    return;
}

int main()
{
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
