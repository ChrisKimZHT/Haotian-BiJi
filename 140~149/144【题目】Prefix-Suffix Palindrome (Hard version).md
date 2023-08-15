**Codeforces Global Round 7**

D2. Prefix-Suffix Palindrome (Hard version)

https://codeforces.com/problemset/problem/1326/D2

<!--more-->

2 seconds / 256 megabytes

standard input / standard output

## Problem

This is the hard version of the problem. The difference is the constraint on the sum of lengths of strings and the number of test cases. You can make hacks only if you solve all versions of this task.

You are given a string $s$, consisting of lowercase English letters. Find the longest string, $t$, which satisfies the following conditions: 

-  The length of $t$ does not exceed the length of $s$. 
-  $t$ is a palindrome. 
-  There exists two strings $a$ and $b$ (possibly empty), such that $t = a + b$ ( "$+$" represents concatenation), and $a$ is prefix of $s$ while $b$ is suffix of $s$. 
## Input

The input consists of multiple test cases. The first line contains a single integer $t$ ($1 \leq t \leq 10^5$), the number of test cases. The next $t$ lines each describe a test case.

Each test case is a non-empty string $s$, consisting of lowercase English letters.

It is guaranteed that the sum of lengths of strings over all test cases does not exceed $10^6$.

## Output

For each test case, print the longest string which satisfies the conditions described above. If there exists multiple possible solutions, print any of them.

## Example

Input

> 5
> a
> abcdfdcecba
> abbaxyzyx
> codeforces
> acbba

Output

> a
> abcdfdcba
> xyzyx
> c
> abba

## Note

In the first test, the string $s = $"a" satisfies all conditions.

In the second test, the string "abcdfdcba" satisfies all conditions, because:

-  Its length is $9$, which does not exceed the length of the string $s$, which equals $11$. 
-  It is a palindrome. 
-  "abcdfdcba" $=$ "abcdfdc" $+$ "ba", and "abcdfdc" is a prefix of $s$ while "ba" is a suffix of $s$. 
It can be proven that there does not exist a longer string which satisfies the conditions.

In the fourth test, the string "c" is correct, because "c" $=$ "c" $+$ "" and $a$ or $b$ can be empty. The other possible solution for this test is "s".

## 题解

写出来这道题 Easy 版本的应该知道解题流程：

1. 从 $s$ 里找首尾相同的部分：$\underline{\text{abc}}\text{chriskimi}\underline{\text{cba}}$
2. 再考虑去掉首位相同部分的：$\text{chriskimi}$
   1. 找从左端点开始的最长回文串：$\underline{\text{c}}\text{hriskimi}$
   2. 找从右端点结束的最长回文串：$\text{chrisk}\underline{\text{imi}}$
   3. 取上面两者情况较长的部分：$\text{imi}$
3. 答案就是首位部分加中间的最长回文串：$\text{abcimicba}$

Easy 版本中，字符串长度为 $10^3$ 级别，因此第 $2$ 步可以用 $O(n^2)$ 的中心扩展法通过。

Hard 版本中，字符串长度为 $10^6$ 级别，因此第 $2$ 步必须使用 $O(n)$ 的算法解决。

[Manacher](https://io.zouht.com/143.html)算法可以在 $O(n)$ 时间内找到所有回文子串，那当然就能找到最大的从左端点或从右端点开始的回文子串，因此可以使用它解决。使用方法基本上就是套模板，再此不多解释。

又因为该题要找的是从左端点或右端点开始的回文子串，因此[字符串哈希](https://io.zouht.com/142.html)也能在 $O(n)$ 解决这个问题。解决方法也很简单粗暴，把第 $2$ 步的字符串正着跑一遍字符串哈希，再反着跑一遍字符串哈希，再比对正反字符串的对应子串哈希值是否相同，如果相同就是回文的：

- $\text{chrisk}\underline{\text{imi}}$
- $\underline{\text{imi}}\text{ksirhc}$

不过哈希的方法在 cf 场上很容易被 Hack，建议用双哈希增强。

## 代码

**马拉车法**

```cpp
#include <bits/stdc++.h>
#define endl '\n'
// #define int long long

using namespace std;

string pre_process(string &s)
{
    string ans = "^";
    for (auto &c : s)
    {
        ans += '#';
        ans += c;
    }
    ans += '#';
    ans += '$';
    return ans;
}

void manacher(string &s, vector<int> &p)
{
    int r = 0, mid = 0;
    for (int i = 1; i < s.size() - 1; i++)
    {
        p[i] = r > i ? min(p[2 * mid - i], r - i) : 1;
        while (s[i + p[i]] == s[i - p[i]])
            p[i]++;
        if (i + p[i] > r)
        {
            r = i + p[i];
            mid = i;
        }
    }
}

string filter_str(string s)
{
    string ans;
    for (int i = 0; i < s.size(); i++)
        if (s[i] != '#' && s[i] != '^' && s[i] != '$')
            ans += s[i];
    return ans;
}

void solve()
{
    string s;
    cin >> s;
    int l = 0, r = s.size() - 1;
    for (; l < r; l++, r--)
        if (s[l] != s[r])
            break;
    string pre = s.substr(0, l);
    string suf = s.substr(r + 1);
    string str = s.substr(l, r - l + 1);
    string strnew = pre_process(str);
    vector<int> p(strnew.size() + 10);
    manacher(strnew, p);
    int pos_a = 0;
    for (int i = 1; i < strnew.size() - 1; i++)
        if (i - p[i] + 1 == 1)
            pos_a = i;
    string mid_a = filter_str(strnew.substr(0, 2 * pos_a));
    int pos_b = 0;
    for (int i = strnew.size() - 2; i >= 1; i--)
        if (i + p[i] == strnew.size() - 1)
            pos_b = i;
    string mid_b = filter_str(strnew.substr(pos_b - p[pos_b] + 1));
    cout << pre << (mid_a.size() > mid_b.size() ? mid_a : mid_b) << suf << endl;
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

**哈希法**

```cpp
#include <bits/stdc++.h>
#define endl '\n'
// #define int long long

using namespace std;

typedef unsigned long long ull;
constexpr ull base = 449;
constexpr ull mod = 436522843;

void solve()
{
    string s;
    cin >> s;
    int n = s.size();
    int same_pos = -1;
    for (int i = 0, j = n - 1; i < j && s[i] == s[j]; i++, j--)
        same_pos = i;
    string pre = s.substr(0, same_pos + 1);
    string suf = s.substr(n - same_pos - 1);
    // awa = s remove prefix and suffix
    // bwb = reversed awa
    string awa = s.substr(same_pos + 1, n - 2 * (same_pos + 1));
    string bwb = awa;
    reverse(bwb.begin(), bwb.end());
    int cn = awa.size();
    vector<ull> p(cn + 10), ha(cn + 10), hb(cn + 10);
    p[0] = 1;
    for (int i = 1; i <= cn; i++)
    {
        p[i] = p[i - 1] * base % mod;
        ha[i] = (ha[i - 1] * base % mod + awa[i - 1]) % mod;
        hb[i] = (hb[i - 1] * base % mod + bwb[i - 1]) % mod;
    }
    auto get_a = [&](int l, int r) -> ull
    {
        return (ha[r + 1] - ha[l] * p[r - l + 1] % mod + mod) % mod;
    };
    auto get_b = [&](int l, int r) -> ull
    {
        return (hb[r + 1] - hb[l] * p[r - l + 1] % mod + mod) % mod;
    };
    // from left
    int cnt_a = 0;
    for (int i = 0; i < cn; i++)
        if (get_a(0, i) == get_b(cn - 1 - i, cn - 1))
            cnt_a = i + 1;
    // from right
    int cnt_b = 0;
    for (int i = 0; i < cn; i++)
        if (get_a(cn - 1 - i, cn - 1) == get_b(0, i))
            cnt_b = i + 1;
    string mid = "";
    if (cnt_a > cnt_b)
        mid = awa.substr(0, cnt_a);
    else
        mid = awa.substr(cn - cnt_b);
    cout << pre + mid + suf << endl;
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

