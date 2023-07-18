**“范式杯”2023牛客暑期多校训练营1**

L - Three Permutations

https://ac.nowcoder.com/acm/contest/57355/L

<!--more-->

## 题意

给定三个长度为 $n$ 的排列 $a, b, c,(x, y, z)$ 最开始为 $(1,1,1)$, 每过一秒变为 $\left(a_y, b_z, c_x\right)$。$q$ 次询问求变成 $\left(x^{\prime}, y^{\prime}, z^{\prime}\right)$ 的最短时间。 $1 \leq n \leq 10^5, 1 \leq q \leq 10^5$ 。

## 题解

如果这道题的变换是一一对应的，即每过一秒变为 $\left(a_x, b_y, c_z\right)$，那思路就比较清晰，就是分别找到 $a,b,c$ 的循环长度 $l_x,l_y,l_z$ ，然后找到 $1\rightarrow x,\;1\rightarrow y,\;1\rightarrow z$ 的距离 $d_x,d_y,d_z$，那么问题就变成了：
$$
\left\{ 
\begin{matrix}
t \equiv d_x \pmod {l_x} \\ 
t \equiv d_y \pmod {l_y} \\
t \equiv d_z \pmod {l_z}
\end{matrix} \right.
$$

这个线性同余方程组正好与扩展中国剩余定理对应，所以可以直接用它解出来最小的 $t$，这便是最短时间。

该题进行了加强，即变换不是一一对应的，$y\overset{a}\rightarrow x,z\overset{b}\rightarrow y,c\overset{a}\rightarrow x$，这样就会让循环节的寻找变困难，因为 $x,y,z$ 在互相变化。但是可以发现，变换的周期为三，即每三次 $x$ 又会变回 $x$，那么我们就将三次变换看作一个变化：
$$
abc(x)=a(b(c(x)))\\
bca(x)=b(c(a(x)))\\
cab(x)=c(a(b(x)))
$$
这样就让变换变成了一一对应的：$x\overset{abc}\rightarrow x,y\overset{bca}\rightarrow y,z\overset{cab}\rightarrow z$，除此之外还需要维护 $1\overset{abc}\rightarrow x,1\overset{bca}\rightarrow y,1\overset{cab}\rightarrow z$ 的距离 $d_x,d_y,d_z$，那么同样用扩展中国剩余定理，那么就能解出最优解 $t$.

但是需要注意的是，我们上面解出来的是正好 $3t$ 秒时的情况，实际上并不一定为 $3$ 的倍数，即不一定每次都是进行完整的一轮循环。因此我们还需要考虑 $3t+1$ 与 $3t+2$ 时的情况。

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

typedef long long ll;
const int INF = 1e18;

ll exgcd(ll a, ll b, ll &x, ll &y)
{
    if (!b)
    {
        x = 1;
        y = 0;
        return a;
    }
    ll d = exgcd(b, a % b, y, x);
    y -= a / b * x;
    return d;
}

// first-a second-mod
pair<ll, ll> excrt(pair<ll, ll> a, pair<ll, ll> b)
{
    if (a.first == -1 || b.first == -1)
        return {-1, -1};
    ll ya, yb;
    ll d = exgcd(a.second, b.second, ya, yb);
    if ((b.first - a.first) % d)
        return {-1, -1};
    ya *= (b.first - a.first) / d;
    ll tmp = b.second / d;
    ya = (ya % tmp + tmp) % tmp;
    a.first += a.second * ya;
    a.second = a.second / d * b.second;
    return a;
}

void solve()
{
    int n;
    cin >> n;
    vector<int> a(n + 10), b(n + 10), c(n + 10);
    vector<int> ra(n + 10), rb(n + 10), rc(n + 10);
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
        ra[a[i]] = i;
    }
    for (int i = 1; i <= n; i++)
    {
        cin >> b[i];
        rb[b[i]] = i;
    }
    for (int i = 1; i <= n; i++)
    {
        cin >> c[i];
        rc[c[i]] = i;
    }
    vector<int> abc(n + 10), bca(n + 10), cab(n + 10);
    for (int i = 1; i <= n; i++)
    {
        abc[i] = a[b[c[i]]];
        bca[i] = b[c[a[i]]];
        cab[i] = c[a[b[i]]];
    }
    int la = 0, lb = 0, lc = 0;
    vector<int> da(n + 10, -1), db(n + 10, -1), dc(n + 10, -1);
    for (int s = 1; da[s] == -1; s = abc[s], la++)
        da[s] = la;
    for (int s = 1; db[s] == -1; s = bca[s], lb++)
        db[s] = lb;
    for (int s = 1; dc[s] == -1; s = cab[s], lc++)
        dc[s] = lc;

    auto solve = [&](int x, int y, int z) -> ll
    {
        if (da[x] == -1 || db[y] == -1 || dc[z] == -1)
            return INF;
        pair<ll, ll> p(da[x], la);
        pair<ll, ll> q(db[y], lb);
        pair<ll, ll> r(dc[z], lc);
        pair<ll, ll> ans = excrt(p, excrt(q, r));
        if (ans.first == -1)
            return INF;
        return ans.first % ans.second;
    };

    int q;
    cin >> q;
    while (q--)
    {
        int x, y, z;
        cin >> x >> y >> z;
        int ans_0 = solve(x, y, z);
        int ans_1 = solve(rc[z], ra[x], rb[y]);
        int ans_2 = solve(rc[rb[y]], ra[rc[z]], rb[ra[x]]);
        int ans = min({ans_0 * 3, ans_1 * 3 + 1, ans_2 * 3 + 2});
        if (ans >= INF)
            cout << -1 << endl;
        else
            cout << ans << endl;
    }
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

