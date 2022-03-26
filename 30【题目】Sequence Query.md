**AtCoder Beginner Contest 241（Sponsored by Panasonic）**

D - Sequence Query

https://atcoder.jp/contests/abc241/tasks/abc241_d

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $400$ points

### Problem Statement

We have an empty sequence $A$.
Given $Q$ queries, process them in order.
Each query is of one of the following three types.

- `1 x` : Insert $x$ to $A$.
- `2 x k` : Among the elements of $A$ that are less than or equal to $x$, print the $k$-th largest value. ($k$ is no more than $\bf{5}$)
  If there are less than $k$ elements of $A$ that are less than or equal to $x$, then print `-1`.
- `3 x k` : Among the elements of $A$ that are greater than or equal to $x$, print the $k$-th smallest value. ($k$ is no more than $\bf{5}$)
  If there are less than $k$ elements of $A$ that are greater than or equal to $x$, then print `-1`.

### Constraints

- $1\leq Q \leq 2\times 10^5$
- $1\leq x\leq 10^{18}$
- $1\leq k\leq 5$
- All values in input are integers.

------

### Input

Input is given from Standard Input in the following format:

> $Q$
> $\text{query}_1$
> $\text{query}_2$
> $\vdots$
> $\text{query}_Q$

In the $i$-th query $\text{query}_i$, the type of query $c_i$ (which is either $1, 2$, or $3$) is given first.
If $c_i=1$, then $x$ is additionally given; if $c_i=2, 3$, then $x$ and $k$ are additionally given.

In other words, each query is given in one of the following three formats:

> $1$ $x$
> $2$ $x$ $k$
> $3$ $x$ $k$

### Output

Print $q$ lines, where $q$ is the number of queries such that $c_i=2,3$.
The $j$-th line $(1\leq j\leq q)$ should contain the answer for the $j$-th such query.

------

### Sample Input 1

> 11
> 1 20
> 1 10
> 1 30
> 1 20
> 3 15 1
> 3 15 2
> 3 15 3
> 3 15 4
> 2 100 5
> 1 1
> 2 100 5

### Sample Output 1

> 20
> 20
> 30
> -1
> -1
> 1

After $\text{query}_{1,2,3,4}$ have been processed, we have $A=(20,10,30,20)$.

For $\text{query}_{5,6,7}$, the elements of $A$ greater than or equal to $15$ are $(20,30,20)$.
The $1$-st smallest value of them is $20$; the $2$-nd is $20$; the $3$-rd is $30$.

### 我的笔记

当时不知道 multiset 这个好玩意，所以一直在用奇怪的方法做。

先是用 vector 储存，每次操作都 sort 一次，时间复杂度当然爆炸。然后又尝试用 map 储存数字和数字的个数，不知为何也 TLE 和 WA，怎么调都没调对。因此还是 multiset 最适合这道题。

**\<set\>**
multiset: 可存在重复元素的集合，并且能保证序列有序。
时间复杂度（插入、删除）：$O(\log n)$

因此插入操作只需要直接调用 .insert() 这个成员函数，就可以在 $O(\log n)$ 时间内插入新数字且维护有序性。

寻找元素时使用 multiset 容器自带的二分成员函数，用法和独立的二分函数一样，.upper_bound(x) 为返回 $>x$ 的第一个元素的迭代器，.lower_bound(x) 为返回 $\geq x$ 的第一个元素的迭代器。

寻找 $\leq x$ 的元素时，用 .upper_bound(x) 获得 $>x$ 的第一个元素的迭代器，再将迭代器前移 $1$ 位，就是第 $1$ 个 $\leq x$ 的元素，再前移 $k-1$ 位，就是第 $k$ 个 $\leq x$ 的元素。总体就是将迭代器前移 $k$ 位。

寻找 $\geq x$ 的元素时，用 .lower_bound(x) 获得 $\geq x$ 的第一个元素的迭代器，再将迭代器后移 $k-1$ 位，就是第 $k$ 个 $\geq x$ 的元素。

在移动迭代器时，要注意迭代器不可越过 multiset 的头和尾。要注意 .end() 成员函数返回的是最后一个元素后的迭代器，并非最后一个元素，因此要特判这种情况。

### 代码

```cpp
#include <bits/stdc++.h>

using namespace std;

int main(void)
{
    multiset<long long> A;
    int Q;
    cin >> Q;
    for (int i = 0; i < Q; i++)
    {
        int operation;
        cin >> operation;
        if (operation == 1)
        {
            long long x;
            cin >> x;
            A.insert(x);
        }
        else if (operation == 2)
        {
            long long x, k;
            cin >> x >> k;
            auto it = A.upper_bound(x);
            bool find = true;
            for (int j = 0; j < k; j++)
            {
                if (it == A.begin())
                {
                    find = false;
                    break;
                }
                it--;
            }
            if (find)
                cout << *it << endl;
            else
                cout << "-1" << endl;
        }
        else
        {
            long long x, k;
            cin >> x >> k;
            auto it = A.lower_bound(x);
            bool find = true;
            for (int j = 1; j < k; j++)
            {
                if (it == A.end())
                {
                    find = false;
                    break;
                }
                it++;
            }
            if (it == A.end())
                find = false;
            if (find)
                cout << *it << endl;
            else
                cout << "-1" << endl;
        }
    }
    return 0;
}
```

