**Codeforces Round #834 (Div. 3)**

F. All Possible Digits

https://codeforces.com/contest/1759/problem/F

<!--more-->

3 seconds / 256 megabytes

standard input / standard output

### Problem

A positive number $x$ of length $n$ in base $p$ ($2 \le p \le 10^9$) is written on the blackboard. The number $x$ is given as a sequence $a_1, a_2, \dots, a_n$ ($0 \le a_i < p$) — the digits of $x$ in order from left to right (most significant to least significant).

Dmitry is very fond of all the digits of this number system, so he wants to see each of them at least once.

In one operation, he can:

- take any number $x$ written on the board, increase it by $1$, and write the new value $x + 1$ on the board.

For example, $p=5$ and $x=234_5$.

- Initially, the board contains the digits $2$, $3$ and $4$;
- Dmitry increases the number $234_5$ by $1$ and writes down the number $240_5$. On the board there are digits $0, 2, 3, 4$;
- Dmitry increases the number $240_5$ by $1$ and writes down the number $241_5$. Now the board contains all the digits from $0$ to $4$.

Your task is to determine the minimum number of operations required to make all the digits from $0$ to $p-1$ appear on the board at least once.

### Input

The first line of the input contains a single integer $t$ ($1 \le t \le 2 \cdot 10^3$) — the number of test cases. The descriptions of the input test cases follow.

The first line of description of each test case contains two integers $n$ ($1 \le n \le 100$) and $p$ ($2 \le p \le 10^9$) — the length of the number and the base of the number system.

The second line of the description of each test case contains $n$ integers $a_1, a_2, \dots, a_n$ ($0 \le a_i < p$) — digits of $x$ in number system with base $p$

It is guaranteed that the number $x$ does not contain leading zeros (that is, $a_1>0$).

### Output

For each test case print a single integer — the minimum number of operations required for Dmitry to get all the digits on the board from $0$ to $p-1$.

It can be shown that this always requires a finite number of operations.

### Example

**input**

>11
>2 3
>1 2
>4 2
>1 1 1 1
>6 6
>1 2 3 4 5 0
>5 2
>1 0 1 0 1
>3 10
>1 2 3
>5 1000
>4 1 3 2 5
>3 5
>2 3 4
>4 4
>3 2 3 0
>1 3
>2
>5 5
>1 2 2 2 4
>3 4
>1 0 1

**output**

>1
>1
>0
>0
>7
>995
>2
>1
>1
>1
>2

### 题解

首先可以很容易发现，答案的上界为 $p-1$，即数字个位就能够覆盖 $[0,p)$ 的所有数的情况。可以分成两种情况讨论：个位不进位、个位发生进位。

为方便之后的表述，令 $n$ 位的 $p$ 进制数字为 $a_{n-1}a_{n-2}\dots a_1a_0$，其中 $a_0$ 为个位，$a_{n-1}$ 为最高位。

**数字已经齐全**

若 $>a_0$ 的数字齐全，$<a_0$ 的数字齐全，则说明 $[0,p)$ 齐全。此时答案为 $0$.

**个位不进位**

若 $>a_0$ 的数字不齐，缺失的最大的数字为 $x$，$<a_0$ 的数字齐全，则个位不需要发生进位，从 $a_0$ 一直增到 $x$ 即可。此时答案为 $x-a_0$.

**个位发生进位**

若 $>a_0$ 的数字不齐，$<a_0$ 的数字不齐，那么 $a_0$ 需要一直递增到进位。

将进位前和进位后的数字放到一起考察，若进位后 $<a_0$ 的数字齐全了，此时答案为 $p-a_0$.  若进位后 $<a_0$ 的缺失的最大的数字为 $x$， 此时答案为 $p-a_0+x$.

**易错点**

题目给的数据左边为最高位，右边为最低位，我们读入后在数组中就变成左边为最低位，右边为最高位，需要注意一下顺序，要不然进位就进反了。如果不习惯的话可以用 reverse 把顺序再调换回来。

### 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'

using namespace std;

void solve()
{
    int n, p;
    cin >> n >> p;
    vector<int> a(n);
    // 排序+去重
    set<int> nums;
    for (int i = 0; i < n; i++)
    {
        cin >> a[i];
        nums.insert(a[i]);
    }
    reverse(a.begin(), a.end());
    int afront = a.front();
    vector<int> numv;
    for (auto t : nums)
        numv.push_back(t);
    // 找比a.back()大的最大缺失项
    int missing_big = -1;
    if (numv.back() != p - 1)
    {
        missing_big = p - 1;
    }
    else
    {
        for (int i = numv.size() - 1; i > 0; i--)
        {
            if (numv[i - 1] != numv[i] - 1)
            {
                missing_big = numv[i] - 1;
                break;
            }
        }
    }
    // 找比a.back()小的最大缺失项
    int missing_small = -1;
    if (numv.front() != 0)
        missing_small = numv.front() - 1;
    int idx_aback = lower_bound(numv.begin(), numv.end(), afront) - numv.begin();
    for (int i = idx_aback; i > 0; i--)
    {
        if (numv[i - 1] != numv[i] - 1)
        {
            missing_small = max(missing_small, numv[i] - 1);
            break;
        }
    }
    // 如果都无缺失
    if (missing_big == -1 && missing_small == -1)
    {
        cout << 0 << endl;
        return;
    }
    // 如果比a.back()小的无缺失
    if (missing_small == -1)
    {
        cout << missing_big - afront << endl;
        return;
    }
    // 比a.back()小的有缺失
    // 首先整个数字会进位
    a.front() = p;
    int tmp = 0;
    for (int i = 0; i < a.size(); i++)
    {
        a[i] += tmp;
        tmp = 0;
        if (a[i] == p)
        {
            a[i] = 0;
            tmp = 1;
        }
    }
    if (tmp)
        a.push_back(1);
    // 排序+去重
    for (auto tmp : a)
        nums.insert(tmp);
    numv.clear();
    for (auto tmp : nums)
        numv.push_back(tmp);
    // 找比a.back()小的最大缺失项
    missing_small = -1;
    idx_aback = lower_bound(numv.begin(), numv.end(), afront) - numv.begin();
    for (int i = idx_aback; i > 0; i--)
    {
        if (numv[i - 1] != numv[i] - 1)
        {
            missing_small = numv[i] - 1;
            break;
        }
    }
    // 如果比a.back()小的无缺失
    if (missing_small == -1)
    {
        cout << p - afront << endl;
        return;
    }
    // 如果比a.back()小的有缺失
    cout << p - afront + missing_small << endl;
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    int t;
    cin >> t;
    while (t--)
        solve();
    return 0;
}
```

