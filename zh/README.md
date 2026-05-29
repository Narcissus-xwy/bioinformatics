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

> **提示：** 每个模块标题链接到其 `README.md`。练习在每个模块描述下方单独列出。

---

## 模块 1 — 生物信息学导论

📂 **[01-introduction/README.md](01-introduction/README.md)** | 📂 **[中文版](zh/01-introduction/README.md)**

核心概念：什么是生物信息学，为什么它重要，以及生物数据的生态系统。

**主题：**
- 生物信息学思维：多学科科学和计算思维
- 中心法则、"组学"及其如何产生数据
- 生物数据库（NCBI / ENA / UniProt）、标识符和元数据
- 课程中使用的文件格式：FASTA, FASTQ, GFF, GenBank 等

**练习：**
- [生物数据库](01-introduction/exercises/01_databases.md) — 从 NCBI 及其他公共数据库搜索、浏览和下载序列

---

## 模块 2 — 编程基础：Unix + 脚本

📂 **[02-coding-basics/README.md](02-coding-basics/README.md)** | 📂 **[中文版](zh/02-coding-basics/README.md)**

你将学习生物信息学的"日常工具"：命令行和自动化。

**主题：**
- Unix/Linux 基础（也适用于 macOS）：导航、权限、管道和重定向
- 生物文件的文本处理（`grep`, `cut`, `sort`, `wc`, pipes）
- 使用 Python 编写脚本的基础：变量、类型、控制流、文件 I/O
- 将脚本应用于 FASTA/序列操作和生物数据分析

**练习：**
- [创建 GitHub 账户](02-coding-basics/exercises/01_creating_an_github_account.md) — 设置 GitHub 和 Codespaces 用于本课程
- [使用 Unix 终端](02-coding-basics/exercises/02_using_unix_terminal.md) — 导航、文件操作以及使用生物文件进行文本处理
- [使用 Python 编写脚本](02-coding-basics/exercises/03_scripting_in_python.md) — 变量、数据类型、条件判断、循环以及用于生物分析的脚本

---

## 模块 3 — 序列分析：BLAST、比对和引物设计

📂 **[03-sequence_analysis/README.md](03-sequence_analysis/README.md)** | 📂 **[中文版](zh/03-sequence_analysis/README.md)**

从这里开始，你将做生物信息学家最常做的事情：**比较序列**。

**主题：**
- 使用 BLAST 进行相似性搜索（`blastn`, `blastp`, `blastx`）和结果解读（E-value, score, identity, coverage）
- 全局 vs 局部比对：使用动态规划的 Needleman-Wunsch 和 Smith-Waterman 算法
- 同一性（Identity）vs 相似性（Similarity）；打分方案和 gap 罚分
- 引物设计标准、Primer-BLAST 和 *in silico* PCR / 电泳

**练习：**
- [使用动态规划的序列比对](03-sequence_analysis/exercises/code/alignment_dp.py) — 从头开始用 Python 实现 Needleman-Wunsch 和 Smith-Waterman
- [引物设计](03-sequence_analysis/exercises/01_primer_design.md) — 使用 Primer-BLAST 设计引物、验证扩增子大小并模拟凝胶电泳

---

## 模块 4 — 系统发育学与进化推断

📂 **[04-phylogenetics/README.md](04-phylogenetics/README.md)** | 📂 **[中文版](zh/04-phylogenetics/README.md)**

你将使用分子数据构建和解读进化树。

**主题：**
- 多序列比对（MSA）和模型选择
- 基于距离 vs 基于特征的方法（Neighbor-Joining, Maximum Likelihood）
- Bootstrap 和统计支持值
- 在生物学和临床背景下解读系统发育树

**练习：**
- [通过 16S rRNA 系统发育分析鉴定细菌](04-phylogenetics/exercises/01_phylogenetics.md) — 案例实践（临床、环境、链霉菌）：BLAST、参考选择、MSA、在 MEGA 中构建 NJ/ML 树以及鉴定未知细菌

---

## 模块 5 — DNA/RNA 测序：从分子到组装基因组

📂 **[05_sequencing/README.md](05_sequencing/README.md)** | 📂 **[中文版](zh/05_sequencing/README.md)**

现在你学习数据实际上来自哪里——以及如何从中重建基因组。

**主题：**
- 分子基础：核苷酸结构、3'-OH、DNA 复制以及 Sanger/Illumina 为什么能工作
- 测序技术时间线：Sanger → 454 → Illumina → PacBio → Nanopore
- 质量分数（Phred）、FASTQ 格式、覆盖度（coverage）、深度（depth）、trimming
- Single-end vs paired-end 读长
- 组装策略：mapping vs *de novo*；OLC 和 De Bruijn 图；k-mer
- 组装评估：N50, L50, NG50, BUSCO、污染检测

**练习：**
- [基因组组装：使用 Falco + fastp + Shovill](05_sequencing/exercises/01_1_genome_assembly_falco_fastp_shovill.md)
- [基因组组装：使用 FastQC + Velvet](05_sequencing/exercises/01_2_genome_assembly_fastqc_velvet.md)

---

## 模块 6 — 基因组学：从组装基因组到生物学知识

📂 **[06-genomics/README.md](06-genomics/README.md)** | 📂 **[中文版](zh/06-genomics/README.md)**

你赋予组装基因组生物学意义：注释、变异和比较基因组学。

**主题：**
- 基因组注释：基因预测（原核生物 vs 真核生物）、功能分配、GFF/GBK 格式
- 变异检测：SNP、indel、VCF 格式、映射到参考序列
- 比较基因组学：ANI、泛基因组（核心/附属）、AMR/毒力基因检测、共线性（synteny）

**练习：**
- [基因组注释](06-genomics/exercises/01_1_genome_annotation_galaxy.md)

---

## 模块 7 — 蛋白质生物信息学

📂 **[07-proteins/README.md](07-proteins/README.md)** | 📂 **[中文版](zh/07-proteins/README.md)**

**主题：**
- 蛋白质序列、结构域、基序和家族
- 搜索和注释蛋白质（BLASTp, Pfam/InterPro）
- 结构基础和功能推断

---

## 模块 8 — 蛋白质组学

📂 **[08-proteomics/README.md](08-proteomics/README.md)** | 📂 **[中文版](zh/08-proteomics/README.md)**

**主题：**
- 质谱基础和肽段鉴定
- 蛋白质定量和常见分析步骤
- 将蛋白质组学与通路和生物学解读相连接

---

## 模块 9 — 转录组学（RNA-Seq）

📂 **[09-transcriptomics/README.md](09-transcriptomics/README.md)** | 📂 **[中文版](zh/09-transcriptomics/README.md)**

**主题：**
- RNA-Seq 测量什么、实验设计和关键偏差
- 定量概念（counts, TPM/FPKM）和归一化
- 差异表达和功能解读

---

## 模块 10 — 代谢网络建模：从基因组到生物工厂设计

📂 **[10-metabolic-models/README.md](10-metabolic-models/README.md)** | 📂 **[中文版](zh/10-metabolic-models/README.md)**

**主题：**
- 代谢网络和基因组规模代谢模型（GEM）
- 通量平衡分析（FBA）：直觉、约束和目标函数
- 带有酶约束的模型（GECKO）
- 解读预测并将其与实验相连接

---

## 课前准备

## 所需软件和工具箱

- **GitHub 账户** — 免费，用于访问 Codespaces（[github.com](https://github.com)）
- **MEGA X** — 系统发育分析（[megasoftware.net](https://www.megasoftware.net/)）
- **网页浏览器** — 用于 NCBI、BLAST、Primer-BLAST 及其他在线工具
- 其他工具将在相应模块中介绍

---

本作品基于 [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/) 许可。<br>![](https://i.creativecommons.org/l/by/4.0/88x31.png)
