**AtCoder Regular Contest 155**

C - Even Sum Triplet

https://atcoder.jp/contests/arc155/tasks/arc155_c

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $700$ points

## Problem Statement

You are given integer sequences of length $N$: $A=(A_1, A_2, \dots, A_N)$ and $B=(B_1, B_2, \dots, B_N)$.

You may perform the following operation any number of times:

- Choose an integer $i\ (1 \leq i \leq N-2)$ such that $A_i+A_{i+1}+A_{i+2}$ is even. Then, rearrange $A_i$, $A_{i+1}$, $A_{i+2}$ as you like.
Determine whether it is possible to make $A$ equal $B$.

## Constraints

- $3 \leq N \leq 2 \times 10^5$
- $1 \leq A_i, B_i \leq 2 \times 10^5$
- All values in the input are integers.
## Input

The input is given from Standard Input in the following format:

> $N$
> $A_1$ $A_2$ $\dots$ $A_N$
> $B_1$ $B_2$ $\dots$ $B_N$

## Output

If it is possible to make $A$ equal $B$, print Yes; otherwise, print No.

## Sample Input 1

> 5
> 1 2 3 4 5
> 3 1 2 4 5

## Sample Output 1

> Yes

$A_1+A_2+A_3$ is $1+2+3=6$, which is even, so you can choose $i=1$.

If you choose $i=1$ and rearrange $A_1, A_2, A_3$ into $A_3, A_1, A_2$, then $A$ becomes $(3, 1, 2, 4, 5)$.

Now $A$ equals $B$, so you should print `Yes`.

## Sample Input 2

> 5
> 1 2 4 6 5
> 5 1 4 2 6

## Sample Output 2

> No

## Sample Input 3

> 9
> 2 10 4 3 6 2 6 8 5
> 2 4 10 3 8 6 6 2 5

## Sample Output 3

> Yes

## 题解

#### 该操作是可逆的

如果数列 $X$ 经过若干次操作后变为数列 $Y$，那么数列 $Y$ 一定能通过相反的操作还原为 $X$. 这个结论是显而易见的。

#### 如何确认 $A$ 与 $B$ 之间的关系

$A$ 与 $B$ 都是可以变化的，例如将 $A$ 做一次操作变成 $A'$，这两个数列在本质上是没有区别的，它们可以互相转化，这个任意性给比较 $A$ 与 $B$ 造成了很大的麻烦。

如果我们可以将 $A$ 与 $B$ 统一成一种唯一的标准型 $Norm(A),Norm(B)$，满足：

- 能通过操作互相转化的数列，标准型一定相同
- 不能通过操作互相转化的数列，标准型一定不同

那么我们的比较就变得非常方便了，即：

- $Norm(A)=Norm(B)$：输出 Yes
- $Norm(A)\neq Norm(B)$：输出 No

#### 如何设计一个标准型

由于数列中元素的顺序是无意义的，所以我们想到用排序来消除这种差异，但是由于题目限制，我们是无法任意排序的。

1. 当整个数列全是奇数时

数列无法进行任何操作，因此**其本身就是标准型**。

2. 当整个数列不存在距离 $\leq2$ 的奇数时（即任意 $3$ 个相邻的数，奇数个数 $\leq1$）

奇数无法做任何移动，各个奇数将数列分成了若干个偶数段，若偶数段的长度 $\geq3$，那么该偶数段的元素可以任意变换顺序。

通过上述特性，我们**将每个能够任意变换顺序的偶数段进行排序，结果作为标准型**。

3. 存在距离 $\leq2$ 的奇数时（即存在 $3$ 个相邻的数，奇数个数 $\geq2$）

两个奇数可以通过操作任意左右移动，如右移的方式如下：
$$
\dots,偶,奇,奇,偶,偶,\dots\\
\downarrow\\
\dots,偶,奇,偶,奇,偶,\dots\\
\downarrow\\
\dots,偶,偶,奇,奇,偶,\dots\\
$$
若碰到了更多奇数，活动区间可以变得更长如 $(奇,奇,奇,\dots)$，但不影响它的左右移动，还是每次把一个偶数从一边挪到另一边就行了。因此，我们可以通过该操作，将所有奇数放在一起，把所有偶数放在一起，我们规定奇数放前面偶数放后面。

同时也能对这串连续的奇数进行排序，只需借助一个偶数即可：

- 可将一个偶数移动到奇数中的任意位置，而不改变奇数顺序
- 偶数两旁的奇数可调换位置：$(奇_1,偶,奇_2)\Leftrightarrow(奇_2,偶,奇_1)$
- 综上，可以交换任意相邻奇数的位置，使用冒泡排序就能将完成排序

如果偶数数量 $\geq3$ 偶数也能进行排序，反之偶数的位置无法调换。

通过上述特性，我们**将奇数放在前面并对其排序，偶数放在后面，若偶数数量 $\geq3$ 则对其排序，结果作为标准型**。

#### 算法流程

判断 $A,B$ 的类型：

- 若 $A,B$ 的类型不同：输出 No
- 若 $A,B$ 的类型相同
  - 若类型为全奇数
    - $A,B$ 相同：输出 Yes
    - $A,B$ 不同：输出 No
  - 若类型为无相邻或有相邻
    - $A,B$ 的标准型相同：输出 Yes
    - $A,B$ 的标准型不同：输出 No

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'

using namespace std;

// 计算类型: 0-无相邻 1-有相邻 2-全奇数
int type(vector<int> &v)
{
    int last = -3, cnt_odd = 0, ans = 0;
    for (int i = 0; i < v.size(); i++)
    {
        if (v[i] % 2)
        {
            if (i - last <= 2)
                ans = 1;
            last = i;
            cnt_odd++;
        }
    }
    if (cnt_odd == v.size())
        ans = 2;
    return ans;
}

// 计算标准型
vector<int> norm(vector<int> &v, int type)
{
    if (type == 0)
    {
        int last = -1;
        for (int i = 0; i < v.size(); i++)
        {
            if (v[i] % 2)
            {
                if (i - last >= 4)
                    sort(v.begin() + last + 1, v.begin() + i);
                last = i;
            }
        }
        sort(v.begin() + last + 1, v.end());
        return v;
    }
    else if (type == 1)
    {
        vector<int> odd, even;
        for (auto ele : v)
        {
            if (ele % 2)
                odd.push_back(ele);
            else
                even.push_back(ele);
        }
        sort(odd.begin(), odd.end());
        if (even.size() >= 3)
            sort(even.begin(), even.end());
        odd.insert(odd.end(), even.begin(), even.end());
        return odd;
    }
    return v;
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    int N;
    cin >> N;
    vector<int> A(N), B(N);
    for (int i = 0; i < N; i++)
        cin >> A[i];
    for (int i = 0; i < N; i++)
        cin >> B[i];
    int ia = type(A), ib = type(B); // 计算类型
    if (ia != ib)                   // 类型不同直接判假
        cout << "No" << endl;
    else if (ia == 2) // 若均为全奇数类型，比对原型
        cout << (A == B ? "Yes" : "No") << endl;
    else // 若为无相邻或有相邻类型，比对标准型
        cout << (norm(A, ia) == norm(B, ib) ? "Yes" : "No") << endl;
    return 0;
}
```

