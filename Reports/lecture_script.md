# 从中心极限定理到 Video Generation · 讲稿

**汇报人：赵文彪　·　总时长：约 36 分钟　·　27 张幻灯片**

> 使用说明：
> - 每页都有「⏩ 翻页 →」标记和大概用时
> - **加粗** 部分建议重读/停顿
> - 公式都按"先念字面、再说意思、最后举比喻"三段式讲
> - 边讲边记得切换到下一页

---

## ⏩ 翻页 → 首页（Frontispiece） · 1 分钟

各位老师、同学下午好，我是赵文彪。

今天我要讲一个看起来跨度很大的故事 —— **从中心极限定理，一路讲到 Video Generation**。

听起来这两件事差了 200 年：一个是 19 世纪末数学家们在炉子边写下的极限定理，一个是 2026 年正在抖音上以每分钟好几部的速度涌现的 AI 短剧。

但我想说服你们一件事：**它们讲的本质上是同一件事 —— 关于"高斯"和"信号"的故事。**

整个汇报分六章，从概率论的根 → 语言模型 → 扩散模型 → 自回归视频 → 开放问题 → 学术与工业的脱节，最后我们会在 Möbius 环上回到起点。

我们开始。

---

## ⏩ 翻页 → 第 1 页（Chapter I 封面） · 30 秒

第一章 —— **中心极限定理（CLT）**。

副标题我写的是："一切归于钟形的数学起点"。这一章只想做一件事：让大家彻底相信 —— **正态分布不是某种特例，它是宇宙的某种偏好。**

---

## ⏩ 翻页 → 第 2 页（万物归于钟形） · 1.5 分钟

开场我问大家一个问题：**为什么宇宙这么偏爱正态分布？**

你随便去测什么 —— 人的身高、考试分数、噪声电压、股票日收益 —— 画出直方图，基本都是这个钟形。这有点诡异。

我们看三个例子：

- **case 1：** 一颗骰子，1 到 6 均匀分布，不像钟形
- **case 2：** 指数分布，左边一堆，右边长尾，明显偏斜
- **case 3：** 我随便画一个奇形怪状的分布，乱七八糟

但只要你做一件事 —— **不停地从它们里采样、求均值、再画样本均值的分布** —— 不管原分布长成什么鬼样子，**最后都收敛到一个漂亮的钟形**。

所以钟形的来源不是"分布特殊"，**是"求均值"这个动作本身在塑造高斯**。

比喻一下：就像 Galton Board（高尔顿钉板）—— 你扔一颗弹珠下去，它在每个钉子上随机左右，**单次完全随机**；但你扔一万颗，底下堆出来的形状几乎一定是钟形。每次随机的方向不可预测，但很多随机叠加起来，**形状变得不可避免**。

---

## ⏩ 翻页 → 第 3 页（Theorem · CLT 数学陈述） · 2 分钟

现在我们用数学说清楚这件事。

**Lindeberg–Lévy 中心极限定理**，1920 年代的经典形式：

设 $X_1, X_2, \dots, X_n$ 是 **独立同分布**，期望是 $\mu$，方差是 $\sigma^2 < \infty$。记样本均值 $\bar{X}_n$。那么当 $n \to \infty$：

$$\sqrt{n}\;\frac{\bar X_n - \mu}{\sigma} \;\xrightarrow{\;d\;}\; \mathcal{N}(0,1).$$

公式我拆开念一下：
- $\bar X_n - \mu$：样本均值跟真均值的偏差
- 除以 $\sigma$：把偏差按标准差归一化
- 乘 $\sqrt{n}$：把这个偏差放大 —— 因为不放大，它会被平均掉趋于 0，看不见
- 等价说法：$\bar X_n \approx \mathcal{N}(\mu, \sigma^2/n)$，**样本均值本身就近似高斯**，方差以 $1/n$ 速度收缩

比喻一下：CLT 就像一个**显微镜** —— 你看不见"波动"，因为它很小；但乘上 $\sqrt{n}$ 放大之后，**波动的形状暴露了，永远是钟形**。

CLT 的三个前提：**独立、同分布、有限方差**。

最后一个小彩蛋 —— 右下角写了：**如果方差不存在，CLT 就破了**。最经典的反例是柯西分布，它的方差是无穷。你平均一千次、一万次柯西分布的样本，**永远不会变成钟形** —— 还是柯西。这告诉我们：**高斯的"统治"也有它的边界**。

---

## ⏩ 翻页 → 第 4 页（When Noise Becomes Signal） · 1.5 分钟

这一页是第一章的小结，但也是整场讲座的**总伏笔**。

CLT 用一句话讲：**任何由大量独立随机扰动叠加而成的过程，结果都倾向于正态分布。**

三个领域都印证了这件事：

- **物理：布朗运动** —— 花粉颗粒为什么会颤？因为水分子从各方向撞它，是大量碰撞的叠加 —— 高斯
- **金融：股价日收益率** —— 每天的对数收益率为什么近似高斯？因为它是无数交易决策的叠加
- **深度学习：神经网络初始化** —— 宽神经网络的输出，是大量随机权重的线性组合 —— 趋于高斯过程

最关键的是黄色高亮那句话：

> **如果"噪声"本身就是高斯的 —— 那"生成"的本质，会不会就是"从噪声里捞出信号"？**

这就是第三章 Diffusion 的核心思想。**记住这个伏笔**，等会儿揭晓。

---

## ⏩ 翻页 → 第 5 页（Chapter II 封面 · Language Models） · 45 秒

进入第二章 —— **语言模型**。

上一章我们说"一切归于高斯"，但有个明显的反例：**语言是离散的**。词是从词表里挑出来的，不可能是"半个 token"。所以这一章我们要讲：**当随机变量不再是连续数，而是"词"的时候，整套数学要怎么改写？**

四个小节：Transformer 架构、Next-token Prediction 的数学本质、离散 vs 连续、最后是 softmax 这个连接两个世界的小天使。

---

## ⏩ 翻页 → 第 6 页（Next-Token Prediction） · 1.5 分钟

LLM 做的事情，浓缩成一句话：**给定前文 token 序列，输出对下一个 token 的概率分布。** 仅此而已。

核心公式：

$$P(x_t \mid x_{<t}) = \operatorname{softmax}(W_o \cdot h_t), \quad h_t = \operatorname{Transformer}(x_1,\dots,x_{t-1}).$$

我念一下：
- 前文 token 进 Transformer，输出一个隐状态 $h_t \in \mathbb{R}^d$
- 用一个线性层 $W_o$ 把它映射到词表维度，得到 logits
- softmax 把 logits 变成概率分布

比喻：**LLM 是一台超级填空机**。它不知道"意思"，它只学会一件事 —— 给一段前文，下一个最合理的词应该是什么。

下面那条管线图是一遍：[B,L] → [B,L,d] → … → [B,L,|V|]，最后输出的就是一个 **|V| 维的概率向量**。

注意右下角的注脚：**$h_t$ 是连续的，但输出空间是离散的** —— 这就是 LLM 和 Diffusion 最关键的差异。LLM 在最后一步"投影"到离散，Diffusion 始终留在连续空间。

---

## ⏩ 翻页 → 第 7 页（Discrete vs Continuous） · 1.5 分钟

这一页其实是整场讲座最重要的一张表。

把 LLM 和 Diffusion 在五个维度上对照：

| | LLM（离散） | Diffusion（连续） |
|---|---|---|
| 样本空间 | 有限词表 | $\mathbb{R}^d$ |
| 概率定义 | $P(X=v_i)$ | 概率密度 $p(x)$ |
| 归一化 | $\sum P = 1$（softmax） | $\int p\,dx = 1$ |
| 学习目标 | 交叉熵 / NLL | 噪声预测 / score matching |
| 采样 | 多项分布抽 | SDE / ODE 数值解 |

比喻：**LLM 在一个 |V| 维的"概率单纯形"上跳跃**（每次选一个顶点），**Diffusion 在 $\mathbb{R}^d$ 流形上滑行**（连续移动）。

最下面那行斜体字：那么 —— 能不能把图像 / 视频也"离散化"让它像语言一样建模？或者反过来让 LLM 也"连续化"？**这就是接下来几章的全部张力**。

---

## ⏩ 翻页 → 第 8 页（Softmax） · 1 分钟

softmax 的公式，加了温度 τ：

$$p_i = \frac{\exp(z_i / \tau)}{\sum_j \exp(z_j / \tau)}.$$

我喜欢这个公式因为它**把连续的 logits 变成了离散概率**，是两个世界的桥梁。

温度 τ 的作用很有意思 —— 比喻：
- **τ 大（接近无穷）**：所有 token 概率被压平，像"民主投票" —— 大家差不多
- **τ 小（接近 0）**：放大差距，最高的 logit 拿走一切 —— "独裁"，几乎就是 argmax
- **τ = 1**：标准 softmax

右边那个三角形就是 2-单纯形 —— softmax 输出永远落在它上面。**采样温度调高调低，就是这枚棋子在这个三角形上来回滑动**。

---

## ⏩ 翻页 → 第 9 页（Chapter III 封面 · Diffusion Transformer） · 45 秒

进入第三章 —— **扩散模型**。

左边小图：噪声 → 半成形 → 数据。这一章我们要把第一章那个伏笔回收掉 —— **如果噪声就是高斯，那生成就是从高斯里把信号"剥"出来。**

5 个小节：从为什么回到连续空间，到前向加噪、反向去噪、DiT 架构、训练目标。

底下那句话：**"Diffusion 让我们想起第 I 章 —— 所有噪声叠加，本质上是高斯。我们只需学会一件事：把高斯一点点剥回数据。"**

---

## ⏩ 翻页 → 第 10 页（Forward Process） · 2 分钟

前向过程 —— **把数据一步步煮成噪声**。

连续时间视角，写成 SDE：

$$d\mathbf{x} = f(\mathbf{x}, t)\,dt + g(t)\,d\mathbf{W}.$$

念一下：
- $f(\mathbf{x},t)\,dt$：确定性的"漂移项"
- $g(t)\,d\mathbf{W}$：随机扰动项，其中 $d\mathbf{W}$ 是 **Wiener 过程 —— 布朗运动**

注意这里！**布朗运动就是我们第一章讲的那个高斯叠加的极限**。CLT 的伏笔，第一次在公式里现身了。

实际训练用的是离散时间版本 —— DDPM 闭式形式：

$$\mathbf{x}_t = \sqrt{\bar\alpha_t}\,\mathbf{x}_0 + \sqrt{1-\bar\alpha_t}\,\boldsymbol{\varepsilon}, \qquad \boldsymbol{\varepsilon} \sim \mathcal{N}(0, I).$$

意思是：**给定原图 $\mathbf{x}_0$ 和时刻 $t$，我一次性算出 $\mathbf{x}_t$**。不用真的迭代 1000 步，直接闭式跳过去。

这是 diffusion **训练高效的根源** —— 因为我可以从任意 $t$ 直接采样训练样本，不用按顺序模拟。

比喻：往一杯清水里一勺一勺加墨水。每加一勺，颜色变深一点；加到第 1000 勺，就是纯黑的"高斯"。**前向是个不可逆的"煮"的过程**。

---

## ⏩ 翻页 → 第 11 页（Reverse Process · SDE vs ODE） · 2 分钟

反向过程 —— **从噪声里捞出图像**。

有两种数学路径，左右对比：

**Case A · Reverse SDE（含随机项）：**

$$d\mathbf{x} = \big[f(\mathbf{x},t) - g^2(t)\,\nabla_x \log p_t(\mathbf{x})\big] dt + g(t)\, d\bar{\mathbf{W}}.$$

**Case B · Probability Flow ODE（确定性）：**

$$\frac{d\mathbf{x}}{dt} = f(\mathbf{x},t) - \tfrac{1}{2} g^2(t)\, \nabla_x \log p_t(\mathbf{x}).$$

两个公式里都有同一个对象：**$\nabla_x \log p_t(\mathbf{x})$ —— 数据分布的对数密度梯度，也叫 score**。

中间那个红框是整个 diffusion 的灵魂：

$$s_\theta(\mathbf{x}, t) \approx \nabla_x \log p_t(\mathbf{x}).$$

**整个 diffusion 模型，本质上就是在学这一个对象 ——score function**。它告诉我们："你现在在哪儿？想去高密度区域，应该往哪个方向走？"

比喻：**score 就是一个指南针**。在概率空间里，它永远指向"数据更可能的地方"。反向过程就是"沿着指南针一步步往家走"。

SDE 和 ODE 怎么选？看右边的对照表：
- SDE：路径每次都不一样，**多样性高**，但**步数多**（>50 步）
- ODE：同一初始噪声 → 永远到同一图像，**确定**，但**步数少**（4-10 步）

右下角那张轨迹图：**SDE 是锯齿状的随机游走，ODE 是一条光滑曲线** —— 后面 Rectified Flow / Flow Matching 把这条 ODE 拉得更直，让步数更少。这是后面"蒸馏"的基础。

---

## ⏩ 翻页 → 第 12 页（DiT Architecture） · 1.5 分钟

第三章的重头戏 —— **DiT，Diffusion Transformer，Peebles & Xie 2022**。

一句话：**把 U-Net 替换成 Transformer，让 diffusion 模型可以像 LLM 一样规模化。**

左边的大架构图你可以慢慢看。简单来说：噪声 latent 进来 → Patchify 切成 2×2 小块 → N 个 DiT Block → 输出预测的噪声 $\varepsilon_\theta$。条件（timestep $t$ 和 condition $c$）从顶上注入。

右上角那个 margin note 我想强调一下：**这套思路如今几乎成了 2024–2026 视觉生成的统一架构 —— SD3 / SORA / Seedance / Wan 全部沿用 DiT 范式。** 你可以把 DiT 理解为视觉生成里的 "Transformer 时刻"。

三个关键创新：

- **i · adaLN-Zero**：条件 $(t, c)$ 不是简单 concat 进去，而是用 scale / shift / gate 三个调制因子，**零初始化**让深层网络冷启动稳定
- **ii · Patchify**：把 latent 切成 token，像 ViT 一样吃 token 序列
- **iii · Scaling Law**：参数 / 算力越大，FID 单调下降 —— **视觉生成第一次拥有了 LLM 那样的 scaling 曲线**

---

## ⏩ 翻页 → 第 13 页（Training Objective） · 1.5 分钟

整个 diffusion 训练的核心，只有一个 loss：

$$\mathcal{L}(\theta) = \mathbb{E}_{t, \mathbf{x}_0, \boldsymbol{\varepsilon}}\big[ \,\big\Vert \boldsymbol{\varepsilon} - \boldsymbol{\varepsilon}_\theta(\mathbf{x}_t, t, c) \big\Vert^2 \,\big].$$

念一下：随机抽一个时刻 $t$、一张图 $\mathbf{x}_0$、一个噪声 $\boldsymbol{\varepsilon}$，把它们按前向公式合成出 $\mathbf{x}_t$；然后让网络看到 $\mathbf{x}_t$，预测出我加进去的噪声 $\boldsymbol{\varepsilon}$。损失就是预测和真实的 **L2 距离**。

比喻：**这个网络一辈子只学一件事 —— 看到一张被泼了墨的画，猜墨水是什么。** 学好了之后，它就能在反向时一勺一勺地把墨水挑出来。

左边那段伪代码是 6 行训练循环 —— 极其简单，但**就是这 6 行训出了今天所有的视觉生成模型**。

---

## ⏩ 翻页 → 第 14 页（Chapter IV 封面 · AR Video Diffusion） · 1 分钟

第四章 —— **自回归视频扩散**。

视频生成不能简单套 DiT，因为有个商业上的硬约束 —— **要流式（streaming）**。

下面三张卡对比三种架构：

- ✗ **双向 DiT**：一次生成整段，**得等全部去噪完才出第一帧**，长度受限
- ✗ **纯自回归（GPT-style）**：逐 token 流式，但**视觉信号是连续的**，强行离散化损失质量
- ✓ **AR + Diffusion**（hybrid）：**chunk 级因果** —— 段与段流式串接，段内仍是高质量 diffusion

比喻：**双向 DiT 像写一整本书一次过，纯 AR 像逐字打字机，AR+Diffusion 像分章节写作 —— 段内高质量，段间流式**。这是当前视频生成的主流方案。

最下面：Diagonal Distillation 在 5 秒视频上做到 **2.6 秒生成（31 FPS）**，比未蒸馏快 **277×**。这就是当前的速度水平。

---

## ⏩ 翻页 → 第 15 页（Chunk-by-Chunk） · 2 分钟

具体怎么 chunk-by-chunk？

把视频概率拆成连乘：

$$p(\text{video}) = \prod_{k=1}^{K} p(\text{chunk}_k \mid \text{chunk}_{<k}).$$

**每个 chunk 自己用 diffusion 生成，chunk 之间用 causal attention 串起来。**

比喻：**像写一篇议论文** —— 你每写一段，都要看前面写过的所有段，但**不能往后偷看**（causal）。

三个设计点：

- **i · Causal Mask**：第 $k$ 个 chunk 只能看到前 $k-1$ 个，严格因果
- **ii · KV Cache**：历史 chunk 的 K/V 算一次缓存起来 —— **段越长，加速越显著**。这跟 LLM 推理的 KV cache 是同一个思想
- **iii · Self Forcing**（Huang et al., 2025）：训练时让学生看自己生成的前缀，**缓解 train-test gap** —— 这一点等会儿第 19 页还会出现

---

## ⏩ 翻页 → 第 16 页（Distillation · 从 50 步到 4 步） · 1.5 分钟

光有架构还不够 —— **diffusion 默认要 25-50 步采样，每帧都得跑这么多次，5 秒视频要算几千次前向。太慢。**

所以要**蒸馏**。

比喻：**师傅 50 招打人，学徒只学 4 招也能打中** —— 这就是蒸馏：把多步教师的能力压到少步学生身上。

时间线四个里程碑：

1. **2023.Q4 · DMD**（Distribution Matching Distillation）—— diffusion 第一次能 1-4 步采样
2. **2025.Q1 · Causal ODE Distillation** —— 把 DMD 从双向架构搬到自回归视频
3. **2025.Q3 · Diagonal Distillation** —— 早 chunk 多步保画质、晚 chunk 少步追速度，**非对称步数策略**
4. **2025.Q4 · Causal Forcing** —— 用 AR 多步教师直接初始化少步学生

右边的那个红色大数字：**31 FPS · 5 s 视频 2.6 s 生成 · 单 GPU · 4 步扩散**。这就是 2026 年的工业纪录。

学术上叫 **asymmetric DMD** 或 **diagonal distillation**，是 2025 年最热的视频蒸馏方向。

---

## ⏩ 翻页 → 第 17 页（The Speed Leap） · 1 分钟

把架构 + 蒸馏的红利合起来看 —— 这张大柱状图比 100 句话都直观。

生成 1 段 5 秒视频的耗时：

- 2023 · SVD / AnimateDiff：**~600 秒**
- 2024 · CogVideoX / Sora：**~120 秒**
- 2025 · Wan 2.1 / Seedance：**~30 秒**
- 2026 · Self-Forcing 家族：**2.6 秒**

三年时间，从"看一杯水煮开"压到"按一下回车"。

最关键的红字：**拐点不是模型变大，而是"蒸馏 + 自回归架构"的组合**。

下面那句话：**"更快不等于更好"** —— 下一章我们就讲，模型跑得飞快之后，什么新问题浮现了。

---

## ⏩ 翻页 → 第 18 页（Chapter V · Open Problems + V.1 长一致性） · 1.5 分钟

第五章 —— **开放问题：漂亮的数学在哪里失灵**。

5 个问题列在上面，我会依次过 4 个核心的。第一个 **V.1 长一致性** 就在这一页：

5 秒视频还行，**30 秒视频里，角色的面孔已经悄悄漂移了**。FramePack 在超长视频上 subject consistency 下降 **13.71%**。

比喻：**像传话游戏（telephone game）**。第一个人说"红衣服戴眼镜的女生"，传到第 30 个人时，可能变成"穿红裙子的小孩"。**每一帧都基于上一帧的"回声"**，回声里的微小变形，30 步之后就放大成新的人。

这不是模型不努力 —— **是自回归本质决定的**。

---

## ⏩ 翻页 → 第 19 页（V.2 Compounding Error · 误差累积） · 1.5 分钟

把刚才那个"回声变形"用数学写清楚：

$$\delta_{\text{total}} \approx \sum_{k=1}^{K} \delta_k \cdot \gamma^{K-k}, \qquad \gamma \ge 1.$$

每步预测误差 $\delta_k$，乘以放大因子 $\gamma$，K 步后误差**线性或指数累积**。如果 $\gamma > 1$，这就是 **雪球效应**。

右边那张图：黑色实线是 naive AR，30 秒之后误差炸了；红色实线（C · error recycling）能压得很平。

三派解药：

- **A · Noise Schedule** —— 让模型对历史依赖减弱（代表：Diffusion Forcing）
- **B · Frame Anchoring** —— 用初始 reference frame 当锚点（代表：FramePack, StreamingT2V）
- **C · Error Recycling** ★ —— **主动把误差注入训练**，让学生学会"自愈"（代表：Stable Video Infinity）

最下面那句 Henry Ford 的话用在这里很妙：**"Failure is simply the opportunity to begin again, this time more intelligently."** —— 不是回避错误，是把错误当作训练信号。

核心是 **train-test gap**：训练时看 ground truth，推理时看自生成。Self Forcing 系列就是为了对齐这两者。

---

## ⏩ 翻页 → 第 20 页（V.3 镜头 + V.4 音视频） · 1 分钟

两个稍微短一点的问题，放在一页讲。

**左边 · 镜头转换**：当前视频生成基本都是"一镜到底"，但电影需要 cut / pan / close-up。难点在**跨 shot 的角色一致性 + 自然过渡**。Seedance 1.0 原生支持 multi-shot storytelling。

**右边 · 音视频联合生成**：声音和画面是同一物理事件的两个观测，**但模型常 desync**。AV-Phys Bench 显示主流模型在跨模态物理一致性上仍有大缺陷。Seedance 1.5 pro 用 Dual-branch DiT + Cross-modal Joint 来做。

底下一句话：**视频生成正在从"生成一段画面"升级为"生成一个事件"** —— 一个事件必然包含多视角（镜头）+ 多模态（音 / 画 / 物理）。

---

## ⏩ 翻页 → 第 21 页（Video Generation ≠ World Model） · 1.5 分钟

整场讲座我最喜欢的一个开放问题。

大字问题：**Sora 能生成一杯水倒掉的视频 —— 但它"知道"水的物理规律吗？**

定义对比：

- **Video Generation**：生成视觉上 plausible（合理）的视频
- **World Model**：可交互、可推演、保持时空一致的环境模拟器

**关键差异**：能否对动作做出正确响应？能否保持长程时空一致？

右边那个金字塔图：

- 底层（宽）：Video Generation —— Sora / Veo3 / Seedance / Wan
- 中层：Interactive Video Models —— Genie / DreamerV3 / 早期 WMA
- 顶层（红）：True World Models —— **research frontier · rare**

下面那行字：**当前业界 95% 的模型，仍停在底层与中层之间**。

引用黄迅（Xun Huang）2025 年那篇博客：**"Sora 和 Veo3 还不能算真正的世界模型 —— 它们生成视觉合理的视频，但缺乏可交互性、动作响应、长程时空一致性。"**

业界的核心争议：**视频生成会不会自然涌现出世界模型？还是需要根本不同的架构？** —— 这是接下来 2-3 年最重要的问题之一。

---

## ⏩ 翻页 → 第 22 页（Chapter VI · The Great Disconnect） · 1.5 分钟

第六章 —— **学术与工业的脱节**。

两边在乎的完全不是同一件事：

**学术界（Side A）：**
- FID / FVD / VBench 指标提升 **0.5**
- 新颖架构 / 可证明的理论保证
- 论文可复现性 / open-source weights

**工业界（Side B）：**
- 单卡 5 秒生成够不够？**$ / 秒视频**
- 角色一致性 + 字幕 + 配音 + 翻译一条龙
- 单部剧成本能否压到 **1/10**

比喻：**米其林评委 vs 餐厅老板**。评委盯着摆盘和精致度，老板盯着翻台率和成本。两套语言。

底下那句话：**"学术论文的 SOTA，和工业产品的 SOTA，可能是两件事。"**

---

## ⏩ 翻页 → 第 23 页（AI 微短剧产业） · 1.5 分钟

这一页是整场讲座里**少有的、不是论文数据**的部分 —— 但我觉得最值得思考。

三个数字：

- **¥505 亿** —— 2024 年中国微短剧市场，**首次超过院线票房（425 亿）**
- **50,000+** —— 2026 年 3 月单月抖音上线的 AI 短剧数量
- **≈ 95%** —— 2026 Q1 抖音新作中 AI 的占比

下面那个 8 步流水线图：剧本 → 分镜 → 角色 → **AI 生成（Seedance / Kling / Vidu）** → 多镜头拼接 → 配音 → 字幕 → 平台分发。**多步传统流程被压缩成端到端全栈工具链。**

右边三张小卡：
- 制作周期：**3 个月 → 1 个月**
- 成本：**1/5 ~ 1/10**
- 团队：**~10 人**

底下那句蓝色 callout：**中国微短剧产业是全球第一个大规模商用 AI 视频生成的场景 —— 它不在乎模型有多优雅，只在乎单部剧的 ROI。**

这就是工业语言。

---

## ⏩ 翻页 → 第 24 页（Seedance 案例） · 1.5 分钟

讲一个学术 + 工业结合得很好的案例 —— **字节跳动的 Seedance**。

时间线：

- **2023** · 内部原型
- **2025.06** · Seedance 1.0 —— 即梦平台发布，5s @ 1080p 41.4 秒（arXiv 2506.09113）
- **2025.12** · Seedance 1.5 pro —— 原生音视频联合生成（arXiv 2512.13507）
- **2026.02** · Seedance 2.0 —— 多模态统一，12 reference files

三大技术亮点：

- **A · Data**：精细化视频 caption + 多源数据筛选 —— **高质量训练集是工业模型的护城河**
- **B · Model**：单模型同时支持 T2V / I2V / 多镜头叙事 —— **一套权重打十几种任务**
- **C · Speed**：多阶段蒸馏 + 系统级优化 —— **10× 推理加速**，这是推到亿级用户的关键

学术侧公开 technical report，工业侧直接对接抖音 / 即梦 / 豆包，**亿级 C 端用户的反馈反哺下一代模型**。这种"technical report + 大规模商用"双轨模式，是当前 AI 视频领域**最健康的范式之一**。

---

## ⏩ 翻页 → 第 25 页（A Möbius Loop · 总结） · 2 分钟

到这里讲座基本讲完了。最后一页我想把六章折叠到一起 —— 看看它们能不能首尾相连，成为一个**莫比乌斯环**。

大字标题问的就是：**这场讲座 —— 是一个圆环吗？**

来一一回响：

- **I.** 所有的随机扰动叠加 —— 都**趋于高斯**
- **II.** LLM 在**离散单纯形**上跳跃 —— softmax 是它的语法
- **III.** **Diffusion 把高斯一点点剥回数据 —— 这是 CLT 的逆操作**
- **IV.** 自回归视频 —— 把空间延伸到**时间**，chunk by chunk
- **V.** 长一致性、误差、镜头、音视频 —— 当模型试图理解"**世界**"
- **VI.** 学术与工业的脱节 —— **但脱节本身，也在催生新研究**

第 III 条最关键 —— **Diffusion 是 CLT 的逆操作**。CLT 告诉我们"所有信号最终归于高斯"；Diffusion 告诉我们"反过来，所有高斯都可以剥回信号"。**生成模型的整个根，就埋在 1920 年代那个 Lindeberg–Lévy 定理里。**

底下黄色 callout 是整场的主旨：

> **我们用高斯的语言描述世界，然后用 Transformer 学会从高斯里召唤出世界。**
> **下一站，是从"生成视频"到"生成可交互的世界"。**

---

## ⏩ 翻页 → 第 26 页（Thank You · References） · 1 分钟

讲完了。最后一页是参考文献。

我特别要感谢这十篇工作，没有它们这场讲座完全讲不下去：

- **[1]** Peebles & Xie · DiT · ICCV 2023
- **[2]** Esser et al. · Scaling Rectified Flow Transformers (SD3) · ICML 2024
- **[3]** Brooks et al. · Video Generation Models as World Simulators (SORA) · OpenAI 2024
- **[4]** Huang et al. · Self-Forcing · 2025
- **[5]** Liu et al. · Stable Video Infinity · arXiv 2510.09212
- **[6]** ByteDance Seed · Seedance 1.0 · arXiv 2506.09113
- **[7]** ByteDance Seed · Seedance 1.5 pro · arXiv 2512.13507
- **[8]** Streaming AR Video Generation via Diagonal Distillation · arXiv 2603.09488
- **[9]** Causal Forcing · arXiv 2602.02214
- **[10]** Xun Huang · Towards Video World Models · blog 2025

我是赵文彪，谢谢各位 —— 欢迎提问、批评、踩雷。**慢慢来。** 🌿

---

## 时间表（备查 · 总 36 分钟）

| 章节 | 页码 | 用时 |
|---|---|---|
| 首页 | 0 | 1:00 |
| 第 I 章 · CLT | 1–4 | 5:30 |
| 第 II 章 · LM | 5–8 | 4:45 |
| 第 III 章 · Diffusion / DiT | 9–13 | 7:45 |
| 第 IV 章 · AR Video | 14–17 | 5:30 |
| 第 V 章 · Open Problems | 18–21 | 5:30 |
| 第 VI 章 · Academia vs Industry | 22–24 | 4:30 |
| 总结 + 致谢 | 25–26 | 3:00 |

如果实际超时，可以**压缩 Page 8 / Page 20 / Page 24** 到半分钟；
如果还有富余，可以在 **Page 4 / Page 21** 多展开比喻和争议。

祝顺利！💪
