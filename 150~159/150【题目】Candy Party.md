**Codeforces Round 896 (Div. 2)**

D1. Candy Party (Easy Version)

D2. Candy Party (Hard Version)

https://codeforces.com/contest/1869/problem/D1

https://codeforces.com/contest/1869/problem/D2

<!--more-->

2 seconds / 256 megabytes

standard input / standard output

## Problem (Easy Version)

This is the easy version of the problem. The only difference is that in this version everyone must give candies to exactly one person and receive candies from exactly one person. Note that a submission cannot pass both versions of the problem at the same time. You can make hacks only if both versions of the problem are solved.

After Zhongkao examination, Daniel and his friends are going to have a party. Everyone will come with some candies.

There will be $n$ people at the party. Initially, the $i$-th person has $a_i$ candies. During the party, they will swap their candies. To do this, they will line up in an arbitrary order and everyone will do the following exactly once:

-  Choose an integer $p$ ($1 \le p \le n$) and a non-negative integer $x$, then give his $2^{x}$ candies to the $p$-th person. Note that one cannot give more candies than currently he has (he might receive candies from someone else before) and he cannot give candies to himself. 

Daniel likes fairness, so he will be happy if and only if everyone receives candies from exactly one person. Meanwhile, his friend Tom likes average, so he will be happy if and only if all the people have the same number of candies after all swaps.

Determine whether there exists a way to swap candies, so that both Daniel and Tom will be happy after the swaps.

## Input

The first line of input contains a single integer $t$ ($1\le t\le 1000$) — the number of test cases. The description of test cases follows.

The first line of each test case contains a single integer $n$ ($2\le n\le 2\cdot 10^5$) — the number of people at the party.

The second line of each test case contains $n$ integers $a_1,a_2,\ldots,a_n$ ($1\le a_i\le 10^9$) — the number of candies each person has.

It is guaranteed that the sum of $n$ over all test cases does not exceed $2\cdot 10^5$.

## Output

For each test case, print "Yes" (without quotes) if exists a way to swap candies to make both Daniel and Tom happy, and print "No" (without quotes) otherwise.

You can output the answer in any case (upper or lower). For example, the strings "yEs", "yes", "Yes", and "YES" will be recognized as positive responses.

## Example

Input

> 6
> 3
> 2 4 3
> 5
> 1 2 3 4 5
> 6
> 1 4 7 1 5 4
> 2
> 20092043 20092043
> 12
> 9 9 8 2 4 4 3 5 1 1 1 1
> 6
> 2 12 7 16 11 12

Output

> Yes
> Yes
> No
> Yes
> No
> No

## Note

In the first test case:

-  The first person gives $1$ candy to the third person; 
-  The second person gives $2$ candies to the first person; 
-  The third person gives $1$ candy to the second person. 

Then all people have $3$ candies.

In the second test case:

-  The fifth person gives $4$ candies to the first person, from now on the first person has $5$ candies; 
-  The first person gives $2$ candies to the third person; 
-  The third person gives $2$ candies to the fifth person; 
-  The fourth person gives $2$ candies to the second person; 
-  The second person gives $1$ candy to the fourth person. 

Then all people have $3$ candies. Note that at first the first person cannot give $2$ candies to the third person, since he only has $a_1=1$ candy. But after the fifth person gives him $4$ candies, he can do this, because he currently has $1+4=5$ candies.

In the third test case, it's impossible for all people to have the same number of candies.

In the fourth test case, the first person gives $1024$ candies to the second person, and the second person gives $1024$ candies to the first person as well.

## 题解 (Easy Version)

首先特判总数是否能够平均，即先求出糖果总数，再判断是否能被人数整除，如果不能被整除则输出 `No` 结束。

------

在简单版题目中，限制为每个人必须严格收到一次糖果，即等价于每个人必须严格赠送一次糖果，并且收到和赠送的糖果必须为 $2$ 的幂次。

记第 $i$ 个人的初始糖果数为 $a_i$，最终平均糖果数为 $m$，收到的糖果数的幂次为 $p$，送出的糖果数的幂次为 $q$，那么我们可以得到每个人的初始糖果数需要满足的条件：
$$
\Delta=m-a_i=2^p-2^q
$$
那么我们第二次判断的依据便是上面的公式，即判断糖果变化量 $\Delta$ 是否能够拆分为两个 $2$ 的幂次相减。

判断方式很简单，即观察 $\Delta$ 的二进制表示是否为以下结构：
$$
{\overbrace{1\dots1}^{a个1}\overbrace{0\dots0}^{b个0}}_2
$$
如果 $\Delta$ 的二进制表示满足上面的结构，则可以一定可以拆分为两个二进制数 $x,y$ 满足 $\Delta=x-y$：
$$
\begin{cases}
x={1\overbrace{0\dots0}^{a+b个0}}_2=2^{a+b}\\
y={1\overbrace{0\dots0}^{b个0}}_2=2^b
\end{cases}
$$
将所有初始糖果数判断一遍，只要出现一个不可拆分的，则说明不合法，输出 `No` 结束。

那么对于 $\Delta=0$ 的人，该怎么处理呢？其实可以直接跳过，因为这些人可以插入到任意一个置换环中，他们一定合法。

------

接下来，还有需要满足的条件，即收到和赠送的糖果数必须一一对应，因为如果有一个人赠送了 $k$ 个糖果，那么必须会有一个人收到 $k$ 个糖果。

我们可以使用一个数组记录幂次为 $i$ 的糖果的收发情况，如果一个人收到 $2^i$ 个糖果，则将第 $i$ 位 $-1$，反之 $+1$. 所有操作后检查数组是否全为 $0$，如果不是全 $0$，则说明收发情况非法，输出 `No` 结束。

## 代码 (Easy Version)

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

pair<int, int> bin_split(int x)
{
    int start = -1;
    int t = x, digit = 0;
    while (t)
    {
        digit++;
        t /= 2;
    }
    for (int i = 0; i < digit; i++)
    {
        if (start == -1 && (x >> i & 1) == 1)
            start = i;
        else if (start != -1 && (x >> i & 1) == 0)
            return {-1, -1};
    }
    return {start, digit};
}

void solve()
{
    int n;
    cin >> n;
    vector<int> a(n + 10);
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    int sum = accumulate(a.begin() + 1, a.begin() + 1 + n, 0LL);
    if (sum % n)
    {
        cout << "No" << endl;
        return;
    }
    int mean = sum / n;
    vector<int> remain(40);
    int same = 0;
    for (int i = 1; i <= n; i++)
        if (a[i] == mean)
            same++;
    for (int i = 1; i <= n; i++)
    {
        if (a[i] == mean)
            continue;
        int delta = abs(a[i] - mean);
        auto splt = bin_split(delta);
        if (splt.first == -1)
        {
            cout << "No" << endl;
            return;
        }
        if (a[i] > mean)
        {
            remain[splt.first]--;
            remain[splt.second]++;
        }
        else
        {
            remain[splt.first]++;
            remain[splt.second]--;
        }
    }
    for (int i = 0; i < remain.size(); i++)
    {
        if (remain[i])
        {
            cout << "No" << endl;
            return;
        }
    }
    cout << "Yes" << endl;
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

## Problem (Hard Version)

困难版题面省略，可以自行去原页面查看。困难版与简单版的区别：

*This is the hard version of the problem. The only difference is that in this version everyone must give candies to* **no more than one** *person and receive candies from* **no more than one** *person. Note that a submission cannot pass both versions of the problem at the same time. You can make hacks only if both versions of the problem are solved.*

## 题解 (Hard Version)

困难版与简单版的唯一区别就是，一个人可以不收到糖果，也可以不发送糖果。那么首先需要分析这个区别到底影响了什么。

容易发现，之前我们拆分两个 $2$ 的幂次的思路是建立在必须收到和发送一次的条件下的。对于无法拆分为两个 $2$ 的幂次的 $\Delta$ 没有区别，仍然是不合法的。但是对于可以拆分为两个 $2$ 的幂次的 $\Delta$，那么就需要分类讨论了：

- 如果 $\Delta$ 不是 $2$ 的幂次：只可按照上文方式拆分为两个 $2$ 的幂次相减
- 如果 $\Delta$ 是 $2$ 的幂次：可按照上文方式拆分为两个 $2$ 的幂次相减，也可以不拆分，**直接发送或收到其本身**。

可以发现，如果 $\Delta$ 正好是 $2$ 的幂次，那么就会出现两个分法，这会对最后的合法性判断造成干扰。

------

为了解决这个问题，对于 $\Delta$ 正好是 $2$ 的幂次的情况，先按照不拆分的方式计算，如果出现了不合法的情况，那么再尝试将其拆分，检查拆分后是否能够合法。

具体的流程是，使用三个数组 $a,b,c$，分别储存拆分情况的收发情况，不拆情况的收情况，不拆情况的发情况。

对于 $a_i=0$ 的情况，它已经合法，则跳过。

对于 $a_i\neq0$ 的情况，则需要通过拆分第 $i-1$ 位来弥补缺口。

如果 $a_i<0$，则将 $2^{i-1}$ 拆分为 $2^{i}-2^{i-1}$，需要拆 $a_i$ 个以弥补缺口。则 $a_{i-1}\leftarrow a_{i-1}+a_i,c_{i-1}\leftarrow c_{i-1}+a_i\;$

如果 $a_i>0$，则将 $-2^{i-1}$ 拆分为 $-2^{i}+2^{i-1}$，需要拆 $a_i$ 个以弥补缺口。则 $a_{i-1}\leftarrow a_{i-1}+a_i,b_{i-1}\leftarrow b_{i-1}-a_i\;$

如果过程当中发现不够拆，那么就不合法输出 `No`. 如果发现第 $0$ 位最后不为 $0$，那么也是不合法输出 `No`.

## 代码 (Hard Version)

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

pair<int, int> bin_split(int x)
{
    int start = -1;
    int t = x, digit = 0;
    while (t)
    {
        digit++;
        t /= 2;
    }
    for (int i = 0; i < digit; i++)
    {
        if (start == -1 && (x >> i & 1) == 1)
        {
            start = i;
        }
        else if (start != -1 && (x >> i & 1) == 0)
        {
            return {-1, -1};
        }
    }
    return {start, digit};
}

int log_2(int x)
{
    int num = 1, pow = 0;
    while (num < x)
    {
        num *= 2;
        pow++;
    }
    return num == x ? pow : -1;
}

void solve()
{
    int n;
    cin >> n;
    vector<int> a(n + 10);
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    int sum = accumulate(a.begin() + 1, a.begin() + 1 + n, 0LL);
    if (sum % n)
    {
        cout << "No" << endl;
        return;
    }
    int mean = sum / n;
    vector<int> remain(40), add(40), sub(40);
    for (int i = 1; i <= n; i++)
    {
        if (a[i] == mean)
            continue;
        int delta = abs(a[i] - mean);
        int pow = log_2(delta);
        if (pow != -1)
        {
            if (a[i] > mean)
                sub[pow]++;
            else
                add[pow]++;
            continue;
        }
        auto splt = bin_split(delta);
        if (splt.first == -1)
        {
            cout << "No" << endl;
            return;
        }
        if (a[i] > mean)
        {
            remain[splt.first]--;
            remain[splt.second]++;
        }
        else
        {
            remain[splt.first]++;
            remain[splt.second]--;
        }
    }
    for (int i = 31; i >= 0; i--)
    {
        remain[i] += sub[i];
        remain[i] -= add[i];
        if (i == 0)
            break;
        if (remain[i] < 0)
        {
            sub[i - 1] += remain[i];
            remain[i - 1] += remain[i];
            if (sub[i - 1] < 0)
            {
                cout << "No" << endl;
                return;
            }
        }
        else
        {
            add[i - 1] -= remain[i];
            remain[i - 1] += remain[i];
            if (add[i - 1] < 0)
            {
                cout << "No" << endl;
                return;
            }
        }
    }
    if (remain[0] == 0)
        cout << "Yes" << endl;
    else
        cout << "No" << endl;
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

