**去噪扩散概率模型** (Denoising Diffusion Probabilistic Models, DDPM)：一种通过学习“如何将纯噪声一步步还原成清晰数据”来生成高质量图像、音频或其他内容的深度学习生成模型。

<!--more-->

原始论文：[Denoising Diffusion Probabilistic Models](https://arxiv.org/abs/2006.11239)

# 1 概览

首先从模型名称“去噪扩散概率模型”来理解该模型在做什么：

- **去噪** (Denoising)：这是模型的核心工作方式，模型通过学习如何从包含噪声的图片中还原出清晰的图片。
- **扩散** (Diffusion)：这是一个物理学概念，在模型中，它指代将数据（如图像）逐渐转变为纯噪声的过程。
- **概率** (Probabilistic)：模型并不是进行简单的数学映射，而是基于概率分布进行推演。
- **模型** (Models)

<img src="https://assets.zouht.com/img/note/194-01.webp" style="zoom:50%;" />

因此，该模型最核心的两个步骤便是：

- **前向过程**（扩散）：向原始图像中分步添加高斯噪声，直到图像变成完全无法分辨的随机噪声。上图从右向左的过程就是前向过程，清晰的原图片 $\boldsymbol{\mathrm{x}}_{0}$ 不断添加噪声，变成随机噪声 $\boldsymbol{\mathrm{x}}_T$。
- **反向过程**（去噪）：这是 DDPM 的核心，模型学习如何从纯噪声中一步步预测并剔除噪声，最终生成一张全新的、清晰的图像。上图从左向右的过程便是反向过程，一步步从纯噪声 $\boldsymbol{\mathrm{x}}_T$ 还原为原图 $\boldsymbol{\mathrm{x}}_{0}$.

在训练时，DDPM 从训练数据中学习到图片的分布；在推理时，DDPM 从高斯分布的噪声图像开始剔除噪声，生成一张与训练数据同分布的图片。由于推理时的初始高斯分布是随机的，因此每次推理时模型都会收敛到数据分布中的一个不同的点，即生成出原始训练数据中不存在的新图片。

# 2 前向过程

前向过程的任务是向图片里添加高斯噪声，这个操作在论文中的记法是从一个均值为 $\boldsymbol{\mu}$，协方差矩阵 $\boldsymbol{\Sigma}$ 的高斯分布中采样得到图片 $\boldsymbol{\mathrm{x}}$，即 $\mathcal{N}(\boldsymbol{\mathrm{x}};\boldsymbol{\mu},\boldsymbol{\Sigma})$.

在理解图片的分布时，需要将图片视作一个数值矩阵，均值便是每一个像素的分布中心（期望值），方差便是该像素的噪声强度。更浅显地来说，均值就是这次采样的底图，是输入的一张确定的图片，而方差决定了向底图中加入进去的高斯噪声的强度。

首先看到单独的一步，从 $\boldsymbol{\mathrm{x}}_{t-1}$ 的图片采样得到 $\boldsymbol{\mathrm{x}}_{t}$ 的过程：
$$
q(\mathbf{x}_t|\mathbf{x}_{t-1}) := \mathcal{N}(\mathbf{x}_t; \sqrt{1-\beta_t}\mathbf{x}_{t-1}, \beta_t\mathbf{I})
$$
实际上也可以将这个采样的过程转为我们朴素理解的“向图片里加噪”的过程，得到实际的计算方式：
$$
\boldsymbol{\mathrm{x}}_{t}=\sqrt{1-\beta_t}\mathbf{x}_{t-1}+\sqrt{\beta_t}\boldsymbol{\epsilon}_{t-1}\\
其中\boldsymbol{\epsilon}_{t-1}\sim\mathcal{N}(\boldsymbol{0},\boldsymbol{\mathrm{I}})
$$
用自然语言解释便是将原图 $\boldsymbol{\mathrm{x}}_{t-1}$ 衰减 $\sqrt{1-\beta_t}$ 后作为底图，向其中添加强度为 $\beta_t$ 的高斯噪声，得到加噪后的图片 $\boldsymbol{\mathrm{x}}_{t}$.

将单步重复 $T$ 次便是完整的前向过程：
$$
q(\mathbf{x}_{1:T}|\mathbf{x}_0) := \prod_{t=1}^T q(\mathbf{x}_t|\mathbf{x}_{t-1})
$$
那么整个前向过程的计算方式便是：
$$
\begin{align}
\boldsymbol{\mathrm{x}}_{t}&=\sqrt{1-\beta_t}\mathbf{x}_{t-1}+\sqrt{\beta_t}\boldsymbol{\epsilon}_{t-1}\\
&=\sqrt{1-\beta_t}\left(\sqrt{1-\beta_{t-1}}\mathbf{x}_{t-2}+\sqrt{\beta_{t-1}}\boldsymbol{\epsilon}_{t-2}\right)+\sqrt{\beta_t}\boldsymbol{\epsilon}_{t-1}\\
&=\sqrt{(1-\beta_t)(1-\beta_{t-1})}\mathbf{x}_{t-2}+\sqrt{(1-\beta_t)\beta_{t-1}}\boldsymbol{\epsilon}_{t-2}+\sqrt{\beta_t}\boldsymbol{\epsilon}_{t-1}\\
&=\sqrt{(1-\beta_t)(1-\beta_{t-1})}\mathbf{x}_{t-2}+\sqrt{(1-\beta_t)\beta_{t-1}+\beta_t}\boldsymbol{\epsilon}\\
&=\sqrt{(1-\beta_t)(1-\beta_{t-1})}\mathbf{x}_{t-2}+\sqrt{1-(1-\beta_t)(1-\beta_{t-1})}\boldsymbol{\epsilon}\\
&=\sqrt{(1-\beta_t)(1-\beta_{t-1})\cdots(1-\beta_1)}\mathbf{x}_{0}+\sqrt{1-(1-\beta_t)(1-\beta_{t-1})\cdots(1-\beta_1)}\boldsymbol{\epsilon}
\end{align}
$$
需要注意，这里用到了正态分布的相加性质：
$$
\mathcal{N}(0,\sigma^2_1\boldsymbol{\mathrm{I}})+\mathcal{N}(0,\sigma^2_2\boldsymbol{\mathrm{I}})=\mathcal{N}(0,(\sigma^2_1+\sigma^2_2)\boldsymbol{\mathrm{I}})
$$
为了方便起见，记 $\alpha_t:=1-\beta_t$，那么原式便转换为：
$$
\begin{align}
\boldsymbol{\mathrm{x}}_{t}&=\sqrt{\alpha_t\alpha_{t-1}\cdots\alpha_1}\mathbf{x}_{0}+\sqrt{1-\alpha_t\alpha_{t-1}\cdots\alpha_1}\boldsymbol{\epsilon}
\end{align}
$$
再记 $\bar\alpha_t=\prod_{i=1}^{t}\alpha_i$，原式转换为：
$$
\begin{align}
\boldsymbol{\mathrm{x}}_{t}&=\sqrt{\bar\alpha_t}\mathbf{x}_{0}+\sqrt{1-\bar\alpha_t}\boldsymbol{\epsilon}
\end{align}
$$
使用概率分布形式的记法，即为：
$$
q(\mathbf{x}_t|\mathbf{x}_0) = \mathcal{N}(\mathbf{x}_t; \sqrt{\bar{\alpha}_t}\mathbf{x}_0, (1-\bar{\alpha}_t)\mathbf{I})
$$
上面的推导给出了前向过程的具体计算方式，同时说明了即使理论上前向过程需要重复 $T$ 次，但由于高斯噪声的合并性质，前向过程中任意步数的图片状态都只需要一次计算就能获得。

原论文中选择 $T=1000$，$\beta$ 为固定为常数，从 $\beta_1=10^{-4}$ 到 $\beta_T=0.02$ 进行线性增长。代回上面的公式便是随着步数 $i$ 增加，$\beta_i$ 线性变大，$\alpha_i$ 线性变小，$\bar\alpha_i$ 指数变小，图片变模糊的速度随步数 $i$ 越来越快，最终 $\bar\alpha_i$ 几乎为 $0$，图片近似为完全随机的高斯噪声 $\mathcal{N}(\boldsymbol{0},\boldsymbol{\mathrm{I}})$.

# 3 反向过程

对于反向过程，我们希望得到一个模型，倒过来剔除在正向过程中添加的高斯噪声，这样当需要生成图片时，我们就可以利用这个模型从标准高斯噪声开始，一步步剔除噪声，最终获得一个训练数据分布中的新图片。

正向过程定义为在一个高斯分布中采样得到新突破，反向过程也可以这样定义：
$$
p_\theta(\mathbf{x}_{t-1}|\mathbf{x}_t) := \mathcal{N}(\mathbf{x}_{t-1}; \mu_\theta(\mathbf{x}_t, t), \Sigma_\theta(\mathbf{x}_t, t))
$$
唯一的区别就是，对于反向过程我们没有办法直接求得分布的均值和协方差，因此只能使用神经网络来拟合它。即我们使用参数为 $\theta$ 的神经网络，基于当前步的图片 $\mathbf{x}_t$ 和当前的步数 $t$，拟合得到分布的均值 $\mu_\theta(\mathbf{x}_t, t)$ 和协方差 $\Sigma_\theta(\mathbf{x}_t, t)$，然后在其中采样得到预测出的上一步的图片 $\mathbf{x}_{t-1}$.

整个反向过程便是：
$$
p_\theta(\mathbf{x}_{0:T}) := p(\mathbf{x}_T) \prod_{t=1}^T p_\theta(\mathbf{x}_{t-1}|\mathbf{x}_t)
$$

# 4 损失函数

最朴素的想法就是，直接计算模型 $\theta$ 凭空生成图像 $\mathbf{x}_0$ 的概率，写成负对数形式如下：
$$
\mathbb{E}[-\log p_\theta(\mathbf{x}_0)]
$$
对于 $p_\theta(\mathbf{x}_0)$，需要计算所有的从纯噪声 $\mathbf{x}_T$ 去噪到 $\mathbf{x}_0$ 的路径得到，但这些路径的数量是天文数字，因此该形式的损失是不可解的。

为了解决这个问题，前向的加噪过程便起到了作用，通过计算加噪过程 $q(\mathbf{x}_{1:T}|\mathbf{x}_0)$ 和神经网络降噪过程 $p_\theta(\mathbf{x}_{0:T})$ 之间的相对关系，构建出损失：
$$
\mathbb{E}_q \left[ -\log \frac{p_\theta(\mathbf{x}_{0:T})}{q(\mathbf{x}_{1:T}|\mathbf{x}_0)} \right]
$$
该损失是原损失的上界，因此通过优化该损失值，一定程度上就等于优化原损失值：
$$
\mathbb{E}\left[-\log p_\theta\left(\mathbf{x}_0\right)\right] \leq \mathbb{E}_q\left[-\log \frac{p_\theta\left(\mathbf{x}_{0: T}\right)}{q\left(\mathbf{x}_{1: T} \mid \mathbf{x}_0\right)}\right]=\mathbb{E}_q\left[-\log p\left(\mathbf{x}_T\right)-\sum_{t\geq1} \log \frac{p_\theta\left(\mathbf{x}_{t-1} \mid \mathbf{x}_t\right)}{q\left(\mathbf{x}_t \mid \mathbf{x}_{t-1}\right)}\right]=: L
$$
直接计算该损失会导致梯度极不稳定，因为前向单步概率 $q(\mathbf{x}_t|\mathbf{x}_{t-1})$ 没有直接用到原图 $\mathbf{x}_0$ 的信息。为了解决这个问题，需要对原始损失值进行变形，向 $q(\mathbf{x}_t|\mathbf{x}_{t-1})$ 引入 $\mathbf{x}_0$：

$$
q(\mathbf{x}_t|\mathbf{x}_{t-1}) = \frac{q(\mathbf{x}_{t-1}|\mathbf{x}_t, \mathbf{x}_0) q(\mathbf{x}_t|\mathbf{x}_0)}{q(\mathbf{x}_{t-1}|\mathbf{x}_0)}
$$

> $q(\mathbf{x}_t, \mathbf{x}_{t-1} | \mathbf{x}_0)$ 有两种链式展开方式：
>
> - $q(\mathbf{x}_t, \mathbf{x}_{t-1} | \mathbf{x}_0) = q(\mathbf{x}_t | \mathbf{x}_{t-1}, \mathbf{x}_0) q(\mathbf{x}_{t-1} | \mathbf{x}_0)$
> - $q(\mathbf{x}_t, \mathbf{x}_{t-1} | \mathbf{x}_0) = q(\mathbf{x}_{t-1} | \mathbf{x}_t, \mathbf{x}_0) q(\mathbf{x}_t | \mathbf{x}_0)$
>
> 因此得到：
>
> $$
> q(\mathbf{x}_t | \mathbf{x}_{t-1}, \mathbf{x}_0) q(\mathbf{x}_{t-1} | \mathbf{x}_0) = q(\mathbf{x}_{t-1} | \mathbf{x}_t, \mathbf{x}_0) q(\mathbf{x}_t | \mathbf{x}_0)
> $$
> 同时需要注意到，前向加噪过程 $q$ 是一个马尔科夫链，$t$ 步状态仅与 $t-1$ 步的状态有关，因此 $q(\mathbf{x}_t | \mathbf{x}_{t-1}, \mathbf{x}_0)$ 中的 $\mathbf{x}_0$ 是不影响结果的：
> $$
> q(\mathbf{x}_t | \mathbf{x}_{t-1}, \mathbf{x}_0)=q(\mathbf{x}_t | \mathbf{x}_{t-1})
> $$
> 综上：
> $$
> q(\mathbf{x}_t | \mathbf{x}_{t-1}) q(\mathbf{x}_{t-1} | \mathbf{x}_0) = q(\mathbf{x}_{t-1} | \mathbf{x}_t, \mathbf{x}_0) q(\mathbf{x}_t | \mathbf{x}_0)\\
> \Rightarrow q(\mathbf{x}_t|\mathbf{x}_{t-1}) = \frac{q(\mathbf{x}_{t-1}|\mathbf{x}_t, \mathbf{x}_0) q(\mathbf{x}_t|\mathbf{x}_0)}{q(\mathbf{x}_{t-1}|\mathbf{x}_0)}
> $$

于是，损失函数里的 $\log$ 分式即可转换为：
$$
\begin{align}
\log \frac{p_\theta\left(\mathbf{x}_{t-1} \mid \mathbf{x}_t\right)}{q\left(\mathbf{x}_t \mid \mathbf{x}_{t-1}\right)}&=\log \frac{p_\theta\left(\mathbf{x}_{t-1} \mid \mathbf{x}_t\right)q(\mathbf{x}_{t-1}|\mathbf{x}_0)}{q(\mathbf{x}_{t-1}|\mathbf{x}_t, \mathbf{x}_0) q(\mathbf{x}_t|\mathbf{x}_0)}\\
&=\log \frac{p_\theta\left(\mathbf{x}_{t-1} \mid \mathbf{x}_t\right)}{q(\mathbf{x}_{t-1}|\mathbf{x}_t, \mathbf{x}_0) }+\log \frac{q(\mathbf{x}_{t-1}|\mathbf{x}_0)}{q(\mathbf{x}_t|\mathbf{x}_0)}\\
\end{align}
$$
于是，原来的求和结果便是：
$$
\begin{align}
&\sum_{t=1}^{T} \log \frac{p_\theta\left(\mathbf{x}_{t-1} \mid \mathbf{x}_t\right)}{q\left(\mathbf{x}_t \mid \mathbf{x}_{t-1}\right)}\\
=&\left[\log \frac{p_\theta\left(\mathbf{x}_{0} \mid \mathbf{x}_1\right)}{q(\mathbf{x}_{0}|\mathbf{x}_1, \mathbf{x}_0) }+\log \frac{q(\mathbf{x}_{0}|\mathbf{x}_0)}{q(\mathbf{x}_1|\mathbf{x}_0)}\right]+\left[\log \frac{p_\theta\left(\mathbf{x}_{1} \mid \mathbf{x}_2\right)}{q(\mathbf{x}_{1}|\mathbf{x}_2, \mathbf{x}_0) }+\log \frac{q(\mathbf{x}_{1}|\mathbf{x}_0)}{q(\mathbf{x}_2|\mathbf{x}_0)}\right]+\cdots+\left[\log \frac{p_\theta\left(\mathbf{x}_{T-1} \mid \mathbf{x}_T\right)}{q(\mathbf{x}_{T-1}|\mathbf{x}_T, \mathbf{x}_0) }+\log \frac{q(\mathbf{x}_{T-1}|\mathbf{x}_0)}{q(\mathbf{x}_T|\mathbf{x}_0)}\right]\\
=&\left[\log \frac{p_\theta\left(\mathbf{x}_{0} \mid \mathbf{x}_1\right)}{q(\mathbf{x}_{0}|\mathbf{x}_1, \mathbf{x}_0) }+\log \frac{p_\theta\left(\mathbf{x}_{1} \mid \mathbf{x}_2\right)}{q(\mathbf{x}_{1}|\mathbf{x}_2, \mathbf{x}_0) }+\cdots+\log \frac{p_\theta\left(\mathbf{x}_{T-1} \mid \mathbf{x}_T\right)}{q(\mathbf{x}_{T-1}|\mathbf{x}_T, \mathbf{x}_0) }\right]+\left[\log \frac{q(\mathbf{x}_{0}|\mathbf{x}_0)}{q(\mathbf{x}_1|\mathbf{x}_0)}+\log \frac{q(\mathbf{x}_{1}|\mathbf{x}_0)}{q(\mathbf{x}_2|\mathbf{x}_0)}+\cdots+\log \frac{q(\mathbf{x}_{T-1}|\mathbf{x}_0)}{q(\mathbf{x}_T|\mathbf{x}_0)}\right]\\
=&\sum_{t=1}^T \log \frac{p_\theta(\mathbf{x}_{t-1}|\mathbf{x}_t)}{q(\mathbf{x}_{t-1}|\mathbf{x}_t, \mathbf{x}_0)}+\log \frac{q(\mathbf{x}_{0}|\mathbf{x}_0)}{q(\mathbf{x}_T|\mathbf{x}_0)}\\
=&\sum_{t=1}^T \log \frac{p_\theta(\mathbf{x}_{t-1}|\mathbf{x}_t)}{q(\mathbf{x}_{t-1}|\mathbf{x}_t, \mathbf{x}_0)}-\log q(\mathbf{x}_T|\mathbf{x}_0)
\end{align}
$$
将该 $\log$ 分式代回损失函数：
$$
\begin{align}
L&=\mathbb{E}_q\left[-\log p\left(\mathbf{x}_T\right)-\sum_{t>1} \log \frac{p_\theta\left(\mathbf{x}_{t-1} \mid \mathbf{x}_t\right)}{q\left(\mathbf{x}_t \mid \mathbf{x}_{t-1}\right)}\right]\\
&=\mathbb{E}_q\left[-\log p\left(\mathbf{x}_T\right)-\sum_{t=1}^T \log \frac{p_\theta(\mathbf{x}_{t-1}|\mathbf{x}_t)}{q(\mathbf{x}_{t-1}|\mathbf{x}_t, \mathbf{x}_0)}+\log q(\mathbf{x}_T|\mathbf{x}_0)\right]\\
&=\mathbb{E}_q\left[\log \frac{q(\mathbf{x}_T|\mathbf{x}_0)}{p\left(\mathbf{x}_T\right)}+\sum_{t=1}^T \log \frac{q(\mathbf{x}_{t-1}|\mathbf{x}_t, \mathbf{x}_0)}{p_\theta(\mathbf{x}_{t-1}|\mathbf{x}_t)}\right]\\
&=\mathbb{E}_q\left[\log \frac{q(\mathbf{x}_T|\mathbf{x}_0)}{p\left(\mathbf{x}_T\right)}+\sum_{t=2}^T \log \frac{q(\mathbf{x}_{t-1}|\mathbf{x}_t, \mathbf{x}_0)}{p_\theta(\mathbf{x}_{t-1}|\mathbf{x}_t)}+\log \frac{q(\mathbf{x}_{0}|\mathbf{x}_1, \mathbf{x}_0)}{p_\theta(\mathbf{x}_{0}|\mathbf{x}_1)}\right]\\
&=\mathbb{E}_q\left[\log \frac{q(\mathbf{x}_T|\mathbf{x}_0)}{p\left(\mathbf{x}_T\right)}+\sum_{t>1} \log \frac{q(\mathbf{x}_{t-1}|\mathbf{x}_t, \mathbf{x}_0)}{p_\theta(\mathbf{x}_{t-1}|\mathbf{x}_t)}-\log p_\theta(\mathbf{x}_{0}|\mathbf{x}_1)\right]
\end{align}
$$
最后，根据 KL 散度的公式 $D_{\mathrm{KL}}(q\parallel p)=\sum_i q(x_i)\log\frac{q(x_i)}{p(x_i)}=\mathbb{E}_q[\log\frac{q}{p}]$，将损失函数转换为 KL 散度形式：
$$
L=\mathbb{E}_q \bigg[ \underbrace{D_{\text{KL}}(q(\mathbf{x}_T | \mathbf{x}_0) \parallel p(\mathbf{x}_T))}_{L_T} + \sum_{t>1} \underbrace{D_{\text{KL}}(q(\mathbf{x}_{t-1} | \mathbf{x}_t, \mathbf{x}_0) \parallel p_\theta(\mathbf{x}_{t-1} | \mathbf{x}_t))}_{L_{t-1}} \underbrace{- \log p_\theta(\mathbf{x}_0 | \mathbf{x}_1)}_{L_0} \bigg]
$$
下一节将分别解释 $L_T,L_{t-1},L_0$ 三个部分的损失计算。

# 5 损失计算

## 5.1 $L_T=D_{\text{KL}}(q(\mathbf{x}_T | \mathbf{x}_0) \parallel p(\mathbf{x}_T))$

回看前向过程，论文选择 $\beta$ 为从 $\beta_1=10^{-4}$ 到 $\beta_T=0.02$ 线性增长的常数，这就代表着分布 $q(\mathbf{x}_T | \mathbf{x}_0)$ 仅由固定的图片 $\mathbf{x}_0$ 和常量 $\beta$ 决定，与模型参数 $\theta$ 无关。对于分布 $p(\mathbf{x}_T)\sim\mathcal{N}(\mathbf{0},\mathbf{I})$，同样与模型参数 $\theta$ 无关。

因此，整个 $L_T$ 损失都与模型参数 $\theta$ 无关，它就是一个常数，它的梯度也永远是 $0$，因此 $L_T$ 没有作用，可以直接忽略不做计算。

## 5.2 $L_{t-1}=D_{\text{KL}}(q(\mathbf{x}_{t-1} | \mathbf{x}_t, \mathbf{x}_0) \parallel p_\theta(\mathbf{x}_{t-1} | \mathbf{x}_t))$

首先需要计算 $q(\mathbf{x}_{t-1} | \mathbf{x}_t, \mathbf{x}_0)$，使用贝叶斯公式进行转换：
$$
q(\mathbf{x}_{t-1} | \mathbf{x}_t, \mathbf{x}_0)=q(\mathbf{x}_t | \mathbf{x}_{t-1}, \mathbf{x}_0)\frac{q(\mathbf{x}_{t-1} | \mathbf{x}_0)}{q(\mathbf{x}_{t} | \mathbf{x}_0)}
$$
其中：

- $q(\mathbf{x}_t | \mathbf{x}_{t-1}, \mathbf{x}_0)=q(\mathbf{x}_t | \mathbf{x}_{t-1})=\mathcal{N}(\mathbf{x}_t; \sqrt{\alpha_t}\mathbf{x}_{t-1}, \beta_t\mathbf{I})$
- $q(\mathbf{x}_{t-1} | \mathbf{x}_0)=\mathcal{N}(\mathbf{x}_{t-1}; \sqrt{\bar{\alpha}_{t-1}}\mathbf{x}_0, (1-\bar{\alpha}_{t-1})\mathbf{I})$
- $q(\mathbf{x}_{t} | \mathbf{x}_0)=\mathcal{N}(\mathbf{x}_t; \sqrt{\bar{\alpha}_t}\mathbf{x}_0, (1-\bar{\alpha}_t)\mathbf{I})$

多个高斯分布相乘仍然是高斯分布，因此 $q(\mathbf{x}_{t-1} | \mathbf{x}_t, \mathbf{x}_0)$ 可以记为一个新的高斯分布：
$$
q(\mathbf{x}_{t-1} | \mathbf{x}_{t}, \mathbf{x}_0)=\mathcal{N}(\mathbf{x}_{t-1}; \tilde{\mu}_t, \tilde{\beta}_t\mathbf{I})
$$
要计算该高斯分布的均值 $\tilde{\mu}_t$ 和方差 $\tilde{\beta}_t$，将以上三个式子带回原式（高斯分布的概率密度函数 $p(x)=\frac{1}{\sqrt{2\pi}\sigma}\exp\left(-\frac{(x-\mu)^2}{2\sigma^2}\right)$），然后进行变形：
$$
\begin{align}
&q(\mathbf{x}_{t-1} | \mathbf{x}_t, \mathbf{x}_0)\\
=&\frac{1}{\sqrt{2\pi\beta_t}}\exp\left(-\frac{(\mathbf{x}_t-\sqrt{\alpha_t}\mathbf{x}_{t-1})^2}{2\beta_t}\right)\cdot\frac{1}{\sqrt{2\pi(1-\bar{\alpha}_{t-1})}}\exp\left(-\frac{(\mathbf{x}_{t-1}-\sqrt{\bar\alpha_{t-1}}\mathbf{x}_{0})^2}{2(1-\bar{\alpha}_{t-1})}\right)\cdot\left[\frac{1}{\sqrt{2\pi(1-\bar{\alpha}_{t})}}\exp\left(-\frac{(\mathbf{x}_{t}-\sqrt{\bar\alpha_{t}}\mathbf{x}_{0})^2}{2(1-\bar{\alpha}_{t})}\right)\right]^{-1}\\
=&\frac{1}{\sqrt{2\pi\beta_t\frac{1-\bar\alpha_{t-1}}{1-\bar\alpha_t}}}\exp\left[-\frac{1}{2}\left(\frac{(\mathbf{x}_t-\sqrt{\alpha_t}\mathbf{x}_{t-1})^2}{\beta_t}+\frac{(\mathbf{x}_{t-1}-\sqrt{\bar\alpha_{t-1}}\mathbf{x}_{0})^2}{1-\bar{\alpha}_{t-1}}-\frac{(\mathbf{x}_{t}-\sqrt{\bar\alpha_{t}}\mathbf{x}_{0})^2}{1-\bar{\alpha}_{t}}\right)\right]\\
\propto&\frac{1}{\sqrt{2\pi\beta_t\frac{1-\bar\alpha_{t-1}}{1-\bar\alpha_t}}}\exp\left[-\frac{1}{2} \cdot \frac{1-\bar{\alpha}_t}{\beta_t(1-\bar{\alpha}_{t-1})} \left( \mathbf{x}_{t-1}^2 - 2\left( \frac{\sqrt{\alpha_t}(1-\bar{\alpha}_{t-1})}{1-\bar{\alpha}_t}\mathbf{x}_t + \frac{\sqrt{\bar{\alpha}_{t-1}}\beta_t}{1-\bar{\alpha}_t}\mathbf{x}_0 \right)\mathbf{x}_{t-1} \right)\right]
\end{align}
$$
得到方差和均值的结果：
$$
\begin{cases}
\begin{align}
\tilde{\beta}_t&=\frac{1-\bar\alpha_{t-1}}{1-\bar\alpha_t}\beta_t\\
\tilde{\mu}_t&=\frac{\sqrt{\bar{\alpha}_{t-1}}\beta_t}{1-\bar{\alpha}_t}\mathbf{x}_0+\frac{\sqrt{\alpha_t}(1-\bar{\alpha}_{t-1})}{1-\bar{\alpha}_t}\mathbf{x}_t
\end{align}
\end{cases}
$$
同时，由于：
$$
\begin{align}
\mathbf{x}_t &= \sqrt{\bar{\alpha}_t}\mathbf{x}_{0} + \sqrt{1-\bar{\alpha}_t}\epsilon_t \\
\Rightarrow\mathbf{x}_0 &= \frac{\mathbf{x}_t - \sqrt{1-\bar{\alpha}_t}\epsilon_t}{\sqrt{\bar{\alpha}_t}}
\end{align}
$$
因此，均值还能进一步化简为：
$$
\tilde{\mu}_t = \frac{1}{\sqrt{\alpha_t}}(\mathbf{x}_t -\frac{1 - \alpha_t}{\sqrt{1-\bar{\alpha}_t}}\epsilon_t)
$$

------

接下来，看到反向过程 $p_\theta(\mathbf{x}_{t-1} | \mathbf{x}_t)$，每一步反向过程都由神经网络 $\theta$ 通过输入的 $\mathbf{x}_t, t$ 预测得到高斯分布的均值 $\mu_\theta$ 和协方差 $\Sigma_\theta$：
$$
p_\theta(\mathbf{x}_{t-1}|\mathbf{x}_t) := \mathcal{N}(\mathbf{x}_{t-1}; \mu_\theta(\mathbf{x}_t, t), \Sigma_\theta(\mathbf{x}_t, t))
$$
但论文发现，将协方差设定为常数 $\Sigma_\theta(\mathbf{x}_t, t)=\sigma_{t}^2\mathbf{I}$ 已经可以得到不错的结果（$\sigma_t^2=\beta_t$ 或 $\sigma_t^2=\frac{1-\bar\alpha_{t-1}}{1-\bar\alpha_{t}}\beta_t$），这样就可以简化模型的训练，模型只需要预测高斯分布的均值 $\mu_\theta$ 了。

既然协方差被设定为固定值 $\Sigma_\theta(\mathbf{x}_t, t)=\sigma_{t}^2\mathbf{I}$，我们要计算两个高斯分布 $q(\mathbf{x}_{t-1} | \mathbf{x}_t, \mathbf{x}_0)$ 和 $p_\theta(\mathbf{x}_{t-1} | \mathbf{x}_t)$ 之间的 KL 散度，实际上就等价于计算它们均值之间的均方误差。因此 $L_{t-1}$ 可以写为：
$$
L_{t-1} = \mathbb{E}_q \left[ \frac{1}{2\sigma_t^2} \| \tilde{\mu}_t - \mu_\theta(\mathbf{x}_t, t) \|^2 \right] + C
$$
将上一节推导出的 $\tilde{\mu}_t = \frac{1}{\sqrt{\alpha_t}}(\mathbf{x}_t -\frac{1 - \alpha_t}{\sqrt{1-\bar{\alpha}_t}}\epsilon_t)$ 代入：
$$
L_{t-1} = \mathbb{E}_{\mathbf{x}_0, \epsilon} \left[ \frac{1}{2\sigma_t^2} \left\| \frac{1}{\sqrt{\alpha_t}}\left(\mathbf{x}_t -\frac{1 - \alpha_t}{\sqrt{1-\bar{\alpha}_t}}\epsilon_t\right) - \mu_\theta(\mathbf{x}_t, t) \right\|^2 \right]
$$
为了最小化这个损失，朴素的方法是让神经网络 $\mu_\theta(\mathbf{x}_t, t)$ 去直接拟合目标均值 $\tilde{\mu}_t$。但论文发现，当前步的图像 $\mathbf{x}_t$ 是已知的，真正未知的变量其实只有注入的噪声 $\epsilon_t$，因此，我们可以对神经网络进行参数化重构，让模型不直接预测均值，而是去预测图像中的噪声 $\epsilon_\theta(\mathbf{x}_t, t)$：
$$
\mu_\theta(\mathbf{x}_t, t) = \frac{1}{\sqrt{\alpha_t}}\left(\mathbf{x}_t -\frac{1 - \alpha_t}{\sqrt{1-\bar{\alpha}_t}}\epsilon_\theta(\mathbf{x}_t, t)\right)
$$
将重构后的 $\mu_\theta$ 代回损失函数，损失函数变成了预测噪声和真实噪声之间的误差：
$$
L_{t-1} = \mathbb{E}_{\mathbf{x}_0, \epsilon} \left[ \frac{\beta_t^2}{2\alpha_t \sigma_t^2 (1-\bar{\alpha}_t)} \| \epsilon_t - \epsilon_\theta(\sqrt{\bar{\alpha}_t}\mathbf{x}_0 + \sqrt{1-\bar{\alpha}_t}\epsilon, t) \|^2 \right]
$$

## 5.3 $L_0=- \log p_\theta(\mathbf{x}_0 | \mathbf{x}_1)$

在论文中，$t=1$ 和 $t>1$ 的情况被分为 $L_0$ 和 $L_{t-1}$ 两个损失，作者的想法是需要特殊处理 $t=1$ 这个最后一步，因为高斯分布式连续的，图像的像素值是离散的，需要在每个离散像素区间内的积分来得到最终的概率。

但在实际实现当中，$L_0$ 和 $L_{t-1}$ 的计算是统一的，不再进行分开，直接使用上一小节的损失函数即可。

# 6 算法流程

将前面的前向加噪与反向去噪过程结合，DDPM 的训练与采样过程可以被总结为两个极其简洁的算法流程。

## 6.1 训练阶段

训练的核心目的是让模型学会“认出”图片里的噪声。

1. 从真实数据分布中采样一张清晰图片 $\mathbf{x}_0 \sim q(\mathbf{x}_0)$。

2. 随机抽取一个时间步 $t \sim \text{Uniform}(\{1, \dots, T\})$。

3. 生成一个标准高斯噪声 $\epsilon \sim \mathcal{N}(\mathbf{0}, \mathbf{I})$。

4. 执行梯度下降优化极简损失函数：

   $$\nabla_\theta \left\| \epsilon - \epsilon_\theta(\sqrt{\bar{\alpha}_t}\mathbf{x}_0 + \sqrt{1-\bar{\alpha}_t}\epsilon, t) \right\|^2$$

5. 重复上述步骤，直至模型收敛。

## 6.2 采样阶段

采样的核心是从一个纯随机噪声开始，利用训练好的模型一步步剥离噪声，最终得到清晰的图片。

1. 从标准高斯分布中采样生成一张纯噪声图像 $\mathbf{x}_T \sim \mathcal{N}(\mathbf{0}, \mathbf{I})$。

2. 对于 $t = T, T-1, \dots, 1$ 依次执行以下循环：

   - 采样一个额外的环境噪声 $\mathbf{z} \sim \mathcal{N}(\mathbf{0}, \mathbf{I})$（如果 $t=1$ 则 $\mathbf{z}=\mathbf{0}$，因为最后一步不需要再加额外的随机扰动）。

   - 根据模型预测的噪声，计算上一步的图像状态：

     $$\mathbf{x}_{t-1} = \frac{1}{\sqrt{\alpha_t}} \left( \mathbf{x}_t - \frac{1-\alpha_t}{\sqrt{1-\bar{\alpha}_t}} \epsilon_\theta(\mathbf{x}_t, t) \right) + \sigma_t \mathbf{z}$$

3. 循环结束，输出最终生成的清晰图像 $\mathbf{x}_0$

