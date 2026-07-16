**在线自蒸馏 (On-Policy Self-Distillation, OPSD)**：一种让同一大语言模型分别充当教师与学生，教师利用标准答案等特权信息，在学生自身生成轨迹上提供逐词分布监督的推理能力训练方法。

<!--more-->

原始论文：[Self-Distilled Reasoner: On-Policy Self-Distillation for Large Language Models](http://arxiv.org/abs/2601.18734)

# 1 回顾广义知识蒸馏 (GKD)

对于[广义知识蒸馏](https://io.zouht.com/199.html)，它的思路是学生模型生成自己的 On-Policy 序列，然后用教师模型给出该序列的 Token-Level 分布，训练目标是让学生和教师模型的分布差异最小化。用公式来表示 GKD 的 OD 部分便是：
$$
\mathcal{L}_{\text{OD}}(\theta)
:=
\mathbb{E}_{x \sim X}
\left[
\mathbb{E}_{y \sim p_S(\cdot \mid x)}
\left[
\mathcal{D}\left(p_T \,\|\, p_S^\theta\right)(y \mid x)
\right]
\right].
$$
这种蒸馏的前提是教师模型比学生模型要强，常见场景是将一个大参数量的教师模型的能力蒸馏到一个小参数量的学生模型中。另外，该种训练需要的数据非常简单，只需要提示词而不需要答案，因为教师模型的分布被默认为答案。

# 2 在线自蒸馏 (OPSD)

在线自蒸馏提出了一个另一种蒸馏方式，该方式不需要更强的教师模型教学生模型，而是让同一个模型自己来教自己。那么如何保证同一个模型自我迭代能够提升能力呢？这就是 OPSD 的核心思路了，即通过特权信息（参考答案）来给教师模型赋予更强的能力。

![](https://assets.zouht.com/img/note/200-01.webp)

OPSD 需要的数据格式为 $(x,y^{\star})\sim S$，其中 $x$ 为提示词，$y$ 为特权信息（标准答案），它和 GKD 的数据需求区别就是需要额外的特权信息 $y$.

在训练时，学生模型仅接收到提示词 $x$，进行在线采样得到 On-Policy 的序列 $\hat{y}\sim P_S(\cdot\mid x)$，而教师模型虽然是同一个模型，但它会接收到特权信息 $y^{\star}$，然后根据学生模型的采样序列来计算分布 $p_T(\cdot\mid x,y^{\star},\hat{y}_{<n})$，训练目标是让学生和教师模型的分布差异最小化（尽管两个模型是同一个）。

用公式来表示 OPSD 便是：
$$
\mathcal{L}_{\mathrm{OPSD}}(\theta)
=
\mathbb{E}_{(x,y^\star)\sim\mathcal{S}}
\mathbb{E}_{\hat{y}\sim p_S(\cdot\mid x)}
\frac{1}{|\hat{y}|}
\sum_{n=1}^{|\hat{y}|}
D\!\left(
p_T\!\left(\cdot\mid x,y^\star,\hat{y}_{<n}\right)
\,\middle\|\,
p_S\!\left(\cdot\mid x,\hat{y}_{<n}\right)
\right).
$$
特权信息的使用方式通常是拼接到提示词中。假如对于学生模型，它收到的提示词是：

```
问题：求函数 f(x) = 3x² + 2x − 5 在 x = 2 处的导数。

答案：
```

那么对于教师模型，它收到的提示词将会是：

```
问题：求函数 f(x) = 3x² + 2x − 5 在 x = 2 处的导数。

以下是参考解法：首先求得 f′(x) = 6x + 2，然后代入 x = 2 计算：f ′(2) = 6(2) + 2 = 14

理解参考解法后，请尝试在下方使用自己的方法解决此题：

答案：
```

可以看出，对于同一个模型，学生模型在做闭卷考试而教师模型在做开卷考试，开卷考试的能力显然是比闭卷考试强的，因此这种方式能够提升学生模型的能力。

这个思路还是非常朴素和好理解的，并且其实这个思路在生成离线蒸馏数据时已经广泛实践过了。例如在生成离线蒸馏数据时，常常也会用类似的提示词模板来“提升”被蒸馏模型的能力，得到质量更高的蒸馏数据。

# 3 应用于训练

OPSD 方法实际是 GKD 方法的变种，它也已经被主流大模型训练框架例如 [ms-swift](https://github.com/modelscope/ms-swift) 支持了，我们看到 ms-swift 文档的 [GKD 页面](https://swift.readthedocs.io/zh-cn/v4.3/Instruction/GKD.html)，要进行 OPSD 训练，实际上就是将教师模型的参数 `--teacher_model` 设置为和学生模型一致，另外在数据集中提供 `teacher_prompt` 的特权信息即可。