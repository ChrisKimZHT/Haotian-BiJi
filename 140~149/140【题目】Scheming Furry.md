**2023牛客暑期多校训练营8**

K - Scheming Furry

https://ac.nowcoder.com/acm/contest/57362/K

<!--more-->

## 题意

给定一 $n\times m$ 的数组 $\{a\}$，$[1,nm]$ 各出现一次。两个人先后手对数组操作，先手一次必须交换两行，后手一次必须交换两列，谁先把数组变得有序（即按行优先顺序放置数字的顺序）谁获胜。保证初始局面无序，问谁获胜。多测，$1\leq T\leq 100$，$1\leq n,m\leq2\times100$.

## 题解

操作只支持整行交换和整列交换，容易发现，存在不可能完成排序的非法情况。第一步首先判断非法情况，输出 `NSFW`.

然后由于狐狸为先手，如果排序能够一步到位（列已经全部排好，行只需交换一对），那么狐狸可直接胜利，输出 `FOX`.

除去上面两种特殊情况，剩下的便是一般情况。

考虑到只要行(列)数超过三行/列，那么对应行(列)的玩家就有能力阻止对方胜利，因为如果超过三行(列)，那就存在可以永远交换行(列)但不让其变得有序的方法。

所以，如果 $n\geq3\;且\;m\geq3$，那么 FOX 与 CAT 均拥有阻止对方胜利的方法，那么答案一定是 `NSFW`.

如果 $n=2\;且\;m\geq3$，那么 CAT 有阻止对方胜利的方法，所以它的策略将会是要么自己赢，要么都别赢。那么答案只可能是 `CAT` 或 `NSFW`. 为方便，令将列变有序的需要的操作步数为 $c$，那么具体的答案判断方法是：

- 如果行初始有序
  - 如果 $c$ 为奇数：`NSFW`
  - 如果 $c$ 为偶数：`CAT`
- 如果行初始无序
  - 如果 $c$ 为奇数：`CAT`
  - 如果 $c$ 为偶数：`NSFW`

如果 $n\geq3\;且\;m=2$，同理可得，答案只可能是 `FOX` 或 `NSFW`. 具体的答案判断与上面的对称。

除此之外，对于 $n=2\;且\;m=2$ 的情况，因为此时双方均没有阻止对方胜利的方法，因此答案不可能是 `NSFW`，需要特判。这种情况总共就三种可能，直接特判即可。

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
// #define int long long

using namespace std;

constexpr int MAXN = 220;
vector<vector<int>> mat(MAXN, vector<int>(MAXN));
vector<vector<int>> bak(MAXN, vector<int>(MAXN));
int n, m;

void solve()
{
    cin >> n >> m;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            cin >> mat[i][j];
    // sort mat
    vector<pair<int, int>> fr(m + 10);
    for (int i = 1; i <= m; i++)
    {
        fr[i].first = mat[1][i];
        fr[i].second = i;
    }
    sort(fr.begin() + 1, fr.begin() + 1 + m);
    bak = mat;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            bak[i][j] = mat[i][fr[j].second];
    bool col_sorted = true;
    for (int i = 1; i <= m; i++)
        if (bak[1][i] != mat[1][i])
            col_sorted = false;
    sort(bak.begin() + 1, bak.begin() + 1 + n, [](vector<int> &a, vector<int> &b) { return a[1] < b[1]; });
    // fliter illgal cases
    for (int i = 1; i <= n; i++)
    {
        for (int j = 1; j <= m; j++)
        {
            if (bak[i][j] != (i - 1) * m + j)
            {
                cout << "NSFW" << endl;
                return;
            }
        }
    }
    // fox one step win
    if (col_sorted)
    {
        int row_diff = 0;
        for (int i = 1; i <= n; i++)
            if (mat[i][1] != (i - 1) * m + 1)
                row_diff++;
        if (row_diff == 2)
        {
            cout << "FOX" << endl;
            return;
        }
    }
    // row = 2, col = 2
    if (n == 2 && m == 2)
    {
        if (mat[1][1] == 2 || mat[1][1] == 3)
            cout << "FOX" << endl;
        else if (mat[1][1] == 4)
            cout << "CAT" << endl;
        else
            assert(0);
        return;
    }
    // row = 2
    if (n == 2)
    {
        bool row_sorted = mat[1][1] < mat[2][1];
        int col_inv = 0;
        for (int i = 1; i <= m; i++)
            for (int j = i + 1; j <= m; j++)
                if (mat[1][i] > mat[1][j])
                    col_inv++;
        if (col_inv % 2 == !row_sorted)
            cout << "CAT" << endl;
        else
            cout << "NSFW" << endl;
        return;
    }
    // col = 2
    if (m == 2)
    {
        col_sorted = mat[1][1] < mat[1][2];
        int row_inv = 0;
        for (int i = 1; i <= n; i++)
            for (int j = i + 1; j <= n; j++)
                if (mat[i][1] > mat[j][1])
                    row_inv++;
        if (row_inv % 2 == col_sorted)
            cout << "FOX" << endl;
        else
            cout << "NSFW" << endl;
        return;
    }
    cout << "NSFW" << endl;
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

