**AtCoder Beginner Contest 245**

E - Wrapping Chocolate

https://atcoder.jp/contests/abc245/tasks/abc245_e

<!--more-->

Time Limit: 4 sec / Memory Limit: 1024 MB

Score : $500$ points

### Problem Statement

Takahashi has $N$ pieces of chocolate. The $i$-th piece has a rectangular shape with a width of $A_i$ centimeters and a length of $B_i$ centimeters.
He also has $M$ boxes. The $i$-th box has a rectangular shape with a width of $C_i$ centimeters and a length of $D_i$ centimeters.

Determine whether it is possible to put the $N$ pieces of chocolate in the boxes under the conditions below.

- A box can contain at most one piece of chocolate.
- $A_i \leq C_j$ and $B_i \leq D_j$ must hold when putting the $i$-th piece of chocolate in the $j$-th box (they cannot be rotated).

### Constraints

- $1 \leq N \leq M \leq 2\times 10^5$
- $1 \leq A_i,B_i,C_i,D_i \leq 10^9$
- All values in input are integers.

------

### Input

Input is given from Standard Input in the following format:

> $N$ $M$
> $A_1$ $\ldots$ $A_N$
> $B_1$ $\ldots$ $B_N$
> $C_1$ $\ldots$ $C_M$
> $D_1$ $\ldots$ $D_M$

### Output

If it is possible to put the $N$ pieces of chocolate in the boxes, print `Yes`; otherwise, print `No`.

------

### Sample Input 1

> 2 3
> 2 4
> 3 2
> 8 1 5
> 2 10 5

### Sample Output 1

> Yes

We can put the first piece of chocolate in the third box and the second piece in the first box.

------

### Sample Input 2

> 2 2
> 1 1
> 2 2
> 100 1
> 100 1

### Sample Output 2

> No

A box can contain at most one piece of chocolate.

------

### Sample Input 3

> 1 1
> 10
> 100
> 100
> 10

### Sample Output 3

> No

------

### Sample Input 4

> 1 1
> 10
> 100
> 10
> 100

### Sample Output 4

> Yes

### 我的笔记

盒子的长和宽分别大于等于巧克力的长和宽才能放入，同时考虑不太方便，可以分解该问题，先考虑长（或宽），再考虑宽（或长）

首先读入数据，标记好哪个是盒子、哪个是巧克力，然后把它们放到一起排个序。排序原则为长度从大到小，若两个元素长度相同，那么盒子在巧克力前，如果种类也一样，顺序无所谓。

排序后，我们就能保证前面的盒子的长度一定能容纳后面的巧克力，长度问题就解决了，然后考虑宽度。

从前向后遍历，如果是盒子，我们将其宽度存入 multiset 中，如果是巧克力，我们在 multiset 中用二分找到宽度大于等于它的盒子，如果找到，那么由于前面的盒子长度一定能容纳后面的巧克力，这个盒子是有效的，选择这个盒子后将其从 multiset 移除。如果找不到，即二分后迭代器到了 multiset 的尾部，那么就说明找不到宽度能容纳此巧克力的盒子，可以 break 掉循环，输出 No。

### 代码

```cpp
#include <bits/stdc++.h>

using namespace std;

struct DATA
{
    bool type;
    int length, width;
};

bool cmp(DATA a, DATA b)
{
    if (a.length != b.length)
        return a.length > b.length;
    else if (a.type != b.type)
        return a.type > b.type;
    else
        return false;
}

int main(void)
{
    int N, M;
    cin >> N >> M;
    vector<DATA> lst(N + M);
    for (int i = 0; i < N; i++)
    {
        lst[i].type = 0;
        cin >> lst[i].width;
    }
    for (int i = 0; i < N; i++)
    {
        cin >> lst[i].length;
    }
    for (int i = N; i < N + M; i++)
    {
        lst[i].type = 1;
        cin >> lst[i].width;
    }
    for (int i = N; i < N + M; i++)
    {
        cin >> lst[i].length;
    }
    sort(lst.begin(), lst.end(), cmp);
    multiset<int> st;
    bool flag = true;
    for (auto it : lst)
    {
        if (it.type)
        {
            st.insert(it.width);
        }
        else
        {
            if (st.lower_bound(it.width) == st.end())
            {
                flag = false;
                break;
            }
            else
            {
                st.erase(st.lower_bound(it.width));
            }
        }
    }
    if (flag)
        cout << "Yes" << endl;
    else
        cout << "No" << endl;
    return 0;
}
```

