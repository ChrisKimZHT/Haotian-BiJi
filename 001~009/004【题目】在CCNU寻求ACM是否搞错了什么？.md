**“菜鸟杯”华中师范大学程序设计新生赛**

G - 在CCNU寻求ACM是否搞错了什么？

https://ac.nowcoder.com/acm/contest/26134/G

<!--more-->

时间限制：C/C++ 1秒，其他语言2秒
空间限制：C/C++ 262144K，其他语言524288K
64bit IO Format: %lld

## 题目描述：

听说在皮尔特沃夫的塔里斯议员研究出了稳定的海克斯水晶，在 CCNU 魔法学院学习 ACM 魔法的 HJ 想要学习这种技术，于是，HJ去祖安进购了一批魔法水晶。

有趣的是，这一批水晶的数量有 $n * m(2\leq n, m \leq 10^4)$个，并且第 $i$ 个水晶的能量恰好为 $i$，为了妥善保存，HJ 把这些水晶依次放在了一个 $n$ 行 $m$ 列的格子里。 

在研究的过程中，她发现了一件很神奇的事情，如果把某一个水晶打碎，它会释放与它**相邻[1]未被打碎水晶**的能量之和的能量（不包括自己本身）。

 于是HJ想知道怎样把这 $n * m$ 个魔法水晶重新摆放，使得按照最优策略打碎所有水晶得到的总能量值最大，HJ完全无法解答这个问题，她只能求助于你。

 签订了契约成为魔法少女的你，一直想要结束这个圆环之理和焰魔共存的不稳定平衡下的世界，你需要强大的能量，因此请你帮HJ构造出一种对水晶的**摆放方案[2]**使得按照最优策略打碎所有水晶产生的总能量在所有摆放方案中是最大的，最后**只用**告诉她这个总能量是多少。

 可以参考**样例解释**食用题面。

 [1] 相邻： 指上下左右相邻，具体来说，对于两个坐标为[x, y], [i, j]的格子，两个格子相邻指的是 $(x=i \cap y=j\pm 1)\cup(x=i \pm 1 \cap y=j)$。

 [2] 摆放方案：指对于一个从1到 $n*m$ 的排列 $p_1, p_2...p_{n*m-1},p_{n*m}$，把每个格子依次放上第$p_i$个水晶。

## 输入描述：

> 第一行一个整数 $T$，表示有 $T$ 组数据
>
> 接下来每行输入包含两个整数 $n$，$m$，分别表示行和列的大小
>
> $2 \leq n,m \leq 10^4$
>
> $1 \leq T \leq 10^6$
>
> **注意**：由于本题读入输出数据量较大，请使用较快的读入方式，例如scanf，printf。

## 输出描述：

> 输出包含 $T$ 行，每一行为该组数据对应的答案。

## 示例1：

### 输入

> 2
> 2 2
> 2 3

### 输出

> 14
> 36

### 说明

![说明](https://assets.zouht.com/img/io/4-01.webp)

## 我的笔记：

### 分析

这个题本质上就是道数学题，答案完全可以用一条算式表达出来，我在看前面大佬的提交的时候也真的碰见了直接 printf 一个超超超长算式的写法。所以这道题的核心就是怎么把这个通式搞出来。

**哪些水晶能贡献能量？**

经过简单思考，就像国际象棋棋盘一样，黑色格子能贡献能量，白色格子贡献为0。为了让能量最大化，当然要把大数放到能贡献能量的区域。就比如下图，我们把 36~19 放到黑色格子内，把 1~18 放到白色格子里。

<img src="https://assets.zouht.com/img/io/4-02.webp" style="zoom:40%;" />



**贡献的能量次数一样吗？**

我们可以发现，不同位置的黑色格子，贡献能量的次数是不同的，比如中间的黑色格子与上下左右四个格子相邻，即会贡献四次能量。最后黑色格子可以分成三组，即角上贡献2次，边上贡献3次，内部贡献4次。

<img src="https://assets.zouht.com/img/io/4-03.webp" style="zoom:40%;" />

**各种格子有几个？**

需要分类讨论：

一、$n$ 和 $m$ 均为偶数（对应图就在上面）

贡献 4：$\frac{(n-2)(m-2)}{2}$

贡献 3：$n+m-4$

贡献 2：$2$

二、$n$ 与 $m$ 中一奇一偶

<img src="https://assets.zouht.com/img/io/4-04.webp" style="zoom:40%;" />

可以发现结果其实和上面一种情况是一样的，于是与上面情况合并。

三、$n$ 和 $m$ 均为奇数

这种情况内还分两种填充方式，并不知道那种填充方式能量更大，因此两种都得算，然后取大值。(下面的结果包含向下和向上取整，代码实现方面，整型数本身就可以向下取整，向上取整的话+1即可)

情况 1

<img src="https://assets.zouht.com/img/io/4-05.webp" style="zoom:40%;" />

贡献 4：$\lceil\frac{(n-2)(m-2)}{2}\rceil$

贡献 3：$2*(\lfloor\frac{n}{2}\rfloor+\lfloor\frac{m}{2}\rfloor)-4$

贡献 2：$4$



情况 2

<img src="https://assets.zouht.com/img/io/4-06.webp" style="zoom:40%;" />

贡献 4：$\lfloor\frac{(n-2)(m-2)}{2}\rfloor$

贡献 3：$2*(\lfloor\frac{n}{2}\rfloor+\lfloor\frac{m}{2}\rfloor)$

贡献 2：$0$

**求结果**

各种格子对结果的贡献不同，那么当然是把能量大的放到4号格子，较大的放到 3 号格子，第三大的放到二号格子，然后求和即可。我用的是等差数列求和公式。max 是最大的数，besidex 是与 x 个格子相邻的格子数目。

```
ans = 4 * (max + (max - beside4 + 1)) * beside4 / 2 +
      3 * ((max - beside4) + (max - beside4 - beside3 + 1)) * beside3 / 2 +
      2 * ((max - beside4 - beside3) + (max - beside4 - beside3 - beside2 + 1)) * beside2 / 2;
```

### 代码

```cpp
#include <bits/stdc++.h>

using namespace std;

int main(void)
{
	int T;
	cin >> T;
	while (T--)
	{
		int n, m;
		scanf("%d%d", &n, &m);
		long long max = m * n;
		long long ans = 0;
		int beside2, beside3, beside4;
		if (n % 2 == 0 || m % 2 == 0)
		{
			beside4 = (n - 2) * (m - 2) / 2;
			beside3 = n + m - 4;
			beside2 = 2;
			ans = 4 * (max + (max - beside4 + 1)) * beside4 / 2 +
				  3 * ((max - beside4) + (max - beside4 - beside3 + 1)) * beside3 / 2 +
				  2 * ((max - beside4 - beside3) + (max - beside4 - beside3 - beside2 + 1)) * beside2 / 2;
		}
		else if (n % 2 != 0 && m % 2 != 0)
		{
			long long anstmp1 = 0, anstmp2 = 0;
			beside4 = (n - 2) * (m - 2) / 2 + 1;
			beside3 = 2 * (n / 2 + m / 2) - 4;
			beside2 = 4;
			anstmp1 = 4 * (max + (max - beside4 + 1)) * beside4 / 2 +
					  3 * ((max - beside4) + (max - beside4 - beside3 + 1)) * beside3 / 2 +
					  2 * ((max - beside4 - beside3) + (max - beside4 - beside3 - beside2 + 1)) * beside2 / 2;
			beside4 = (n - 2) * (m - 2) / 2;
			beside3 = 2 * (n / 2 + m / 2);
			beside2 = 0;
			anstmp2 = 4 * (max + (max - beside4 + 1)) * beside4 / 2 +
					  3 * ((max - beside4) + (max - beside4 - beside3 + 1)) * beside3 / 2 +
					  2 * ((max - beside4 - beside3) + (max - beside4 - beside3 - beside2 + 1)) * beside2 / 2;
			ans = anstmp1 > anstmp2 ? anstmp1 : anstmp2;
		}
		printf("%lld\n", ans);
	}
	return 0;
}
```

