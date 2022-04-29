**AtCoder Beginner Contest 236**

D - Dance

https://atcoder.jp/contests/abc236/tasks/abc236_d

<!--more-->

Time Limit: 2 sec / Memory Limit: 1024 MB

Score : $400$ points

### Problem Statement

$2N$ people numbered $1, 2, \ldots, 2N$ attend a ball. They will group into $N$ pairs and have a dance.

If Person $i$ and Person $j$ pair up, where $i$ is smaller than $j$, the *affinity* of that pair is $A_{i, j}$.
If the $N$ pairs have the affinity of $B_1, B_2, \ldots, B_N$, the *total fun of the ball* is the bitwise XOR of them: $B_1 \oplus B_2 \oplus \cdots \oplus B_N$​.

Print the maximum possible total fun of the ball when the $2N$ people can freely group into $N$ pairs.

### Constraints

- $1 \leq N \leq 8$
- $0 \leq A_{i, j} < 2^{30}$
- All values in input are integers.

------

### Input

Input is given from Standard Input in the following format:

> $N$
> $A_{1, 2}$    $A_{1, 3}$    $A_{1, 4}$    $\cdots$    $A_{1, 2N}$
> $A_{2,3}$    $A_{2, 4}$    $\cdots$    $A_{2, 2N}$
> $A_{3, 4}$    $\cdots$    $A_{3, 2N}$
> $\vdots$
> $A_{2N-1, 2N}$

### Output

Print the maximum possible total fun of the ball.

------

### Sample Input 1

> 2
> 4 0 1
> 5 3
> 2

### Sample Output 1

> 6

Let $\lbrace i, j\rbrace$ denote a pair of Person $i$ and Person $j$. There are three ways for the four people to group into two pairs, as follows.

- Group into $\lbrace 1, 2\rbrace, \lbrace 3, 4\rbrace$. The total fun of the ball here is $A_{1, 2} \oplus A_{3, 4} = 4 \oplus 2 = 6$.
- Group into $\lbrace 1, 3\rbrace, \lbrace 2, 4\rbrace$. The total fun of the ball here is $A_{1, 3} \oplus A_{2, 4} = 0 \oplus 3 = 3$.
- Group into $\lbrace 1, 4\rbrace, \lbrace 2, 3\rbrace$. The total fun of the ball here is $A_{1, 4} \oplus A_{2, 3} = 1 \oplus 5 = 4$.

Therefore, the maximum possible total fun of the ball is $6$.

------

### Sample Input 2

> 1
> 5

### Sample Output 2

> 5

There will be just a pair of Person $1$ and Person $2$, where the total fun of the ball is $5$.

------

### Sample Input 3

> 5
> 900606388 317329110 665451442 1045743214 260775845 726039763 57365372 741277060 944347467
> 369646735 642395945 599952146 86221147 523579390 591944369 911198494 695097136
> 138172503 571268336 111747377 595746631 934427285 840101927 757856472
> 655483844 580613112 445614713 607825444 252585196 725229185
> 827291247 105489451 58628521 1032791417 152042357
> 919691140 703307785 100772330 370415195
> 666350287 691977663 987658020
> 1039679956 218233643
> 70938785

### Sample Output 3

> 1073289207

### 我的笔记

本题在做的时候，确实想到的是 DFS 遍历，但因为没有仔细思考时间复杂度到底是多少，导致选用了错误的方法最后TLE。

**错误思路**

题目给出了每两个人之间的喜好值，于是我就直来直去，想着直接用喜好值 DFS，选了 $A_{i,j}$ 后就再不选包含 $i$ 和 $j$ 的，并且在每层 DFS 都进行了异或操作，导致耗时非常大，只过不到一半的测试点。

**正确思路**

首先考虑最大情况一共有 16 个人分 8 组，我们可以一个个遍历，第一组第一个有 16 种选法，第一组第二个有 15 种选法，第二组第一个有 14 种选法……时间复杂度为 $O(16!)$，极其巨大，必 T 无疑。

但是这种选法有非常多重复的情况，例如：$\{1,2\}$ 和 $\{2,1\}$ 被统计成不同选法，并且 $\{1,2\}$ 这一组在全部八组中的位置不同，也被统计成了不同选法。

为了规避这种重复，我们就要有序地进行选择，让每一组的第一个从小到大排列，例如 $\big\{\{1,3\},\{2,8\},\{4,6\},\{5,7\}\big\}$，每组第一个人 1、2、4、5 呈升序排列，这样可以保证不重不漏。

实现这种顺序的方法就是，每组第一个人选择最小的未选择的那个。这样时间复杂度为 $O(15×13×11×9×7×5×3×1)$，在 2e6 级别，满足题目限制。

还有一点是，通过一个 vector 存储选择的方式，在 DFS 完成时再进行异或运算得出结果，免去了每一步都进行异或操作，节省时间。

### 源码

```cpp
#include <bits/stdc++.h>

using namespace std;

int N;
int A[20][20];
bool vis[20];
int ans = 0;
vector<pair<int, int>> v;
void dfs()
{
	if (v.size() == N)
	{
		int ret = 0;
		for (auto it = v.begin(); it != v.end(); ++it)
		{
			ret ^= A[it->first][it->second];
		}
		ans = max(ret, ans);
		return;
	}
	int a;
	for (int i = 1; i <= 2 * N; i++)
	{
		if (!vis[i])
		{
			a = i;
			vis[a] = true;
			break;
		}
	}
	for (int i = 1; i <= 2 * N; i++)
	{
		if (!vis[i])
		{
			vis[i] = true;
			v.push_back({a, i});
			dfs();
			vis[i] = false;
			v.pop_back();
		}
	}
	vis[a] = false;
}

int main(void)
{
	ios::sync_with_stdio(false);
	cin >> N;
	for (int i = 1; i <= 2 * N - 1; i++)
	{
		for (int j = i + 1; j <= 2 * N; j++)
		{
			cin >> A[i][j];
		}
	}
	dfs();
	cout << ans << endl;
	return 0;
}
```



