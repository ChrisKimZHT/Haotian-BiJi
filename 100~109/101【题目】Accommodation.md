**Nebius Welcome Round (Div. 1 + Div. 2)**

D. Accommodation

https://codeforces.com/contest/1804/problem/D

<!--more-->

time limit per test: 2 seconds

memory limit per test: 512 megabytes

input: standard input

output: standard output

## Problem

Annie is an amateur photographer. She likes to take pictures of giant residential buildings at night. She just took a picture of a huge rectangular building that can be seen as a table of $n \times m$ windows. That means that the building has $n$ floors and each floor has exactly $m$ windows. Each window is either dark or bright, meaning there is light turned on in the room behind it.

Annies knows that each apartment in this building is either one-bedroom or two-bedroom. Each one-bedroom apartment has exactly one window representing it on the picture, and each two-bedroom apartment has exactly two consecutive windows on the same floor. Moreover, the value of $m$ is guaranteed to be divisible by $4$ and it is known that each floor has exactly $\frac{m}{4}$ two-bedroom apartments and exactly $\frac{m}{2}$ one-bedroom apartments. The actual layout of apartments is unknown and can be different for each floor.

Annie considers an apartment to be occupied if **at least one** of its windows is bright. She now wonders, what are the minimum and maximum possible number of occupied apartments if judged by the given picture?

Formally, for each of the floors, she comes up with some particular apartments layout with exactly $\frac{m}{4}$ two-bedroom apartments (two consecutive windows) and $\frac{m}{2}$ one-bedroom apartments (single window). She then counts the total number of apartments that have at least one bright window. What is the minimum and maximum possible number she can get?

## Input

The first line of the input contains two positive integers $n$ and $m$ ($1 \leq n \cdot m \leq 5 \cdot 10^5$) — the number of floors in the building and the number of windows per floor, respectively. It is guaranteed that $m$ is divisible by $4$.

Then follow $n$ lines containing $m$ characters each. The $j$-th character of the $i$-th line is "0" if the $j$-th window on the $i$-th floor is dark, and is "1" if this window is bright.

## Output

Print two integers, the minimum possible number of occupied apartments and the maximum possible number of occupied apartments, assuming each floor can have an individual layout of $\frac{m}{4}$ two-bedroom and $\frac{m}{2}$ one-bedroom apartments.

## Examples

Input

> 5 4
> 0100
> 1100
> 0110
> 1010
> 1011

Output

> 7 10

Input

> 1 8
> 01011100

Output

> 3 4

## Note

In the first example, each floor consists of one two-bedroom apartment and two one-bedroom apartments.

The following apartment layout achieves the minimum possible number of occupied apartments equal to $7$. 

<pre>
|0 1|0|0|
|1 1|0|0|
|0|1 1|0|
|1|0 1|0|
|1|0|1 1|
</pre>

The following apartment layout achieves the maximum possible number of occupied apartments equal to $10$. 

<pre>
|0 1|0|0|
|1|1 0|0|
|0 1|1|0|
|1|0 1|0|
|1 0|1|1|
</pre>

## 题解

首先每一层问题都是独立的，所以只需要考虑每一层的解法即可。

**最小情况**

最小情况比较好理解，每多一个连续两个亮灯的双床房，房间占用数减少一，因此尽量让连续两个亮灯的房间看作双床房即可。

首先统计亮灯数 $light$ 和连续两个亮灯的房间数（不重叠计算）$double$，由于双床房数量为 $\frac{m}{4}$，因此被占用的双床房最多为 $\min(double,\frac{m}{4})$，占用数即为：$light-\min(double,\frac{m}{4})$

**最大情况**

最大情况和上述相反，每少一个连续两个亮灯的双床房，房间占用数减加一，因此要尽量减少连续两个亮灯的双床房，即减少**非**连续两个亮灯的双床房。

首先统计亮灯数 $light$ 和**非**连续两个亮灯的房间数（不重叠计算）$notdouble$，由于双床房数量为 $\frac{m}{4}$，因此最多分配 $\min(notdouble,\frac{m}{4})$ 给**非**连续两个亮灯的双床房，剩下的 $\frac{m}{4}-\min(notdouble,\frac{m}{4})$ 都是连续两个亮灯的双床房，占用数即为：$light-(\frac{m}{4}-\min(notdouble,\frac{m}{4}))$

## 代码

```cpp
#include <bits/stdc++.h>

using namespace std;

void solve()
{
    int n, m;
    cin >> n >> m;
    int mn = 0, mx = 0;
    while (n--)
    {
        string s;
        cin >> s;
        int cnt_light = 0;
        for (int i = 0; i < m; i++)
            if (s[i] == '1')
                cnt_light++;
        int cnt_double = 0;
        for (int i = 0; i < m - 1; i++)
        {
            if (s[i] == '1' && s[i + 1] == '1')
            {
                cnt_double++;
                i++;
            }
        }
        cnt_double = min(cnt_double, m / 4);
        mn += cnt_light - cnt_double;
        int cnt_not_double = 0;
        for (int i = 0; i < m - 1; i++)
        {
            if (s[i] == '0' || s[i + 1] == '0')
            {
                cnt_not_double++;
                i++;
            }
        }
        cnt_not_double = min(cnt_not_double, m / 4);
        mx += cnt_light - (m / 4 - cnt_not_double);
    }
    cout << mn << ' ' << mx << endl;
}

signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    solve();
    return 0;
}
```

