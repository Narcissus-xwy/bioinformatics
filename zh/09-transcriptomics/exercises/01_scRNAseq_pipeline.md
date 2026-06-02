# 🔬 练习 1：单细胞 RNA-Seq 核心分析流程

## 简介

本练习使用 **Scanpy**（Python 单细胞分析标准库）在真实数据集上完成完整的 scRNA-seq 分析：从计数矩阵到细胞类型注释。你将有 AI/Python 基础，因此我们不会花时间解释变量、循环或函数——而是直击分析本身。

我们将使用 **10x Genomics 的 PBMC 3k 数据集**（外周血单核细胞，约 3,000 个细胞），这是 scRNA-seq 领域最常见的教学数据集。

> [!NOTE]
> **整体工作流**
>
> ```
> 原始计数矩阵 (raw counts)
>         │
>         ▼
>   QC 过滤                ← 去除死细胞、双细胞、空液滴
>         │
>         ▼
>   归一化 + 对数化         ← 将计数标准化为可比尺度
>         │
>         ▼
>   高变基因检测            ← 选择信息量最大的基因
>         │
>         ▼
>   PCA 降维               ← 线性降维，作为后续步骤的基础
>         │
>         ▼
>   UMAP / t-SNE           ← 非线性可视化降维
>         │
>         ▼
>   Leiden 聚类            ← 无监督识别细胞群
>         │
>         ▼
>   标记基因鉴定            ← 每个簇的特征基因
>         │
>         ▼
>   细胞类型注释            ← 将簇映射到已知细胞类型
> ```

---

## 🎯 目标

- 理解 scRNA-seq 分析的标准流程
- 学会使用 Scanpy 操作 AnnData 对象
- 掌握 QC 决策：哪些细胞和基因需要过滤
- 理解 PCA → UMAP → Leiden 聚类的级联逻辑
- 通过标记基因推断细胞身份

---

## 📦 环境准备

**在 Google Colab 中运行**（无需本地安装）：

```python
# 安装依赖（Colab 中 Python 3.10+）
# scikit-misc 是 flavor='seurat_v3' 所需的 LOESS 回归库
!pip install scanpy[leiden] anndata matplotlib pandas numpy scikit-misc -q

import scanpy as sc
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# 设置 Scanpy 参数
sc.settings.verbosity = 2      # 日志级别: 0=不输出, 1=警告, 2=信息, 3=详细
sc.settings.set_figure_params(dpi=80, frameon=True, figsize=(6, 5))
```

---

## 第一步：加载数据

我们使用 Scanpy 内置的 PBMC 3k 数据集：

```python
# 下载并加载预处理后的计数矩阵
adata = sc.datasets.pbmc3k()

print(adata)
```

你应该看到类似输出：

```
AnnData object with n_obs × n_vars = 2700 × 32738
    var: 'gene_ids'
```

**AnnData 结构说明：**

```
AnnData
 ├── adata.obs       # 细胞 × 元数据（DataFrame）
 │   └── 行 = 细胞条形码（barcode）
 ├── adata.var       # 基因 × 元数据（DataFrame）
 │   └── 行 = 基因符号
 ├── adata.X         # 计数矩阵（细胞 × 基因），稀疏矩阵格式
 └── adata.uns       # 非结构化数据（分析结果存储于此）
```

> [!TIP]
> 对于你自己的数据，加载方式不同：
> - **10x 格式**：`sc.read_10x_mtx('path/to/matrix.mtx')`
> - **h5ad**：`sc.read_h5ad('file.h5ad')`
> - **h5 (10x 新版)**：`sc.read_10x_h5('filtered_feature_bc_matrix.h5')`

---

## 第二步：QC 过滤

### 2.1 计算 QC 指标

```python
# 计算三个核心 QC 指标
adata.var['mt'] = adata.var_names.str.startswith('MT-')  # 线粒体基因
sc.pp.calculate_qc_metrics(adata, qc_vars=['mt'], percent_top=None, log1p=False, inplace=True)

# 查看 QC 结果
print(adata.obs[['n_genes_by_counts', 'total_counts', 'pct_counts_mt']].head())
```

**QC 指标含义：**

| 指标 | 列名 | 低/高表示什么 |
|---|---|---|
| **基因数** | `n_genes_by_counts` | 每个细胞检测到的基因数量 |
| **UMI 总数** | `total_counts` | 每个细胞的测序深度 |
| **线粒体占比** | `pct_counts_mt` | 死细胞/受损细胞通常 >20% |

### 2.2 可视化过滤标准

```python
fig, axes = plt.subplots(1, 3, figsize=(12, 4))

# 基因数分布
axes[0].hist(adata.obs['n_genes_by_counts'], bins=50, color='steelblue', edgecolor='white')
axes[0].axvline(200, color='red', linestyle='--', label='min=200')
axes[0].axvline(2500, color='red', linestyle='--', label='max=2500')
axes[0].set_xlabel('基因数'); axes[0].set_ylabel('细胞数'); axes[0].legend(); axes[0].set_title('每个细胞的基因数')

# UMI 总数分布
axes[1].hist(adata.obs['total_counts'], bins=50, color='darkorange', edgecolor='white')
axes[1].set_xlabel('UMI 总数'); axes[1].set_ylabel('细胞数'); axes[1].set_title('每个细胞的 UMI 总数')

# 线粒体占比分布
axes[2].hist(adata.obs['pct_counts_mt'], bins=50, color='crimson', edgecolor='white')
axes[2].axvline(20, color='red', linestyle='--', label='max=20%')
axes[2].set_xlabel('线粒体 %'); axes[2].set_ylabel('细胞数'); axes[2].legend(); axes[2].set_title('线粒体基因占比')

plt.tight_layout()
plt.show()
```

> [!WARNING]
> 注意坐标轴：横轴是**指标的值**，纵轴是**细胞数量**。我们要找的是分布在尾部之外的细胞——不是"异常值检测算法"能告诉你的，而是需要**生物学判断**。

### 2.3 执行过滤

```python
# 标准的 PBMC 过滤参数
sc.pp.filter_cells(adata, min_genes=200)     # 去除基因数 < 200 的空液滴
sc.pp.filter_cells(adata, max_genes=2500)    # 去除可能的双细胞（双细胞通常有异常多的基因）
adata = adata[adata.obs['pct_counts_mt'] < 20, :]  # 去除线粒体占比 > 20% 的死细胞

# 过滤只在极少数细胞中表达的基因（减少噪声）
sc.pp.filter_genes(adata, min_cells=3)

print(f'过滤后：{adata.n_obs} 个细胞，{adata.n_vars} 个基因')
```

> [!IMPORTANT]
> QC 阈值**不是普适的**。一个 PBMC 的标准参数（min_genes=200, max_genes=2500, mito<20%）在你分析类器官样本时**完全不同**——类器官细胞可能有更高的基因数（>5000）和不同的线粒体基线。**始终可视化后再决定阈值。**

---

## 第三步：归一化

```python
# 步骤 A：库大小归一化（每个细胞的总计数归一化为 10,000）
# 这消除了测序深度差异
sc.pp.normalize_total(adata, target_sum=1e4)

# 步骤 B：对数变换（log1p = ln(1+x)）
# 压缩动态范围，使高表达基因不会主导后续分析
sc.pp.log1p(adata)

# 步骤 C：保存原始计数（以备某些需要原始计数的分析）
adata.raw = adata
```

**为什么先归一化再对数化？**
```
原始数据分布：    0, 0, 0, 1, 3, 150, 20000  ← 严重右偏
归一化后：         0, 0, 0, 1, 3, 150, 20000  ← 消除测序深度差异，但仍是偏态
对数化后：         0, 0, 0, 0.7, 1.4, 5.0, 9.9  ← 近似正态，适合后续统计方法
```

> [!TIP]
> 这个两步归一化（先 total 再 log1p）是 scRNA-seq 领域的事实标准，但不是唯一的。替代方案包括 **SCTransform**（R 包，使用正则化负二项回归）、**scran**（基于去卷积的归一化）。每个方法对批次效应校正和差异表达的后续表现不同。

---

## 第四步：高变基因检测

我们不需要所有 30,000+ 个基因来发现细胞类型——大多数基因在所有细胞中表达水平接近，不提供判别信息。

```python
# 检测高变基因（HVGs）
# flavor='seurat_v3' 需要 scikit-misc 库（教程开头已安装）
# 如果遇到 ImportError，运行 !pip install scikit-misc -q
sc.pp.highly_variable_genes(adata, n_top_genes=2000, flavor='seurat_v3')

# 多少基因被选为高变？
print(f'高变基因数：{adata.var["highly_variable"].sum()}')

# 绘制高变基因图
sc.pl.highly_variable_genes(adata)
```

```python
# 将数据限制为高变基因（后续所有分析仅使用这 2000 个基因）
adata = adata[:, adata.var['highly_variable']]

# 再做一个步骤：将每个基因缩放到单位方差（Z-score 标准化），对 PCA 非常重要
sc.pp.scale(adata, max_value=10)
```

> [!NOTE]
> **flavor 参数的含义：**
> - `seurat_v3`：模仿 Seurat 的 v3 方法，基于方差均值关系，推荐 >200 细胞的数据
> - `seurat`：原始 Seurat 方法
> - `cell_ranger`：基于与总表达量的离散度
> - 通常选择 2000-5000 个 HVGs 是个好的起点

---

## 第五步：降维

### 5.1 PCA

```python
# 计算 50 个主成分
sc.tl.pca(adata, n_comps=50, svd_solver='arpack')

# 可视化 PCA 结果（按几个基因着色以检查生物学意义）
sc.pl.pca(adata, color=['CST3', 'NKG7', 'PPBP'])
```

```python
# 检查 PCA 的"肘部图"——决定用多少个主成分进行后续分析
sc.pl.pca_variance_ratio(adata, n_pcs=50, log=True)
```

**如何选择 PCs 数量？**

> 肘部图显示每个主成分解释的方差比例。通常在曲线"拐点"处截断——在该点之后，每个额外 PC 解释的方差很少。对于 PBMC 数据，通常取 10-15 个 PC。这个选择影响聚类结果，因为太多 PCs 会引入噪声，太少则会丢失信号。

```python
# 基于肘部图，我们选择 15 个 PC
n_pcs = 15
```

### 5.2 UMAP

```python
# 计算邻域图（基于 PCA 空间的欧氏距离）
sc.pp.neighbors(adata, n_neighbors=10, n_pcs=n_pcs)

# 计算 UMAP 嵌入
sc.tl.umap(adata, min_dist=0.3, spread=1.0)

# 可视化
sc.pl.umap(adata, color=['CST3', 'NKG7', 'PPBP'], 
           cmap='viridis', add_outline=True, ncols=3)
```

> [!TIP]
> UMAP 参数 `min_dist` 控制点之间允许的最小距离：值越小（0.1）簇越紧凑，值越大（0.8）簇越分散。`n_neighbors` 控制局部 vs 全局结构的平衡。不必过于纠结参数——先使用默认值，后续根据生物学问题调整。

---

## 第六步：聚类

```python
# 使用 Leiden 算法进行图聚类（标准方法）
sc.tl.leiden(adata, resolution=0.8, key_added='leiden')

# 检查有多少个簇
print(f'聚类数：{adata.obs["leiden"].nunique()}')
print(adata.obs['leiden'].value_counts().sort_index())
```

```python
# UMAP 上按簇着色
sc.pl.umap(adata, color='leiden', legend_loc='on data', 
           legend_fontsize=8, palette='tab10')
```

> [!IMPORTANT]
> **分辨率（resolution）** 控制聚类粒度：值越高，簇越多。0.4 → ~8 个簇（粗粒度），0.8 → ~12 个簇（中等），1.5 → ~20 个簇（细粒度）。不存在一个"正确"的分辨率——取决于你想看到什么水平的生物学差异。通常的做法是从 0.5-1.0 开始，在已知标记基因的指导下调整。

---

## 第七步：标记基因鉴定

```python
# 计算每个簇相对于其余细胞的差异表达
# 使用 t-test 快速找到标记基因（在探索阶段足够了）
sc.tl.rank_genes_groups(adata, groupby='leiden', method='t-test', n_genes=20)

# 查看每个簇 Top 5 标记基因
sc.pl.rank_genes_groups(adata, n_genes=5, sharey=False)
```

```python
# 获取每个簇的标记基因为 DataFrame
result = adata.uns['rank_genes_groups']
markers = pd.DataFrame({
    group: result['names'][group][:10] 
    for group in result['names'].dtype.names
})
print(markers)
```

```python
# 对特定标记基因可视化
# PBMC 已知的经典细胞标记
known_markers = {
    'CD3D': 'T 细胞',
    'CD8A': 'CD8+ T 细胞',
    'CD4': 'CD4+ T 细胞',
    'NKG7': 'NK 细胞',
    'MS4A1': 'B 细胞',
    'CD14': '单核细胞/巨噬细胞',
    'FCGR3A': 'CD16+ 单核细胞',
    'PPBP': '巨核细胞/血小板',
    'CST3': '树突状细胞 (DC)',
    'JCHAIN': '浆细胞样 DC (pDC)',
    'CD79A': 'B 细胞',
    'IL7R': '记忆 CD4+ T 细胞',
    'CCR7': '初始 T 细胞'
}

sc.pl.umap(adata, color=list(known_markers.keys()), 
           cmap='viridis', ncols=4, vmax='p99')
```

---

## 第八步：细胞类型注释

根据标记基因，我们将每个簇映射到已知的 PBMC 细胞类型：

```python
# 手动定义标记基因到细胞类型的映射
cluster_to_celltype = {
    '0': 'CD4+ T 细胞 (初始/记忆)',
    '1': 'CD14+ 单核细胞',
    '2': 'NK 细胞',
    '3': 'B 细胞',
    '4': 'CD8+ T 细胞',
    '5': 'CD16+ 单核细胞',
    '6': '树突状细胞 (DC)',
    '7': '巨核细胞/血小板',
    '8': '浆细胞样树突状细胞 (pDC)',
}

# 添加到 AnnData 对象
adata.obs['cell_type'] = adata.obs['leiden'].map(cluster_to_celltype).astype('category')

# 可视化注释结果
sc.pl.umap(adata, color='cell_type', legend_loc='on data',
           legend_fontsize=7, legend_font_outline=True)
```

```python
# 验证注释：按细胞类型检查标记基因表达
sc.pl.dotplot(adata, var_names=list(known_markers.keys())[:8], 
              groupby='cell_type', dendrogram=True)
```

> [!IMPORTANT]
> 细胞类型注释是整个流程中**最需要生物学知识**的步骤。软件不会告诉你一个簇是什么——它只告诉你哪些基因在该簇中高表达。**你需要知道** CD3D 是 T 细胞标记，MS4A1 是 B 细胞标记。对于类器官或稀有种群，这可能更具挑战性，需要参考已有文献。

---

## 完整流程总结

```python
# 完整端到端流程（供参考和复用）
import scanpy as sc

# 完整分析函数
def analyze_scRNAseq(filepath='', use_pbmc3k=True, resolution=0.8, n_pcs=15):
    """完整的 scRNA-seq 分析流程"""
    
    # 1. 加载数据
    if use_pbmc3k:
        adata = sc.datasets.pbmc3k()
    else:
        adata = sc.read_10x_mtx(filepath)
    
    # 2. QC
    adata.var['mt'] = adata.var_names.str.startswith('MT-')
    sc.pp.calculate_qc_metrics(adata, qc_vars=['mt'], percent_top=None, log1p=False)
    sc.pp.filter_cells(adata, min_genes=200)
    sc.pp.filter_cells(adata, max_genes=2500)
    adata = adata[adata.obs['pct_counts_mt'] < 20, :]
    sc.pp.filter_genes(adata, min_cells=3)
    
    # 3. 归一化
    sc.pp.normalize_total(adata, target_sum=1e4)
    sc.pp.log1p(adata)
    adata.raw = adata
    
    # 4. HVG
    sc.pp.highly_variable_genes(adata, n_top_genes=2000, flavor='seurat_v3')
    adata = adata[:, adata.var['highly_variable']]
    sc.pp.scale(adata, max_value=10)
    
    # 5. 降维
    sc.tl.pca(adata, n_comps=50)
    sc.pp.neighbors(adata, n_neighbors=10, n_pcs=n_pcs)
    sc.tl.umap(adata)
    
    # 6. 聚类
    sc.tl.leiden(adata, resolution=resolution)
    
    # 7. 标记基因
    sc.tl.rank_genes_groups(adata, groupby='leiden', method='t-test')
    
    return adata

# 运行
# adata = analyze_scRNAseq()
```

---

## 进一步探索

完成基础流程后，尝试以下变体并观察结果差异：

| 实验 | 代码修改 | 预期效果 |
|---|---|---|
| 改变分辨率 | `resolution=0.2, 0.5, 1.5` | 低分辨率 → 簇更少但更可靠；高分辨率 → 发现稀有群体 |
| 改变 HVG 数量 | `n_top_genes=500` vs `5000` | 基因太少会丢失精细结构，太多会引入噪声 |
| 改变 PC 数量 | `n_pcs=5` vs `n_pcs=30` | 太少会丢失信号，太多会引入噪声 |
| 使用 tSNE 替代 UMAP | `sc.tl.tsne(adata)` | tSNE 更保守簇内距离，UMAP 更好保留全局结构 |

> [!TIP]
> **对于你的 AI 背景：** scRNA-seq 分析本质上是一个**无监督学习**流程——PCA 是线性降维、UMAP/t-SNE 是流形学习、Leiden 是图聚类。每个步骤都是一个你可以用 ML 知识理解的可替换组件。练习 2 将深入探讨替代方法。

---

## 参考资源

| 资源 | 链接 |
|---|---|
| Scanpy 官方教程 | https://scanpy.readthedocs.io/ |
| PBMC 3k 教程（官方） | https://scanpy-tutorials.readthedocs.io/ |
| 10x Genomics 数据集 | https://www.10xgenomics.com/datasets/ |
| Theis Lab 单细胞资源 | https://github.com/theislab/single-cell-tutorial |
