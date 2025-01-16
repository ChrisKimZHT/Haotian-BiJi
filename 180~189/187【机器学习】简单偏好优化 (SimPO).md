**简单偏好优化** (Simple Preference optimization, SimPO)：大语言模型强化学习中的一种偏好优化方法，其对齐了<u>偏好优化目标中的奖励函数</u>与<u>生成指标</u>，同时<u>解放了参考模型</u>，相比 DPO 更简单、稳定与高效。

<!--more-->

论文：https://arxiv.org/abs/2405.14734

# 1 DPO 的问题

回顾 DPO，它的目标函数是：
$$
\mathcal{L}_{\text{DPO}}(\pi_\theta;\pi_\mathrm{ref})=-\mathbb{E}_{(x,y_w,y_l)\sim\mathcal{D}}\left[\log\sigma(\beta\log\frac{\pi_{\theta}(y_w\mid x)}{\pi_{\mathrm{ref}}(y_w\mid x)}-\beta\log\frac{\pi_{\theta}(y_l\mid x)}{\pi_{\mathrm{ref}}(y_l\mid x)})\right]
$$
它的奖励函数是：
$$
r(x,y)=\beta\log\frac{\pi_{r}(y\mid x)}{\pi_{\mathrm{ref}}(y\mid x)}+\beta\log Z(x)\\
\text{其中,}\;Z(x)=\sum_{y}\pi_{\mathrm{ref}}(y\mid x)\exp\left({\frac{1}{\beta}r(x,y)}\right)
$$
可以发现 DPO 有以下的问题：

1. DPO 在训练时需要参考模型 $\pi_{\mathrm{ref}}$，这带来了额外的内存和计算开销。
2. 由于推理时没有 $\pi_{\mathrm{ref}}$，DPO 偏好优化时的奖励函数和推理时的生成指标不一致。

第一点是显而易见的，下面展开分析一下第二点。

在生成阶段，策略模型 $\pi_{\theta}$（也就是 LLM）会生成一个最大的平均对数似然序列：
$$
p_{\theta}(y\mid x)=\frac{1}{|y|}\log\pi_{\theta}(y\mid x)=\frac{1}{|y|}\sum_{i=1}^{|y|}\log\pi_{\theta}(y_i\mid x,y_{<i})
$$

找到这个平均对数似然序列的最优解是非常昂贵的，因此在实际中是通过各种解码策略找到这个序列的局部最优解。这些解码策略在“[语言模型解码算法](https://io.zouht.com/183.html)”这篇笔记中有详细列出，可以跳转查看。

可以看到，在生成阶段，是没有模型 $\pi_{\mathrm{ref}}$ 进行参考的，同时它的形式也完全与训练时完全不同。这就代表对于一个三元组 $(x,y_w,y_l)$，满足 $r(x,y_w)>r(x,y_l)$ 这个条件并不一定满足生成时 $p_{\theta}(y_w\mid x)>p_{\theta}(y_l\mid x)$. 根据研究者的实验，使用 DPO 训练时只有约 $50\%$ 的三元组满足这个条件，这个情况是很糟糕的。

SimPO 便是针对 DPO 的这两个问题进行优化，其对齐了偏好优化目标中的奖励函数与生成指标，同时无需参考模型。

# 2 SimPO 方法

## 2.1 长度归一化奖励公式

为了对齐训练和推理，同时消去参考模型，SimPO 的想法很简单，直接拿生成时的指标来做训练时的奖励函数不就行了：
$$
r_{\mathrm{SimPO}}(x,y)=\frac{\beta}{|y|}\log\pi_{\theta}(y\mid x)=\frac{\beta}{|y|}\sum_{i=1}^{|y|}\log\pi_{\theta}(y_i\mid x,y_{<i})
$$
同时，需要注意长度归一化项是关键的。若没有该归一化项，长的序列往往时会有更低的对数似然值的，这就会导致模型在处理较长序列时必须额外拉高概率来弥补长度带来的损失，可能导致模型效果的降低。

## 2.2 目标奖励差额

SimPO 还引入了目标奖励差额 (Target reward margin) 概念：
$$
p(y_w\succ y_l\mid x)=\sigma(r(x,y_w)-r(x,y_l)-\gamma)
$$
其中，$\gamma>0$.

这个变动造成的影响比较好理解，就是获胜的奖励 $r(x,y_w)$ 必须比失败的奖励 $r(x,y_l)$ 大至少 $\gamma$.

## 2.3 目标函数

综上，结合 2.1, 2.2，SimPO 最终的目标函数便是：
$$
\mathcal{L}_{\text{DPO}}(\pi_\theta)=-\mathbb{E}_{(x,y_w,y_l)\sim\mathcal{D}}\left[\log\sigma\left(\frac{\beta}{|y_w|}\log\pi_{\theta}(y_w\mid x)-\frac{\beta}{|y_l|}\log\pi_{\theta}(y_l\mid x)-\gamma\right)\right]
$$
