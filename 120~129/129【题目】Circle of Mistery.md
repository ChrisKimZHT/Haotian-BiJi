**2023牛客暑期多校训练营5**

B - Circle of Mistery

https://ac.nowcoder.com/acm/contest/57359/B

<!--more-->

## 题意

考虑长度为 $n$ 的每个排列 $P=\left\{p_1, p_2, \cdots, p_n\right\}$，并给定权值数组 $\{w\}_{i=1}^n$ 和权值阈值 $k$，若 $P$ 中存在一个任意长度的置换环 $\left\{a_1, a_2, \cdots, a_l\right\}$ 满足 $\sum_{i=1}^l w_{a_i} \geq k$，则考虑统计该排列 $P$ 的逆序对数。问符合条件的排列中最小逆序对数目是多少。 

$1 \leq n \leq 10^3,-10^6 \leq w_i, k \leq 10^6$ 。

## 题解

首先，需要考虑选择了 $\left\{a_1, a_2, \cdots, a_x\right\}$ 这些元素构造置换环，剩下的不在置换环中的元素该如何排布。观察可以得到，若要让逆序对最小化，除了构造的环内的元素外，其他的位置的元素我们都让它满足 $p_i=i$ 即处于原位。

------

然后，需要考虑选择了 $\left\{a_1, a_2, \cdots, a_x\right\}$ 这些元素构造置换环，这些元素该怎么成环。为了保证逆序对最小化，置换环中尽量保持顺序，一种可行的方式是  $p_{a_1}=a_{2},\;p_{a_2}=a_3,\;\dots,p_{a_{x-1}}=a_x,\;p_{a_x}=a_1$，还有一种最大值在第一个的其实是一样的。

------

现在，根据上面的策略，如果我们选择了 $\left\{a_1, a_2, \cdots, a_x\right\}$ 这些元素构造置换环，那么逆序对的数量就是确定的了。例如下面这个例子，下划线代表选中在置换环中的元素，为了方便，记置换环起点 $i$（本例为 $2$），终点为 $j$（本例为 $8$），环长度 $l$（本例为 $4$）：
$$
1,\underline5,3,4,\underline7,6,\underline8,\underline2,9
$$
那么对于位于最后的最小值（本例中的 $2$），它与置换环中的所有其他元素（本例中的 $5,7,8$）都逆序，个数为 $l-1$。还有处于置换环区间里的满足 $p_i=i$ 的元素，它们每个会产生 $2$ 个逆序，一个是与右侧的最小值（本例中的 $2$），一个是与左侧的错位值（本例中 $5,7$），个数共 $2\cdot(j-i+1-l)$.

综上，给定一个置换环元素集合，它的逆序对数目为：$l-1+2\cdot(j-i+1-l)=2\cdot(j-i)-l+1$.

------

现在，问题就转换为了选出 $\left\{a_1, a_2, \cdots, a_x\right\}$ 元素，满足 $\sum_{i=1}^l w_{a_i} \geq k$ 的同时使 $2\cdot(j-i)-l+1$ 最大化.

注意到逆序对数目这个式子，对于一个固定区间 $[i,j]$，如果选择的个数 $l$ 越多逆序对越少。由于 $n\leq 10^3$，那么区间 $[i,j]$ 可以用双重循环解决，只需要考虑的是给定一个区间，如何尽量选更多的元素。时间复杂度 $O(n^2)$

考虑贪心思想，要求求和最大的话，对于 $\geq0$ 的元素必选，而对于 $<0$ 的元素，从最大的开始选，一直尝试选到 $k$ 不成立为止。如此就能保证 $[i,j]$ 区间中选出尽量多的元素，然后更新答案即可。

可用一个 `multiset` 储存元素并维护大小，取的时候从最大开始往小取。时间复杂度 $O(\log n)$

总时间复杂度：$O(n^2\log n)$.

-----

注意，无法取任何数成环是代表不成立，即代码中出现 $l=0$，那是不成立的状态，不能用 $2\cdot(j-i)-l+1$ 把答案更新了，需要特判跳过。

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

void solve()
{
    int n, k;
    cin >> n >> k;
    vector<int> w(n + 10);
    for (int i = 1; i <= n; i++)
        cin >> w[i];
    int ans = INT64_MAX;
    for (int i = 1; i <= n; i++)
    {
        int sum = 0, cnt = 0;
        multiset<int> ms;
        for (int j = i; j <= n; j++)
        {
            if (w[j] < 0)
            {
                ms.insert(w[j]);
            }
            else
            {
                sum += w[j];
                cnt++;
            }
            if (sum >= k)
            {
                int tsum = sum, tcnt = cnt;
                for (auto ti = ms.rbegin(); ti != ms.rend(); ++ti)
                {
                    if (tsum + *ti < k)
                        break;
                    tsum += *ti;
                    tcnt++;
                }
                if (tcnt)
                    ans = min(ans, 2 * (j - i) - tcnt + 1);
            }
        }
    }
    if (ans == INT64_MAX)
        cout << -1 << endl;
    else
        cout << ans << endl;
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

