# 🧫 练习 3：类器官 × 单细胞转录组——延伸分析

## 引言

类器官是从干细胞（iPSC、成体干细胞或胚胎干细胞）在 3D 培养中自组织形成的**微型器官**。它们模拟真实器官的结构、细胞类型组成和功能，同时可以在培养中扩增、冷冻、基因编辑和药物筛选。

**scRNA-seq 在类器官研究中的独特价值：**

```
类器官 vs 真实组织:
                    类器官                          真实组织
             ┌─────────────────┐            ┌─────────────────┐
             │  iPSC → 培养 30天 │            │   人体/小鼠组织  │
             │  成本：$500/次   │            │   成本：$5000+   │
             │  变异：批次内较小│            │   变异：个体间大 │
             │  伦理：无        │            │   伦理：有       │
             └─────────────────┘            └─────────────────┘
                    │                               │
         scRNA-seq 回答：                   scRNA-seq 回答：
         ① 类器官包含哪些细胞类型？         ① 组织在体内如何组成？
         ② 分化效率如何？                   ② 疾病状态 vs 健康状态？
         ③ 与真实组织有多相似？             ③ 细胞-细胞相互作用？
         ④ 药物处理如何改变细胞组成？       ④ 空间组织？
```

---

## 🎯 目标

- 理解 scRNA-seq 在类器官研究中的核心应用场景
- 学会比较类器官和真实组织的单细胞数据
- 掌握轨迹推断（pseudotime / RNA velocity）的基本概念
- 能够评估类器官的"成熟度"和细胞类型组成

---

## 1. 类器官 scRNA-seq 的标准分析问题

### 1.1 类器官的异质性

类器官通常包含多种细胞类型。以脑类器官为例：

```text
脑类器官中的细胞类型（按发育时间排序）：
                                       ┌── 神经祖细胞 (NPC)
                                       │     PAX6+, SOX2+
                  ┌── 端脑 ───────┬───┤
                  │               │   └── 兴奋性神经元
                  │               │         SLC17A7+, SATB2+
                  │               │
iPSC ──→ 神经外胚层 ───┤               └── 抑制性神经元
                           │                     GAD1+, DLX2+
                           │
                           └── 中脑/后脑 ──── 多巴胺能神经元
                                               TH+, LMX1A+
```

**核心问题：** 类器官中的细胞类型比例是多少？与真实胎儿/成人脑组织匹配吗？

### 1.2 类器官 vs 真实组织的对比

```python
# 概念：如何比较类器官与真实组织？
# 你需要两组数据：
# 1. 类器官 scRNA-seq（你的数据）
# 2. 参考 scRNA-seq（真实组织，如胎儿脑组织）

# 方法 A：联合分析
# 将两组数据合并，进行批次校正后联合聚类
adata_combined = adata.concat([adata_organoid, adata_primary_tissue])
# 使用 scVI 进行整合（练习 2）
# 然后看：类器官的细胞落在哪些簇中？

# 方法 B：投影（Projection）
# 用参考数据训练分类器，预测类器官细胞类型
from sklearn.ensemble import RandomForestClassifier

# 在参考数据上训练
clf = RandomForestClassifier(n_estimators=200)
clf.fit(adata_primary_tissue.obsm['X_pca'], adata_primary_tissue.obs['cell_type'])

# 预测类器官细胞类型
organoid_pred = clf.predict(adata_organoid.obsm['X_pca'])
# 置信度
organoid_prob = clf.predict_proba(adata_organoid.obsm['X_pca'])
```

> [!NOTE]
> **常见发现：** 脑类器官的细胞类型组成偏向早期发育阶段（更多 NPC，更少成熟神经元）。这是正常的——脑类器官通常对应**第一/第二孕期**的发育状态，而非成人大脑。成熟度取决于培养时长和方案。

### 1.3 评估类器官质量

```python
# 类器官质量的单细胞指标
import pandas as pd

# 计算每个样本的细胞类型比例
def celltype_composition(adata, sample_key='sample', celltype_key='cell_type'):
    """返回每个样本的细胞类型组成 DataFrame"""
    ct_counts = pd.crosstab(adata.obs[sample_key], adata.obs[celltype_key], normalize='index')
    return ct_counts

# 检查分化效率
# 例如：PAX6+ / SOX2+ 祖细胞比例 vs 成熟神经元比例
progenitor_genes = ['PAX6', 'SOX2', 'NES']
neuron_genes = ['RBFOX3', 'MAP2', 'SYN1']

# 计算每个细胞的祖细胞/神经元评分
adata.obs['progenitor_score'] = adata[:, progenitor_genes].X.mean(axis=1).A1
adata.obs['neuron_score'] = adata[:, neuron_genes].X.mean(axis=1).A1

# 分化效率 = 神经元评分高的细胞比例
# （这是一个代理指标，非正式方法）
```

---

## 2. 轨迹推断：类器官分化的时间维度

### 2.1 从静态快照推断动态

类器官培养的优势在于你可以**在不同时间点**取样。但你也可以在**单个时间点**通过计算推断分化轨迹。

```text
单时间点 scRNA-seq 的数据：

细胞 A: 高表达 PAX6, SOX2    → 神经祖细胞（早期）
细胞 B: 中度 PAX6, 开始 NEUROD1 → 中间祖细胞（过渡）
细胞 C: 高表达 TUBB3, MAP2   → 神经元（晚期）
细胞 D: 高表达 PAX6, SOX2    → 另一个神经祖细胞
细胞 E: 高表达 TUBB3, MAP2   → 另一个神经元
...

推断的轨迹：
    祖细胞 ──→ 中间祖细胞 ──→ 神经元
     A, D        B           C, E
```

这就是**伪时间（pseudotime）**分析的核心假设：在单时间点取样中，不同细胞处于不同的分化阶段，我们可以通过其基因表达模式排序它们。

### 2.2 使用 Diffusion Pseudotime

```python
# 在练习 1 的 PBMC 分析基础上，我们看一个类似的概念
# 实际上要分析分化轨迹，需要使用更专门的工具

# Scanpy 内置的简单的 pseudotime: diffusion pseudotime (DPT)
# 注意：DPT 假设数据在 UMAP 上形成树状结构

# 如果是分化数据，可以尝试：
# sc.tl.dpt(adata, n_branchings=1)  # DPT
# sc.tl.paga(adata)                   # PAGA（分区图抽象）

# 实际上对于类器官分化，推荐使用：
# - scVelo: RNA velocity（需要 spliced/unspliced 计数）
# - Monocle 3: 主流轨迹推断工具
# - Palantir: 对连续分化效果好
```

### 2.3 RNA Velocity

RNA Velocity 利用 pre-mRNA（未剪接）和 mature mRNA（已剪接）的比例来推断基因表达的变化方向和速率：

```python
# scVelo 的概念流程
import scvelo as scv

# 需要 spliced/unspliced 计数矩阵
# 从 Cell Ranger 或 STARsolo 的输出中获取
# adata.layers['spliced'], adata.layers['unspliced']

# 计算 velocity
scv.pp.filter_genes(adata)
scv.pp.normalize_per_cell(adata)
scv.pp.filter_genes_dispersion(adata, n_top_genes=2000)
scv.tl.velocity(adata, mode='stochastic')
scv.tl.velocity_graph(adata)

# 可视化 velocity
scv.pl.velocity_embedding_stream(adata, basis='umap')
```

**RNA Velocity 对类器官研究的价值：**

```text
子类                                               父类
          ↓
     ┌──────────┐
     │ 你现在在  │        ← 细胞当前状态
     │ 这里     │
     └────┬─────┘
          │ velocity 预测的方向
          ▼
     ┌──────────┐
     │ 你将要    │        ← 分化方向预测
     │ 去这里    │
     └──────────┘
```

> [!TIP]
> RNA Velocity 需要特定的数据格式（包含 spliced/unspliced 计数矩阵）。10x Genomics 的 Cell Ranger 和 STARsolo 都输出 split 计数。如果你自己处理 FASTQ，推荐使用 **kallisto | bustools** 或 **alevin-fry**。

---

## 3. 实践：分析公开类器官数据集

### 3.1 可用的公开数据

以下是你可以下载和分析的类器官 scRNA-seq 数据集：

| 数据集 | 类器官类型 | 数据位置 | 大小 |
|---|---|---|---|
| Velasco 2019 (Nature) | 脑类器官（3个月时间线） | GEO: GSE129519 | 20k 细胞 |
| Lancaster 2017 | 脑类器官 | ArrayExpress: E-MTAB-6808 | 5k 细胞 |
| Triana 2021 | 肠道类器官 | GEO: GSE153937 | 30k 细胞 |
| **Trevino 2021** | **人脑类器官（胎儿对比）** | **GEO: GSE162170** | **60k 细胞** |
| Sridhar 2021 | 视网膜类器官 | GEO: GSE148166 | 15k 细胞 |

> [!TIP]
> **推荐：Trevino 2021 数据（GSE162170）**
> 这个数据集同时包含**脑类器官**和**胎儿脑组织**的 scRNA-seq 数据，非常适合练习类器官 vs 真实组织的对比分析。通过 SRA 或 GEO 下载计数矩阵后，可以直接用 Scanpy 或 scVI 加载。

### 3.2 下载和分析示例

```python
# 从 GEO 下载 Trevino 2021 处理后的计数矩阵
# 实际路径需要从 GEO 页面获取
# 这里给出概念性的流程

import scanpy as sc
import pandas as pd
import matplotlib.pyplot as plt

# 方法 1：从 GEO 下载 h5ad 文件（如果作者提供）
# url = "https://ftp.ncbi.nlm.nih.gov/geo/series/GSE162nnn/GSE162170/suppl/"
# 或使用 Hugging Face Dataset 平台

# 方法 2：从 10x 格式的 mtx 文件加载
# adata = sc.read_10x_mtx('filtered_feature_bc_matrix/')

# 加载后，元数据通常包含：
# adata.obs['sample']      → 样本 ID（包含器官oid或胎脑标记）
# adata.obs['timepoint']   → 培养天数（如 D28, D56, D90）
# adata.obs['cell_type']   → 预注释的细胞类型

# 分析步骤（与练习 1 相同的工作流 + 额外的对比分析）
def compare_organoid_vs_tissue(adata):
    """类器官 vs 真实组织的对比分析"""
    
    # 1. 按来源分组
    sc.pl.umap(adata, color='sample', legend_loc='on data')
    
    # 2. 检查细胞类型组成差异
    ct_by_source = pd.crosstab(
        adata.obs['tissue_source'],  # 'organoid' vs 'fetal_brain'
        adata.obs['cell_type'],
        normalize='index'
    )
    
    # 可视化
    ct_by_source.T.plot(kind='bar', figsize=(10, 5))
    plt.title('细胞类型比例：类器官 vs 胎脑组织')
    plt.ylabel('比例')
    plt.xticks(rotation=45, ha='right')
    plt.legend(title='来源')
    plt.tight_layout()
    plt.show()
    
    # 3. 检查成熟度
    # 早期标记 vs 晚期标记的表达差异
    early_genes = ['PAX6', 'SOX2', 'HES1']     # 神经祖细胞
    late_genes = ['SATB2', 'BCL11B', 'NEUROD2']  # 成熟神经元
    
    for ct in ['organoid', 'fetal_brain']:
        subset = adata[adata.obs['tissue_source'] == ct]
        early_expr = subset[:, early_genes].X.mean()
        late_expr = subset[:, late_genes].X.mean()
        print(f'{ct}: 早期标记 = {early_expr:.3f}, 晚期标记 = {late_expr:.3f}')
```

### 3.3 关键分析问题

当你拿到类器官 scRNA-seq 数据时，以下问题可以帮助你组织分析：

| 问题 | 分析方法 | 生物学含义 |
|---|---|---|
| 类器官包含哪些细胞类型？ | 聚类 + 标记基因注释 | 分化方案是否成功产出目标细胞 |
| 细胞类型比例合理吗？ | 组成分析 | 是否过度偏向某种细胞类型 |
| 与真实组织有多相似？ | 联合嵌入 + 投影 | 类器官的生理相关性 |
| 类器官有多"成熟"？ | 伪时间分析 + 成熟标记对比 | 类器官处于哪个发育阶段 |
| 不同培养条件有何影响？ | 差异细胞类型比例 + DE | 优化培养方案 |
| 药物处理改变了什么？ | 差异丰度 + 差异表达 | 药物筛选结果 |

---

## 4. 实际案例分析：类器官分化效率评估

假设你培养了两种条件下的脑类器官：

- **方案 A**：标准培养（对照）
- **方案 B**：添加了 WNT 激活剂

你想知道 scRNA-seq 如何帮你评估差异：

### 4.1 细胞类型组成分析

```python
# 假设 adata 包含两种条件的类器官数据
# adata.obs['condition']: 'A' or 'B'
# adata.obs['cell_type']: 已注释

composition = pd.crosstab(
    adata.obs['condition'], 
    adata.obs['cell_type'],
    normalize='index'
)
print(composition)

# 统计检验：细胞类型比例在不同条件间有显著差异吗？
from scipy.stats import chi2_contingency

# 列联表卡方检验
contingency = pd.crosstab(adata.obs['condition'], adata.obs['cell_type'])
chi2, p_val, dof, expected = chi2_contingency(contingency)
print(f'细胞类型组成差异 p-value: {p_val:.2e}')
```

### 4.2 差异基因表达

```python
# 伪批量方法（Pseudobulk）：在同种细胞类型内对比条件
# 为什么不用"每个细胞做差异表达"？因为单细胞不是独立的！

# 伪批量步骤
# 1. 对同一样本、同一细胞类型的计数求和
# 2. 用 DESeq2 / edgeR 做差异表达
# 3. 解释结果

# 概念代码（使用 pseudobulk + DESeq2-like 逻辑）
def pseudobulk_de(adata, celltype, condition_key='condition'):
    """在指定细胞类型内进行伪批量差异表达"""
    
    # 筛选目标细胞类型
    subset = adata[adata.obs['cell_type'] == celltype].copy()
    
    # 伪批量：按样本求和
    pseudo = pd.DataFrame(
        subset.X.toarray() if hasattr(subset.X, 'toarray') else subset.X,
        index=subset.obs_names,
        columns=subset.var_names
    )
    pseudo['sample'] = subset.obs[condition_key].values
    bulk = pseudo.groupby('sample').sum()
    
    return bulk  # 可以输入到 DESeq2（R 包）

# 差异表达基因可以回答：
# 方案 B 是否激活了特定的分化通路？
# 哪些基因在方案 B 的神经元中上调？
```

---

## 5. 针对你的方向的具体建议

### 5.1 AI + 类器官的单细胞切入点

结合你的 AI 背景和类器官/单细胞兴趣，以下是几个有潜力的方向：

**方向 1：类器官成熟度预测模型**
- 问题：给定 scRNA-seq 数据，类器官处于哪个发育阶段？
- 方法：用胎儿组织数据训练回归模型，预测类器官的"发育年龄"
- 技术：伪时间作为 label，使用岭回归 / GAM / 神经网络

**方向 2：分化轨迹的潜在动力学**
- 问题：能否在 latent space 中建模类器官细胞的分化路径？
- 方法：VAE + ODE（神经 ODE）建模 latent dynamics
- 代表工作：**Latent ODEs** (Chen et al.), **VeloAE** (Qiao et al.)

**方向 3：类器官药物的基于 ML 的筛选**
- 问题：药物处理后，类器官的细胞组成如何变化？
- 方法：使用 Perturbation AE 预测药物效果
- 代表工作：**CPA** (Lotfollahi et al.), **CellOT** (Bunne et al.)

**方向 4：类器官-真实组织的域适应**
- 问题：类器官数据 vs 真实组织数据存在分布偏移——如何迁移知识？
- 方法：对抗域适应 / 对比学习对齐
- 技术：类似 Domain Adversarial Neural Networks (DANN)

### 5.2 推荐阅读

| 论文 | 为什么重要 |
|---|---|
| **Trevino et al. 2021** Nature | 脑类器官 + 胎儿脑的配对 scRNA-seq，分析范式模板 |
| **Lancaster & Knoblich 2014** Nature | 脑类器官的原始方法论文 |
| **Velasco et al. 2019** Nature | 脑类器官长期培养（3个月）的单细胞时间线 |
| **Lotfollahi et al. 2022** (scGen/scVI) | VAE 在单细胞扰动预测中的代表性工作 |
| **Heumos et al. 2023** Nat Rev Genet | 单细胞深度学习综述（最佳综述） |

---

## 总结

```text
类器官 + scRNA-seq 分析工作流

实验设计 → 培养 → 单细胞测序
                 ↓
            ┌──────────┐
            │ QC + 归一化│    ← 练习 1 的流程
            └────┬─────┘
                 ↓
            ┌──────────┐
            │ 聚类 + 注释 │    ← 确定细胞类型
            └────┬─────┘
                 ↓
            ┌──────────────┐
            │ 与真实组织对比 │    ← 评估生理相关性（练习 3）
            └────┬─────────┘
                 ↓
            ┌──────────────┐
            │ 轨迹推断      │    ← 理解分化路径（练习 3）
            └────┬─────────┘
                 ↓
            ┌──────────────┐
            │ 比较条件      │    ← 药物/基因编辑影响（练习 3）
            └────┬─────────┘
                 ↓
            ┌──────────────┐
            │ ML 建模       │    ← 你的 AI 视角（练习 2）
            └──────────────┘
```

> [!NOTE]
> 这个练习没有提供可以直接运行的 Python 脚本，因为类器官数据集的下载和处理涉及较多数据管理操作。如果你有具体的类器官 scRNA-seq 数据集（或想从某个公开数据开始），告诉我，我可以帮你写出完整的数据加载到分析的流程。

---

## 参考资源

| 资源 | 链接 |
|---|---|
| 人类细胞图谱 (HCA) | https://data.humancellatlas.org/ |
| GEO 类器官数据集 | https://www.ncbi.nlm.nih.gov/geo/ -> search "organoid scRNA-seq" |
| Single Cell Portal (Broad) | https://singlecell.broadinstitute.org/ |
| scVelo (RNA velocity) | https://scvelo.readthedocs.io/ |
| DPT / PAGA (轨迹推断) | https://scanpy.readthedocs.io/ |
