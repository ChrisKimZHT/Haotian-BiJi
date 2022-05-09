**AtCoder Beginner Contest 250**

E - Prefix Equality

https://atcoder.jp/contests/abc250/tasks/abc250_e

<!--more-->

Time Limit: 4 sec / Memory Limit: 1024 MB

Score : $500$ points

### Problem Statement

You are given integer sequences $A = (a_1,\ldots,a_N)$ and $B = (b_1,\ldots,b_N)$, each of length N*N*.

For $i=1,...,Q$, answer the query in the following format.

- If the set of values contained in the first $x_i$ terms of $A$, $(a_1,\ldots,a_{x_i}),$ and the set of values contained in the first $y_i$ terms of $B$, $(b_1,\ldots,b_{y_i})$, are equal, then print `Yes`; otherwise, print `No`.

### Constraints

- $1 \leq N,Q \leq 2 \times 10^5$
- $1 \leq a_i,b_i \leq 10^9$
- $1 \leq x_i,y_i \leq N$
- All values in input are integers.

------

### Input

Input is given from Standard Input in the following format:

> $N$
> $a_1$ $\ldots$ $a_N$
> $b_1$ $\ldots$ $b_N$
> $Q$
> $x_1$ $y_1$
> $\vdots$
> $x_Q$ $y_Q$

### Output

Print $Q$ lines. The $i$-th line should contain the response to the $i$-th query.

------

### Sample Input 1

> 5
> 1 2 3 4 5
> 1 2 2 4 3
> 7
> 1 1
> 2 2
> 2 3
> 3 3
> 4 4
> 4 5
> 5 5

### Sample Output 1

> Yes
> Yes
> Yes
> No
> No
> Yes
> No

Note that sets are a concept where it matters only whether each value is contained or not.
For the $3$-rd query, the first $2$ terms of $A$ contain one $1$ and one $2$, while the first $3$ terms of $B$ contain one $1$ and two $2$'s. However, the sets of values contained in the segments are both $\{ 1,2 \}$, which are equal.
Also, for the 66-th query, the values appear in different orders, but they are still equal as sets.

### 我的笔记

使用 $hash$ 计算出 $a_1\sim a_x$ 和 $b_1\sim b_x\;(1\leq x\leq N)$ 所含数字集合的 $hash$ 值，存到长为 $N$ 的数组 $hash\_a$、$hash\_b$ 内。

判断时，只需判断 $hash\_a[x_i]$ 是否等于 $hash\_b[y_i]$。若等于，则集合相等；不等，则集合不等。

$hash$ 函数可用 $f(x)=x\cdot(x+1326)\cdot(x+9185)$，使用自然溢出法。

### 源码

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN = 2e5 + 20;
int N, Q;
unsigned int hash_a[MAXN], hash_b[MAXN];

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> N;
    set<int> st;
    for (int i = 0; i < N; i++)
    {
        int tmp;
        cin >> tmp;
        if (st.find(tmp) == st.end())
        {
            st.insert(tmp);
            hash_a[i + 1] = (hash_a[i] + tmp * (tmp + 1326) * (tmp + 9185));
        }
        else
        {
            hash_a[i + 1] = hash_a[i];
        }
    }
    st.clear();
    for (int i = 0; i < N; i++)
    {
        int tmp;
        cin >> tmp;
        if (st.find(tmp) == st.end())
        {
            st.insert(tmp);
            hash_b[i + 1] = (hash_b[i] + tmp * (tmp + 1326) * (tmp + 9185));
        }
        else
        {
            hash_b[i + 1] = hash_b[i];
        }
    }
    cin >> Q;
    while (Q--)
    {
        int a, b;
        cin >> a >> b;
        if (hash_a[a] == hash_b[b])
            cout << "Yes" << endl;
        else
            cout << "No" << endl;
    }
    return 0;
}
```

