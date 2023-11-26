**Educational Codeforces Round 158 (Rated for Div. 2)**

D. Yet Another Monster Fight

https://codeforces.com/contest/1901/problem/D

<!--more-->

2 seconds / 256 megabytes

standard input / standard output

## Problem

Vasya is a sorcerer that fights monsters. Again. There are $n$ monsters standing in a row, the amount of health points of the $i$-th monster is $a_i$.

Vasya is a very powerful sorcerer who knows many overpowered spells. In this fight, he decided to use a chain lightning spell to defeat all the monsters. Let's see how this spell works.

Firstly, Vasya chooses an index $i$ of some monster ($1 \le i \le n$) and the initial power of the spell $x$. Then the spell hits monsters **exactly** $n$ times, one hit per monster. The first target of the spell is always the monster $i$. For every target **except for the first one**, the chain lightning will choose a **random** monster **who was not hit by the spell and is adjacent to one of the monsters that already was hit**. So, each monster will be hit exactly once. The first monster hit by the spell receives $x$ damage, the second monster receives $(x-1)$ damage, the third receives $(x-2)$ damage, and so on.

Vasya wants to show how powerful he is, so he wants to kill all the monsters with a single chain lightning spell. The monster is considered dead if the damage he received is not less than the amount of its health points. On the other hand, Vasya wants to show he doesn't care that much, so he wants to choose the **minimum** initial power of the spell $x$ such that it kills all monsters, **no matter which monster (among those who can get hit) gets hit on each step**.

Of course, Vasya is a sorcerer, but the amount of calculations required to determine the optimal spell setup is way above his possibilities, so you have to help him find the minimum spell power required to kill all the monsters.

Note that Vasya chooses the initial target and the power of the spell, other things should be considered random and Vasya wants to kill all the monsters even in the worst possible scenario.

## Input

The first line of the input contains one integer $n$ ($1 \le n \le 3 \cdot 10^5$) — the number of monsters.

The second line of the input contains $n$ integers $a_1, a_2, \ldots, a_n$ ($1 \le a_i \le 10^9$), where $a_i$ is the amount of health points of the $i$-th monster.

## Output

Print one integer — the minimum spell power required to kill all the monsters if Vasya chooses the first target optimally, and the order of spell hits can be any possible within the given rules.

## Examples

Input

> 6
> 2 1 5 6 4 3

Output

> 8

Input

> 5
> 4 4 4 4 4

Output

> 8

Input

> 2
> 1 1000000000

Output

> 1000000000

## 题解

本题关键在于理解最坏情况是什么。对于一个敌人，我们让它被攻击时，攻击能量损失值最大化，这种情况便是最坏情况。

很容易想到，要让能量损失最大化，便是再攻击它之前，将其他**能攻击的**敌人**全部**都攻击完。

假设初始选定的位置为 $x$，我们现在要攻击敌人 $k$：

- 若敌人 $k$ 在 $x$ 左侧：我们将 $[k+1,n]$ 的所有敌人攻击后再来攻击他，损失值为 $n-(k+1)+1=n-k$.
- 若敌人 $k$ 在 $x$ 右侧：我们将 $[1,k-1]$ 的所有敌人攻击后再来攻击他，损失值为 $k-1-1+1=k-1$.

我们会惊喜地发现，能量损失的值与 $x$ 的选择**无关**，仅与敌人的位置有关。那么我们完全可以直接遍历所有初始 $x$ 的位置，而不需要修改任何值。

若位置为 $x$ 的敌人血量 $a_x$，攻击到它时能量损失值为 $c_x$，那么初始能量必须为 $a_x+c_x$. 我们可以转换一下，直接将位置为 $x$ 的敌人血量看作 $a_x+c_x$，攻击时不再考虑能量损失。

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

void solve()
{
    int n;
    cin >> n;
    vector<int> a(n + 10);
    multiset<int> l{0}, r{0};
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
        r.insert(a[i] + i - 1);
    }
    int ans = INT32_MAX;
    for (int i = 1; i <= n; i++)
    {
        r.extract(a[i] + i - 1);
        ans = min(ans, max({a[i], *l.rbegin(), *r.rbegin()}));
        l.insert(a[i] + n - i);
    }
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

