**AtCoder Beginner Contest 247**

E - Max Min

https://atcoder.jp/contests/abc247/tasks/abc247_e

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $500$ points

### Problem Statement

We have a number sequence $A = (A_1, A_2, \dots, A_N)$ of length $N$ and integers $X$ and $Y$. Find the number of pairs of integers $(L, R)$ satisfying all the conditions below.

- $1 \leq L \leq R \leq N$
- The maximum value of $A_L, A_{L+1}, \dots, A_R$ is $X$, and the minimum is $Y$.

### Constraints

- $1 \leq N \leq 2 \times 10^5$
- $1 \leq A_i \leq 2 \times 10^5$
- $1 \leq Y \leq X \leq 2 \times 10^5$
- All values in input are integers.

------

### Input

Input is given from Standard Input in the following format:

> $N$ $X$ $Y$
> $A_1$ $A_2$ $\dots$ $A_N$

### Output

Print the answer.

------

### Sample Input 1

> 4 3 1
> 1 2 3 1

### Sample Output 1

> 4

$4$ pairs satisfy the conditions: $(L,R)=(1,3),(1,4),(2,4),(3,4)$.

------

### Sample Input 2

> 5 2 1
> 1 3 2 4 1

### Sample Output 2

> 0

No pair $(L,R)$ satisfies the condition.

------

### Sample Input 3

> 5 1 1
> 1 1 1 1 1

### Sample Output 3

> 15

It may hold that $X=Y$.

------

### Sample Input 4

> 10 8 1
> 2 7 1 8 2 8 1 8 2 8

### Sample Output 4

> 36

### 我的笔记

#### 数列分段

要满足 $\max{A[l\sim r]}=X$ 和 $\min{A[l\sim r]}=Y$，那么说明 $Y\leq A[l\sim r]\leq X$ 。如果有一个 $A_i$ 不在要求的最小值和最大值区域内，那么包含了 $A_i$ 的区间都不满足题意。那么我们大可以将原数列在 $A_i$ 处断开，求两个子数列的情况之和即可。

例如 $A=(2,3,1,4,5,3,6,3,2,4)$，$X=5,Y=2$，那么可以将 $A$ 分裂成多个子数列：$(2,3),(4,5,3),(3,2,4)$

时间复杂度：$O(N)$

#### 计算每段数列的情况

我们将数列分段后，易得 $Y\leq A[l\sim r]\leq X$，那么只要区域中包含 $X$ 和 $Y$，这个区域就是符合条件的。

使用双指针，初始化 $l=r=0$，区间中 $X$ 的个数 $cnt_X=0$，区间中 $Y$ 的个数 $cnt_Y=0$，答案 $ans_k=0$.

如果该区间不符合条件，即 $cnt_X=0$ 或 $cnt_Y=0$，那么 $r$ 指针右移，然后更新 $cnt_X,cnt_Y$

如果该区间符合条件，即 $cnt_X>0$ 且 $cnt_Y>0$，那么 $r$ 指针现在的位置和右侧的所有位置都是成立的，那么 $ans_k=ans_k+(子数列长度-r)$，然后 $l$ 指针右移，更新 $cnt_X,cnt_Y$.

时间复杂度：$O(2N)$

这样就可以得到该段数列的情况，题目答案 $res=\sum{ans_k}$

总时间复杂度：$O(3N)$

### 源码

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN = 2e5 + 10;
int N, X, Y;
int A[MAXN];

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> N >> X >> Y;
    for (int i = 0; i < N; i++)
        cin >> A[i];
    int i = 0;
    long long ans = 0;
    while (i < N)
    {
        vector<int> subseq;
        while (i < N && A[i] >= Y && A[i] <= X)
        {
            subseq.push_back(A[i]);
            i++;
        }
        if (!subseq.empty())
        {
            int l = 0, r = 0;
            int cnt_X = 0, cnt_Y = 0;
            if (subseq[0] == X)
                cnt_X++;
            if (subseq[0] == Y)
                cnt_Y++;
            while (l < subseq.size() && r < subseq.size())
            {
                if (!cnt_X || !cnt_Y)
                {
                    r++;
                    if (subseq[r] == X)
                        cnt_X++;
                    if (subseq[r] == Y)
                        cnt_Y++;
                }
                else
                {
                    ans += subseq.size() - r;
                    if (subseq[l] == X)
                        cnt_X--;
                    if (subseq[l] == Y)
                        cnt_Y--;
                    l++;
                }
            }
        }
        else
        {
            i++;
        }
    }
    cout << ans << endl;
    return 0;
}
```

