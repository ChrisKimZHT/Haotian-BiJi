**Educational Codeforces Round 159 (Rated for Div. 2)**

E. Collapsing Strings

https://codeforces.com/contest/1902/problem/E

<!--more-->

2 seconds / 256 megabytes

standard input / standard output

## Problem

You are given $n$ strings $s_1, s_2, \dots, s_n$, consisting of lowercase Latin letters. Let $|x|$ be the length of string $x$.

Let a collapse $C(a, b)$ of two strings $a$ and $b$ be the following operation: 

-  if $a$ is empty, $C(a, b) = b$; 
-  if $b$ is empty, $C(a, b) = a$; 
-  if the last letter of $a$ is equal to the first letter of $b$, then $C(a, b) = C(a_{1,|a|-1}, b_{2,|b|})$, where $s_{l,r}$ is the substring of $s$ from the $l$-th letter to the $r$-th one; 
-  otherwise, $C(a, b) = a + b$, i. e. the concatenation of two strings. 

Calculate $\sum\limits_{i=1}^n \sum\limits_{j=1}^n |C(s_i, s_j)|$.

## Input

The first line contains a single integer $n$ ($1 \le n \le 10^6$).

Each of the next $n$ lines contains a string $s_i$ ($1 \le |s_i| \le 10^6$), consisting of lowercase Latin letters.

The total length of the strings doesn't exceed $10^6$.

## Output

Print a single integer — $\sum\limits_{i=1}^n \sum\limits_{j=1}^n |C(s_i, s_j)|$.

## Examples

Input

> 3
> aba
> ab
> ba

Output

> 20

Input

> 5
> abab
> babx
> xab
> xba
> bab

Output

> 126

## 题解

注意到，$C(a,b)$ 去除的部分实际上就是 $a$ 的逆序串 $a'$ 和 $b$ 的最长公共前缀。如：
$$
\begin{align}
a&=\text{abdcc}\\
a'&=\underline{\text{ccd}}\text{ba}\\
b&=\underline{\text{ccd}}\text{aa}\\
\Rightarrow C(a,b)&=\text{abaa}
\end{align}
$$
我们将最长公共前缀记为 $\text{lcp}(\cdot)$，那么可以用公式表示：
$$
C(a,b)=|a|+|b|-2|\text{lcp}(a',b)|
$$
根据上面的式子，将问题变形：
$$
\sum_{i=1}^n \sum_{j=1}^n |C(s_i, s_j)|=2n\sum_{i=1}^{n}|s_i|-\sum_{i=1}^n \sum_{j=1}^n\text{lcp}(s_i',s_j)
$$
下面的重点就是求出 $\sum_{i=1}^n \sum_{j=1}^n\text{lcp}(s_i',s_j)$ 了。要求每一种组合的最长公共前缀，进行双循环的话，很明显复杂度不能接受。不过我们可以换一种思维方式，考虑每一种前缀对答案的贡献。对于 $s_i'$ 中的每一种前缀，如果也符合 $s_j$ 的前缀，那么答案就应该减去这种符合这种前缀的字符串数目。

[字典树](https://io.zouht.com/61.html) (Trie 树) 的原理就是将相同前缀的字符串压缩到一起储存，因此可以用它解决这个问题。但是直接使用是不行的，因为字典树维护的是完整字符串的数量，而不是字符串的前缀的数量。

不过这个问题我们简单修改字典树的代码即可，注意下面代码的增删之处（使用 `++` 和 `--` 标记）：

```cpp
  void insert(string &s)
  {
      int p = 0;
      for (int i = 0; i < s.size(); i++)
      {
          int c = s[i] - 'a';
          if (!son[p][c])
              son[p][c] = ++idx;
          p = son[p][c];
++        cnt[p]++;
      }
--    cnt[p]++;
  }
```

另外，由于我们减去的是字符串的每种前缀的数量，因此查询的代码也得进行修改：

```cpp
  int query(string &s)
  {
--    int p = 0;
++    int p = 0, res = 0;
      for (int i = 0; i < s.size(); i++)
      {
          int c = s[i] - 'a';
          if (!son[p][c])
--            return 0;
++            return res;
          p = son[p][c];
++        res += cnt[p];
      }
--    return cnt[p];
++    return res;
  }
```

## 代码

```cpp
#include <bits/stdc++.h>
#define endl '\n'
#define int long long

using namespace std;

const int MAXN = 1e6 + 10;
int son[MAXN][26], cnt[MAXN], idx;

void insert(string &s)
{
    int p = 0;
    for (int i = 0; i < s.size(); i++)
    {
        int c = s[i] - 'a';
        if (!son[p][c])
            son[p][c] = ++idx;
        p = son[p][c];
        cnt[p]++;
    }
}

int query(string &s)
{
    int p = 0, res = 0;
    for (int i = 0; i < s.size(); i++)
    {
        int c = s[i] - 'a';
        if (!son[p][c])
            return res;
        p = son[p][c];
        res += cnt[p];
    }
    return res;
}

void solve()
{
    int n;
    cin >> n;
    vector<string> s(n);
    int ans = 0;
    for (int i = 0; i < n; i++)
    {
        cin >> s[i];
        ans += s[i].size();
        insert(s[i]);
    }
    ans *= 2 * n;
    for (int i = 0; i < n; i++)
    {
        reverse(s[i].begin(), s[i].end());
        ans -= 2 * query(s[i]);
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

