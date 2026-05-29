# 生物信息学：告别白大褂和移液枪的科学

### 课程作者：Albert Tafur Rangel 博士

---

# 课程概述

本课程从实践角度介绍**生物信息学和计算生物学**的基础：你将学习生物数据从何而来、如何存储和共享，以及如何在类 Unix 环境中使用脚本来分析它们。

在整个模块中，我们将核心概念（序列数据、比对、组装、注释、系统发育学以及多种"组学"）与研究和工业中使用的实际工作流联系起来。

**完成本课程后，你应该能够：**

- 浏览生物数据库并以标准格式下载序列数据
- 在 **Unix/Linux** 终端中自如工作（也适用于 macOS）
- 使用小型脚本（Bash 和 Python）自动化重复性任务
- 理解测序、比对、组装和基因组注释背后的原理
- 构建系统发育树并解读进化关系
- 结合背景解读结果：质量、局限性和生物学意义

## 学习目标

- 理解生物信息学和计算生物学的一般概念
- 学习从公共数据库浏览、检索和解读生物数据
- 获得 Unix 命令行工具和用于生物数据分析的 Python 脚本编写的实践技能
- 能够生成和解读复杂分析：比对、系统发育、组装和注释

## 本课程适合我吗？

如果你对以下问题回答"是"，本课程就适合你：

- 你是生物学、微生物学、生物技术或相关领域的学生或研究者吗？
- 你想理解生物信息学工具*如何*工作，而不仅仅是运行它们吗？
- 你想学习自动化分析并以编程方式处理生物数据吗？

## 讲师

[**Albert Tafur Rangel 博士**](https://orcid.org/0000-0002-9428-183X) 是一位计算生物学家，对教学和设计充满热情。博士毕业后，他结合了在生物信息学、微生物学和数据科学方面的专业知识，创建了桥接湿实验和干实验思维的教育材料。

---

# 目录

> **提示：** 每个模块标题链接到其 `README.md`。

---

## 模块 1 — 生物信息学导论

📂 **[01-introduction/README.md](01-introduction/README.md)** | 📂 **[中文版](zh/01-introduction/README.md)**

核心概念：什么是生物信息学，为什么它重要，以及生物数据的生态系统。

**主题：**
- 生物信息学思维：多学科科学和计算思维
- 中心法则、"组学"及其如何产生数据
- 生物数据库（NCBI / ENA / UniProt）、标识符和元数据
- 课程中使用的文件格式：FASTA, FASTQ, GFF, GenBank 等

---

## 模块 2 — 编程基础：Unix + 脚本

📂 **[02-coding-basics/README.md](02-coding-basics/README.md)** | 📂 **[中文版](zh/02-coding-basics/README.md)**

**主题：**
- Unix/Linux 基础：导航、权限、管道和重定向
- 生物文件的文本处理（`grep`, `cut`, `sort`, `wc`, pipes）
- Python 脚本基础：变量、类型、控制流、文件 I/O

---

## 模块 3 — 序列分析：BLAST、比对和引物设计

📂 **[03-sequence_analysis/README.md](03-sequence_analysis/README.md)** | 📂 **[中文版](zh/03-sequence_analysis/README.md)**

**主题：**
- 使用 BLAST 进行相似性搜索和结果解读
- 全局 vs 局部比对：Needleman-Wunsch 和 Smith-Waterman 算法
- 同一性 vs 相似性；打分方案和 gap 罚分
- 引物设计标准和 Primer-BLAST

---

## 模块 4 — 系统发育学与进化推断

📂 **[04-phylogenetics/README.md](04-phylogenetics/README.md)** | 📂 **[中文版](zh/04-phylogenetics/README.md)**

**主题：**
- 多序列比对（MSA）和模型选择
- 基于距离 vs 基于特征的方法（NJ, ML）
- Bootstrap 和统计支持值
- 解读系统发育树的生物学和临床意义

---

## 模块 5 — DNA/RNA 测序：从分子到组装基因组

📂 **[05_sequencing/README.md](05_sequencing/README.md)** | 📂 **[中文版](zh/05_sequencing/README.md)**

**主题：**
- 分子基础：核苷酸结构、3'-OH 以及 Sanger/Illumina 的原理
- 测序技术时间线：Sanger → 454 → Illumina → PacBio → Nanopore
- 质量分数（Phred）、FASTQ 格式、覆盖度和深度
- 组装策略：mapping vs *de novo*；OLC 和 De Bruijn 图

---

## 模块 6 — 基因组学：从组装基因组到生物学知识

📂 **[06-genomics/README.md](06-genomics/README.md)** | 📂 **[中文版](zh/06-genomics/README.md)**

**主题：**
- 基因组注释：基因预测、功能分配、GFF/GBK 格式
- 变异检测：SNP、indel、VCF 格式
- 比较基因组学：ANI、泛基因组、AMR/毒力基因检测

---

## 模块 7 — 蛋白质生物信息学

📂 **[07-proteins/README.md](07-proteins/README.md)** | 📂 **[中文版](zh/07-proteins/README.md)**

**主题：**
- 蛋白质序列、结构域、基序和家族
- 蛋白质搜索和注释（BLASTp, Pfam/InterPro）
- 结构基础与功能推断

---

## 模块 8 — 蛋白质组学

📂 **[08-proteomics/README.md](08-proteomics/README.md)** | 📂 **[中文版](zh/08-proteomics/README.md)**

**主题：**
- 质谱基础与肽段鉴定
- 蛋白质定量与常见分析步骤
- 将蛋白质组学与通路和生物学解读相连接

---

## 模块 9 — 转录组学（RNA-Seq）

📂 **[09-transcriptomics/README.md](09-transcriptomics/README.md)** | 📂 **[中文版](zh/09-transcriptomics/README.md)**

**主题：**
- RNA-Seq 测量什么、实验设计与关键偏倚
- 定量概念（counts, TPM/FPKM）与归一化
- 差异表达与功能解读

---

## 模块 10 — 代谢网络建模

📂 **[10-metabolic-models/README.md](10-metabolic-models/README.md)** | 📂 **[中文版](zh/10-metabolic-models/README.md)**

**主题：**
- 代谢网络与基因组规模代谢模型（GEM）
- 通量平衡分析（FBA）：直觉、约束和目标函数
- 解读预测并将其与实验相连接

---

## 所需软件和工具箱

- **GitHub 账户** — 免费，用于访问 Codespaces（[github.com](https://github.com)）
- **MEGA X** — 系统发育分析（[megasoftware.net](https://www.megasoftware.net/)）
- **网页浏览器** — 用于 NCBI、BLAST、Primer-BLAST 及其他在线工具
- 其他工具将在相应模块中介绍

---

本作品基于 [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/) 许可。
