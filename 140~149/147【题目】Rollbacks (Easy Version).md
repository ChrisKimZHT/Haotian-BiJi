**Codeforces Round 893 (Div. 2)**

E1. Rollbacks (Easy Version)

https://codeforces.com/contest/1858/problem/E1

<!--more-->

3 seconds / 256 megabytes

standard input / standard output

## Problem

This is an easy version of this problem. The only difference between the versions is that you have to solve the hard version in online mode. You can make hacks only if both versions of the problem are solved.

You have an array $a$, which is initially empty. You need to process queries of the following types:

-  \+ $x$ — add the integer $x$ to the end of the array $a$. 
-  \- $k$ — remove the last $k$ numbers from the array $a$. 
-  ! — roll back the last active change (i.e., make the array $a$ the way it was before the change). In this problem, only queries of the first two types (+ and -) are considered as changes. 
-  ? — find the number of distinct numbers in the array $a$. 
## Input

The first line contains an integer $q$ ($1 \leq q \leq 10^6$) — the number of queries.

The next $q$ lines contain the queries as described above.

It is guaranteed that 

- in the queries of the first type, $1 \le x \le 10^6$; 

- in the queries of the second type, $k \ge 1$ and $k$ does not exceed the current length of the array $a$; 

- at the moment of the queries of the third type, there is at least one query of the first or of the second type that can be rolled back. 

It is also guaranteed that the number of queries of the fourth type (?) does not exceed $10^5$.

## Output

For each query of the fourth type output one integer — the number of distinct elements in array $a$ at the moment of query.

## Examples

Input

```
10
+ 1
+ 2
+ 2
?
!
+ 3
- 2
?
+ 1
?
```

Output

```
2
1
1
```

Input

```
6
+ 1
+ 1000000
?
!
!
?
```

Output

```
2
0
```

## Note

In the first example array $a$ changes as follows: 

1. After the first query, $a=[1]$.
2. After the second query, $a=[1,2]$.
3. After the third query, $a=[1,2,2]$.
4. At the moment of the fourth query, there are $2$ distinct intergers in the array $a$: $1$ and $2$.
5. After the fifth query, $a=[1,2]$ (rolled back the change + 2).
6. After the sixth query, $a=[1,2,3]$.
7. After the seventh query, $a=[1]$.
8. At the moment of the eigth query, there is only one $1$ in the array $a$.
9. After the ninth query, $a=[1,1]$.
10. At the moment of the tenth query, there are only two $1$ in the array $a$.

In the second example array $a$ changes as follows: 

1. After the first query, $a=[1]$.
2. After the second query, $a=[1,1000000]$.
3. At the moment of the third query, there are $2$ distinct intergers in the array $a$: $1$ and $1000000$.
4. After the fourth query, $a=[1]$ (rolled back the change + 1000000).
5. After the fifth query, $a=[]$ (rolled back the change + 1).
6. At the moment of the sixth query, there are no integers in the array $a$, so the answer to this query is $0$.

## 题解

如果将 $a$ 数组的每一个状态看作一个节点，那么这道题的操作就可以看做在一棵树上游走。

我们让每个树节点储存 $a$ 数组中的最后一位数字，那么易知每个节点的深度就是对应状态时 $a$ 数组的长度。还需要用栈维护历史记录，储存每一次进行 `+` `-` 操作原始的节点位置，从而实现回滚功能。

如果是 `+ x` 操作，首先将当前节点压入历史记录，然后给当前节点新建一个子节点，赋值为 $x$，并跳到子节点。

如果是 `- k` 操作，首先将当前节点压入历史记录，然后从当前节点向上跳到第 $k$ 个祖先。

如果是 `!` 操作，则弹掉当前栈顶，然后再跳到现在栈顶储存的节点序号。

如果是 `?` 操作，则在当前节点新增查询标记 $x$，表示这个节点被第 $x$ 次查询查到了。查询结果在最后统一计算。

对于 `+` `!` `?` 操作，均能在 $O(1)$ 解决。唯一有问题的是 `-` 操作，它的复杂度是 $O(n)$，如果不断先执行 `-` 然后 `!` 回滚，很容易把复杂度卡到 $O(n^2)$ 然后超时。

为了优化向上找第 $k$ 个祖先的速度，可以使用倍增法解决，维护每个节点的第 $1,2,4,8,\dots$ 个祖先是谁，这样每次就能在 $O(\log n)$ 的速度跳到祖先了。这个其实和[倍增法求 LCA](https://io.zouht.com/123.html) 是同样的代码。

这样，四个操作就都优化好了。不过 `?` 操作并不是在线执行的，在读入所有输入后才会统一开始计算。在读入所有输入后，我们会获得一个构建好的树，此时再 DFS 遍历整颗树的每个节点，并在 DFS 遍历过程中维护答案，遇到有查询标记的节点时，目前的 DFS 答案就是这个查询的答案。

DFS 维护答案的方式也很简单，维护一个 $\text{cnt}_i$，代表数字 $i$ 的出现次数。如果当前节点的值为 $x$，则 $\text{cnt}_x\leftarrow\text{cnt}_x +1$，如果 $\text{cnt}_x$ 是从 $0$ 变成 $1$，就说明数组中多了一种数，那么答案 $\text{res}\leftarrow \text{res}+1$. 记得该节点的所有子节点完成 DFS 后，还需要还原现场。

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
// #define int long long

using namespace std;

constexpr int MAXN = 1e6 + 10;
int num[MAXN], cnt[MAXN], idx;
int fa[MAXN][30];
int cur, res;
stack<int> stk;
vector<int> tree[MAXN], query[MAXN];
vector<int> ans;

void dfs(int now)
{
    int &n = num[now];
    if (n != 0 && cnt[n] == 0)
        res++;
    cnt[n]++;
    for (auto &qid : query[now])
        ans[qid] = res;
    for (auto &nxt : tree[now])
        dfs(nxt);
    cnt[n]--;
    if (n != 0 && cnt[n] == 0)
        res--;
}

void solve()
{
    int q;
    cin >> q;
    stk.push(0);
    while (q--)
    {
        string op;
        cin >> op;
        if (op == "+")
        {
            int x;
            cin >> x;
            num[++idx] = x;
            stk.push(idx);
            tree[cur].push_back(idx);
            fa[idx][0] = cur;
            for (int i = 1; i <= 20; i++)
                fa[idx][i] = fa[fa[idx][i - 1]][i - 1];
            cur = idx;
        }
        else if (op == "-")
        {
            int k;
            cin >> k;
            for (int i = 20; i >= 0; i--)
                if (k >> i & 1)
                    cur = fa[cur][i];
            stk.push(cur);
        }
        else if (op == "!")
        {
            stk.pop();
            cur = stk.top();
        }
        else // if (op == "?")
        {
            query[cur].push_back(ans.size());
            ans.push_back(0);
        }
    }
    dfs(0);
    for (auto &ele : ans)
        cout << ele << endl;
}

signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    int t = 1;
    // cin >> t;
    while (t--)
        solve();
    return 0;
}
```

