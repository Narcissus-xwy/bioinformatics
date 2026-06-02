# 🤖 练习 2：AI/ML 视角下的 scRNA-seq

## 简介

你有 AI/Python 基础，因此你应该知道 scRNA-seq 的标准分析流程本质上是一系列**可替换的机器学习组件**。本练习不从"如何使用 Scanpy"的角度讲，而是从**算法选择**的角度审视每个环节：标准方法是什么，有哪些 ML 替代方案，它们各自的假设、优势和代价是什么。

> [!NOTE]
> **核心论点：** 大多数"生物信息学流程"中的步骤不是一个算法问题有一个正确答案——而是在统计偏差、计算效率和可解释性之间的权衡。理解这些权衡，你就能在不同场景下做出合理的选择。

---

## 🎯 目标

- 从 ML 角度看懂 scRNA-seq 每个步骤的算法本质
- 了解深度学习方法（VAE、对比学习、Transformer）在单细胞中的应用
- 理解 scVI、Geneformer 等工具的设计哲学
- 能够为自己的数据选择适当的分析方法

---

## 1. 降维：PCA → Autoencoder → VAE

### 1.1 标准方法：PCA

scRNA-seq 流程的第一步是 PCA。从 ML 角度看，PCA 是一个**线性、正交、无参数**的降维方法：

| 特性 | PCA | 含义 |
|---|---|---|
| 线性 | 只捕捉线性关系 | 无法处理基因调控的复杂性 |
| 正交 | 主成分之间不相关 | 便于解释但限制了表达能力 |
| 无参数 | 无需调参（除了 n_comps） | 简单且可重复 |
| 全局 | 一个变换应用于所有细胞 | 无法捕捉局部结构 |

```python
# PCA 的本质（简化）
from sklearn.decomposition import PCA

pca = PCA(n_components=50)
# adata.X 是 (n_cells, n_genes) 的稠密矩阵
pca_embedding = pca.fit_transform(scale(adata.X))  
# pca.components_ 是 (50, n_genes) → 每个 PC 是基因的加权组合
```

**优点：** 可解释性强（PC 载荷可以分析哪些基因贡献大）、计算快、可复现。**缺点：** 线性假设是强约束——无法分离非线性结构的细胞群（如发育轨迹中的分支点）。

### 1.2 深度替代：Autoencoder

```text
标准 PCA:        X → WX         (线性投影)
Autoencoder:     X → f(WX + b)  (非线性映射)
 其中 f = ReLU / Tanh / GELU
```

```python
# 一个简单的 scRNA-seq 自编码器（示意图，非实际代码）
import torch.nn as nn

class scAutoencoder(nn.Module):
    def __init__(self, n_genes=2000, latent_dim=32):
        super().__init__()
        self.encoder = nn.Sequential(
            nn.Linear(n_genes, 256),
            nn.BatchNorm1d(256),
            nn.ReLU(),
            nn.Dropout(0.2),
            nn.Linear(256, 128),
            nn.ReLU(),
            nn.Linear(128, latent_dim),   # 瓶颈层
        )
        self.decoder = nn.Sequential(
            nn.Linear(latent_dim, 128),
            nn.ReLU(),
            nn.Linear(128, 256),
            nn.ReLU(),
            nn.Linear(256, n_genes),
        )
    
    def forward(self, x):
        z = self.encoder(x)        # 压缩表示
        x_recon = self.decoder(z)  # 重构
        return x_recon
    
    def get_latent(self, x):
        return self.encoder(x)     # 用于下游分析的降维表示
```

**Autoencoder 的优势：**
- 非线性 → 可以解开 PCA 无法分离的细胞群
- 降维效果通常在聚类任务上优于 PCA
- 可以堆叠更多层捕捉层次结构

**Autoencoder 的问题：**
- 确定性编码 → 不知道每个细胞在 latent space 中的不确定性
- 容易过拟合 → 需要正则化（Dropout, BatchNorm, L2）
- 瓶颈维度需要通过调参确定（不像 PCA 可以看肘部图）

### 1.3 概率方法：VAE（Variational Autoencoder）

VAE 将每个细胞编码为**概率分布**（均值 + 方差）而不是一个点：

```text
PCA:       x → z                   (点估计)
AE:        x → z = f(x)            (点估计，非线性)
VAE:       x → μ(x), σ(x) → z ~ N(μ, σ²I)  (分布估计)
                ↓
        你可以说："细胞 A 在 latent space 中的位置是 μ，置信区间为 ±1.96σ"
```

这就是 **scVI**（single-cell VAE）的核心思想：

```python
# scVI 的概念架构
# 实际使用：scvi.model.SCVI(adata)
class scVI_Conceptual(nn.Module):
    """scVI 的概念简化版"""
    def __init__(self, n_genes, n_latent=10):
        super().__init__()
        # 编码器输出均值和方差
        self.encoder_mean = nn.Linear(n_genes, 128).ReLU().Linear(128, n_latent)
        self.encoder_logvar = nn.Linear(n_genes, 128).ReLU().Linear(128, n_latent)
        self.decoder = nn.Linear(n_latent, 256).ReLU().Linear(256, n_genes)
    
    def forward(self, x):
        # 从编码分布采样
        mu = self.encoder_mean(x)
        logvar = self.encoder_logvar(x)
        z = mu + torch.randn_like(mu) * torch.exp(0.5 * logvar)  # 重参数化技巧
        return self.decoder(z), mu, logvar
        # 损失 = 重构损失 + KL散度（正则化 latent space）
```

**scVI 的核心贡献不仅仅是 VAE，还在于：**
- 使用 **ZINB（零膨胀负二项）分布**作为输出层，显式建模 scRNA-seq 的 dropout 特性
- 在编码器中加入**批次信息**作为条件变量 → 同时完成批次校正
- 提供**概率化的基因表达推断** → 可以对缺失值进行插补

> [!NOTE]
> **ZINB 分布为什么适合 scRNA-seq？**
> ```
> scRNA-seq 的计数分布有两个特点：
> 1. 过度离散（overdispersion）：方差 > 均值 → 用 Negative Binomial 建模
> 2. 过多零值（dropout）：技术原因导致的零表达 → 用 Zero Inflation 建模
> 
> P(X = x) = π · δ₀(x) + (1 - π) · NB(x | μ, θ)
>             ↑                ↑
>         dropout 概率    负二项计数部分
> ```
> 标准分析（log1p 归一化）隐含地假设了高斯分布。scVI 的 ZINB 输出层更贴近数据的真实生成过程。这意味着你**不应**对输入 scVI 的数据做 log1p 归一化——它期望原始计数。

### 1.4 一般性建议

| 场景 | 推荐方法 | 理由 |
|---|---|---|
| 简单数据集，已知细胞类型 | PCA | 快、可解释、足够用 |
| 复杂发育轨迹，连续过渡 | scVI / VAE | 非线性能更好捕捉连续状态 |
| 大规模数据（>100k 细胞） | PCA → scVI | 先用 PCA 降维到 50 维，再跑 scVI |
| 需要统计不确定性 | VAE / scVI | 概率编码提供置信度 |
| 可解释性优先 | PCA | AE/VAE 的 latent dimensions 难以解释 |

---

## 2. 聚类：Leiden vs K-means vs HDBSCAN

### 2.1 标准方法：Leiden 算法

Leiden 是一个**图聚类**算法，在 kNN 图上运行：

```text
构建 kNN 图：    每个细胞是节点，连接到最近的 k 个邻居
         ↓
模块度优化：      Louvain/Leiden 算法迭代优化分区
         ↓
分辨率参数：      控制簇的数量和粒度
```

**Leiden vs K-means：**

| 特性 | K-means | Leiden |
|---|---|---|
| 假设 | 簇是凸形的、各向同性的 | 无形状假设（通过图结构） |
| 参数 | K（簇数） | 分辨率（间接控制簇数） |
| 分配方式 | 硬分配（概率为 0 或 1） | 硬分配（但可重叠） |
| 稳定性 | 初始化依赖，需多次运行 | 更稳定 |
| 处理稀疏性 | 在高维空间表现差 | 图结构天然适合稀疏数据 |
| 速度 | O(n) | O(n log n) |

```python
# K-means 可以替代 Leiden——但并不总是更好
from sklearn.cluster import KMeans, MiniBatchKMeans

# K-means 在 UMAP 嵌入上（不推荐——应该在高维空间做）
kmeans = KMeans(n_clusters=8, random_state=42)
adata.obs['kmeans_8'] = kmeans.fit_predict(adata.obsm['X_pca'])

# MiniBatchKMeans 适合大规模数据
mbk = MiniBatchKMeans(n_clusters=8, batch_size=1024, random_state=42)
adata.obs['mbk_8'] = mbk.fit_predict(adata.obsm['X_pca'])
```

### 2.2 密度方法：HDBSCAN

HDBSCAN 不要求所有细胞都被分配到某个簇——可以标记为**噪声**（即不属于任何簇）：

```python
import hdbscan

# HDBSCAN 可以发现任意形状的簇，并标记离群点
clusterer = hdbscan.HDBSCAN(min_cluster_size=30, min_samples=5)
adata.obs['hdbscan'] = clusterer.fit_predict(adata.obsm['X_pca'])

# 检查有多少细胞被标记为噪声（标签 = -1）
noise_pct = (adata.obs['hdbscan'] == -1).mean() * 100
print(f'噪声比例：{noise_pct:.1f}%')
```

**HDBSCAN 什么时候有用？**
- 数据中包含大量"过渡态"细胞（如分化轨迹中的中间态）
- 怀疑某些细胞群不是离散的而是连续变化的
- 想要保守的聚类——只在高密度区域形成簇

**HDBSCAN 的代价：**
- 计算复杂度 O(n²) 在最坏情况下，不适合超大规模数据
- 参数 `min_cluster_size` 和 `min_samples` 比 Leiden 的分辨率更难调
- 许多细胞被标记为噪声，需要后续处理

### 2.3 方法选择对比实验

```python
# 比较不同聚类方法的结果
from sklearn.metrics import adjusted_rand_score as ari

# 在 PBMC 数据上比较 Leiden (res=0.8) vs K-means (k=8) vs HDBSCAN
clusters = {
    'Leiden': adata.obs['leiden'],
    'KMeans_8': adata.obs['kmeans_8'],
    'HDBSCAN': adata.obs['hdbscan'],
}

# 两两计算 ARI（调整兰德指数）
for m1 in clusters:
    for m2 in clusters:
        if m1 < m2:
            print(f'{m1} vs {m2}: ARI = {ari(clusters[m1], clusters[m2]):.3f}')
```

> [!IMPORTANT]
> 没有"最好"的聚类算法——只有最适合你的**生物学问题**和数据质量的算法。如果你期望离散的细胞类型（如 PBMC），Leiden 是合理选择。如果你期望连续的发育轨迹（如类器官分化），HDBSCAN 或更先进的轨迹推断方法可能更合适。

---

## 3. 批次校正：深度学习视角

### 3.1 问题定义

当你在不同实验批次中测量时，技术差异会混入数据：

```text
批次 A:    ●●○●●○●●○   ← 条件 X
批次 B:    ●●○●●○●●○   ← 条件 Y
           ↑ 批次效应 → 条件 Y 的细胞看起来像另一个条件
```

### 3.2 方法谱系

```text
批次校正方法的时间线：

2015: ComBat         ← 经验贝叶斯，位置-尺度调整（来自微阵列时代）
2017: MNN Correct    ← 相互最近邻配对，保持生物学变异
2018: Harmony        ← 在 PCA 空间中迭代混合，R 主导
2019: scVI           ← VAE，以批次为条件变量，ZINB 输出
2020: scANVI         ← 半监督 scVI，使用部分已知的细胞类型标签
2021: SCALEX         ← 在线学习，无需数据的"整合"
2022: 对比学习方法   ← 使用对比损失对齐批次的表示
```

**关键差异：**

| 方法 | 类型 | 保留全局结构？ | 保留稀有群体？ | 需要原始计数？ |
|---|---|---|---|---|
| ComBat | 统计 | 是 | 否 | 是 |
| Harmony | 统计+迭代 | 是 | 中等 | 否 |
| scVI | 深度生成 | 是 | 是（概率编码） | 是（ZINB） |
| scANVI | 半监督深度 | 是 | 是 | 是 |
| BBKNN | 图 | 否（每个批次单独聚类） | 否 | 否 |

### 3.3 为什么 scVI 在批次校正上表现好？

```
标准方法（如 Harmony）：找出每个批次细胞的偏移并纠正它。
                          假设：批次效应是简单的平移/缩放。
                          
scVI 的方法：              将批次 ID 作为条件输入 VAE。
                          解码器学习：P(x | z, batch)。
                          
                          在 latent space z 中，批次效应被"条件化掉"了——
                          模型学会对 same biological state 生成
                          依赖于 batch 的观测计数。
                          
                          这类似于控制变量回归，但更加灵活。
```

```python
# scVI 的实际使用
import scvi

# 设置
scvi.model.SCVI.setup_anndata(adata, batch_key='batch')

# 创建和训练模型
model = scvi.model.SCVI(adata, n_latent=10, n_layers=2)
model.train()

# 获取批次校正后的表示
adata.obsm['X_scVI'] = model.get_latent_representation()

# 后续可以用 scVI 的 latent 做 UMAP 和聚类
sc.pp.neighbors(adata, use_rep='X_scVI')
sc.tl.umap(adata)
sc.pl.umap(adata, color=['cell_type', 'batch'])
```

> [!TIP]
> **如何评估批次校正效果？**
> 好的批次校正：不同批次的**相同细胞类型**混合在一起（`batch` 在 UMAP 上无结构），但**不同细胞类型**保持分离。
> 坏的批次校正：过度校正——相同细胞类型的不同样本仍然分离，或不同细胞类型被强行混合。
>
> 定量指标：**iLISI**（批次混合度，越高越好）vs **cLISI**（细胞类型分离度，越低越好）。

---

## 4. 基础模型（Foundation Models）：范式转变

### 4.1 从传统流程到预训练模型

2023-2025 年，单细胞领域经历了一场类似于 NLP 的"BERT 时刻"——各种大规模预训练模型的出现：

```
传统方法：      数据 → 预处理 → 降维 → 聚类 → 注释
               （每个数据集独立分析，没有知识迁移）

基础模型：      大量训练数据 → 预训练模型 → 零样本/微调
               （知识从大规模数据迁移到新数据集）
```

### 4.2 代表性模型

| 模型 | 年份 | 架构 | 预训练数据 | 核心能力 |
|---|---|---|---|---|
| **Geneformer** | 2023 | 6-layer Transformer | 30M 人类单细胞 | 零样本基因调控预测 |
| **scGPT** | 2024 | GPT 架构 | 33M 人类单细胞 | 整合、注释、扰动预测 |
| **scFoundation** | 2024 | 非线性 AE + Transformer | 50M 人类单细胞 | 基因表达嵌入，药物响应 |
| **CellPLM** | 2024 | 对比学习 | 100M+ 细胞 | 跨物种、跨模态对齐 |

### 4.3 Geneformer 核心设计

```python
# Geneformer 的概念架构

# 1. 输入：将每个细胞的基因表达转换为"基因排名序列"
#    每个基因根据其在一个细胞中的表达水平排序
#    输入序列：[CD3D, CD8A, NKG7, ..., 被检测到的最不表达的基因]
#                                             ↑
#                                    这类似于 BERT 的 token 序列

# 2. 架构：6 层 Transformer Encoder（类似 BERT）
#    每个"基因 token"学习上下文感知的表示

# 3. 预训练任务：掩码基因预测（类似 BERT 的 MLM）
#    随机掩盖 15% 的基因，预测被掩盖的基因身份
#    → 模型学到基因共表达网络（co-expression network）

# 4. 微调：在特定任务上（如细胞类型分类）添加分类头
```

**Geneformer 的关键洞察：**
- 使用 rank（排名）而非 raw value（原始值） → 对批次效应和测序深度鲁棒
- 自注意力机制可以捕捉长程基因-基因交互
- 预训练后在少量标注数据上微调即可取得领先结果

### 4.4 基础模型的实际价值评估

```python
# 使用 Geneformer 进行零样本细胞类型预测（伪代码）
# 实际需要: pip install geneformer

from geneformer import Classifier

# 加载预训练模型
classifier = Classifier.from_pretrained(
    "gf-6L-30M-i2048",
    num_classes=0  # 零样本模式
)

# 直接预测细胞类型（无需在自己的数据上训练）
# predictions = classifier.predict(adata)
```

**对于你的研究方向（AI + 单细胞 + 类器官），基础模型的价值在：**

| 应用场景 | 传统方法 | 基础模型方法 | 优势 |
|---|---|---|---|
| 细胞类型注释 | 先聚类 → 手动查标记基因 | 零样本预测 | 无需手动注释，速度快 |
| 稀有群体发现 | 容易忽略 | 语义感知的嵌入 | 预训练知识包含稀有群体 |
| 基因扰动预测 | 需要实验 | Geneformer 推断 | 省钱省时间 |
| 药物响应 | 需要实验验证 | 模型预测 | 加速筛选过程 |

> [!WARNING]
> 基础模型并非万能。当前（2025-2026）的限制包括：
> 1. **物种偏差**：大多数模型基于人类数据，跨物种泛化有限
> 2. **组织偏差**：预训练数据偏向血液/免疫系统，类器官数据代表性不足
> 3. **批次效应处理**：基础模型无法完全替代专门的批次校正方法
> 4. **计算资源**：运行推理需要 GPU（虽然可以在 Colab 完成）
> 5. **可解释性**：Transformer 的黑盒性质使生物学验证更具挑战性

---

## 5. 你的 AI 工具箱

将你的 ML 技能应用于单细胞分析：

### 5.1 你可以立即做的对比

```python
# 在同一个数据上练习不同方法
import scanpy as sc
import scvi

# 对比 1：PCA (线性) vs scVI (VAE) 的 latent representation
ada = sc.datasets.pbmc3k()

# PCA baseline
sc.pp.normalize_total(ada, target_sum=1e4)
sc.pp.log1p(ada)
sc.pp.highly_variable_genes(ada, n_top_genes=2000)
ada = ada[:, ada.var.highly_variable]
sc.pp.scale(ada, max_value=10)
sc.tl.pca(ada, n_comps=10)

# scVI
scvi.model.SCVI.setup_anndata(ada)
model = scvi.model.SCVI(ada, n_latent=10)
model.train(max_epochs=100)
ada.obsm['X_scVI'] = model.get_latent_representation()

# 可视化对比
fig, axes = plt.subplots(1, 2, figsize=(12, 5))
sc.pl.embedding(ada, basis='X_pca', color='MS4A1', ax=axes[0], show=False, title='PCA')
sc.pl.embedding(ada, basis='X_scVI', color='MS4A1', ax=axes[1], show=False, title='scVI (VAE)')
plt.show()
```

### 5.2 对你有价值的 AI-in-scRNA 研究方向

| 方向 | 问题 | 适合你的切入点 |
|---|---|---|
| **类器官建模** | 用 VAE 学习类器官分化轨迹的 latent dynamics | 你的 domain 知识 + ML |
| **基因调控网络推断** | Transformer 注意力权重 → GRN | 你熟悉 attention |
| **多模态整合** | RNA + 蛋白 + 染色质可及性 → 跨模态对齐 | 对比学习方法 |
| **药物扰动响应预测** | 给定基因表达，预测药物效果 | 回归/分类 |
| **空间转录组学** | 将 scRNA-seq 映射到组织空间位置 | 图神经网络 |

---

## 总结

| 流程步骤 | 标准方法 | ML 替代 | 何时考虑替代 |
|---|---|---|---|
| 降维 | PCA | scVI (VAE), Autoencoder | 非线性结构明显、需要概率表示 |
| 聚类 | Leiden | HDBSCAN, K-means | 连续过渡数据、大规模数据（MiniBatchKMeans） |
| 批次校正 | Harmony, MNN | scVI, scANVI | 复杂批次结构、需要概率处理 |
| 细胞类型注释 | 手动+标记基因 | Geneformer, scGPT | 想自动化、处理稀有群体 |
| 差异表达 | DESeq2, edgeR | 模型比较 (scVI 后验) | 非标准实验设计 |

> [!TIP]
> **一句话总结：** scRNA-seq 标准流程（PCA → Leiden → 手动注释）是 2015-2020 年积累的最佳实践，适用于大多数场景。深度学习方法（scVI、Geneformer）在特定场景下有明确优势，但增加了计算成本和复杂性。**做基础分析用标准流程，深入探索加 ML 方法。**

---

## 参考资源

| 资源 | 链接 |
|---|---|
| scVI 文档 | https://docs.scvi-tools.org/ |
| Geneformer | https://huggingface.co/ctheodoris/Geneformer |
| scGPT | https://github.com/bowang-lab/scGPT |
| Theis Lab 综述（ML in single-cell） | https://www.nature.com/articles/s41592-021-01336-8 |
