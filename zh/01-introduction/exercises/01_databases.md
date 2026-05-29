# 💻 练习：生物数据库及文件和序列的基本要素

## 引言

在基因组学时代，分析 DNA 序列的能力已成为现代生物学的基础技能。从鉴定与疾病相关的基因，到设计用于生物技术应用的微生物，序列分析是起点。然而，在分析任何序列之前，我们必须先知道如何找到它、下载它并理解其格式。

本练习是你首次进入生物数据库的世界——这些数字存储库保存着全球产生的大量遗传信息。我们将重点关注 NCBI（National Center for Biotechnology Information，美国国家生物技术信息中心），它是科学界最重要且使用最广泛的资源之一。

通过实践练习，你将学习如何浏览 NCBI 的 Nucleotide 数据库，搜索特定序列，并以 FASTA 和 GenBank 等标准格式下载它们。完成后，你将能够识别这些文件的关键组成部分，并理解为什么根据分析所需的信息选择合适的格式至关重要。

## 🎯 目标

- 熟悉 **NCBI Nucleotide** 数据库的界面和功能。
- 以 FASTA 及其他格式下载选定的序列。
- 识别并理解序列文件的关键元素，如标题行、长度和序列本身。
- 比较同一序列在不同文件格式（FASTA vs GenBank）中可获得的信息。

---

## 📦 前提条件
- 运行 **Windows/Linux/macOS** 的计算机。
- 用于访问 **NCBI Nucleotide** 的互联网连接。

---

## 🔬 操作步骤

### 浏览 NCBI（National Center for Biotechnology Information）

NCBI 是生物信息学中最重要的资源之一。它托管了多个集成的数据库。NCBI 主页有一个下拉菜单，可以在所有数据库中搜索（All Databases），或选择特定数据库（如 Nucleotide, Protein, Gene, PubMed）。NCBI 中发现的数据类型包括：
* 核苷酸序列
* 蛋白质序列
* 3D 结构
* 基因信息
* 科学文献（PubMed）
* 基因表达数据（GEO）等

这些数据按数据库相互连接或独立存储，一些主要资源包括：
* PubMed：科学文献
* Gene：基因信息
* Nucleotide：DNA/RNA 序列
* Protein：蛋白质序列
* Genome：完整基因组
* SRA：Sequence Read Archive（序列读长档案）
* BLAST：相似性搜索工具

#### 如何使用 NCBI：

1. **第 1 步：访问 URL**：https://www.ncbi.nlm.nih.gov/
2. **第 2 步：选择数据库**。下拉菜单：Gene, Nucleotide, Protein 等。
3. **第 3 步：搜索数据**。例如："GPD1 Saccharomyces cerevisiae"，你可以应用筛选条件：物种、分子类型、日期
4. **第 4 步：查看序列。**
   * 登录号（accession number，例如：NM_007294.4）
   * 信息：长度、物种、参考文献
   * 序列：核苷酸或氨基酸
5. **第 5 步：下载数据**
   * 格式：FASTA, GenBank, 其他
   * 选项：完整序列或特定区域

### 待办：实践练习

目标：获取并区分 β-内酰胺酶（Beta-lactamase，青霉素抗性）的遗传信息。

1. **搜索**：在 NCBI Nucleotide 中，搜索：`Escherichia coli blaTEM-1`。
2. **筛选：** 在左侧边栏中，选择 "Source databases: Nucleotide" 以排除合成序列。
3. **GenBank 格式分析：**
   * 查找 FEATURES 部分。
   * 识别关键字 `/gene="blaTEM-1"` 或 `gene="blaTEM"` 或任何匹配的 `gene="xxxxxx"`。
   * 识别登录号（accession number，例如 MZ123456.1）。
   * 记录坐标（例如 1..861）。这表示基因在该序列中的起始和终止位置。
4. **对比下载**（点击 `Send to` → `File` → `Format: FASTA`）：
   * 下载 FASTA 格式的序列（查看纯净序列）。
   * 下载 GenBank (Full) 格式的序列（查看基因的"传记"）。
   * 下载 GFF3 格式的序列（查看结构注释）。
5. 用文本编辑器打开。
> [!TIP]
> 你可以右键点击 `打开方式` → `Notepad++` 或 `VSCode` 或 `记事本` 以获得更好的查看效果。
6. 挑战 1：在 GenBank 文件中找到该特定序列来源的细菌菌株名称（Strain）。
7. 挑战 2：比较两种格式下的序列长度。它们相等吗？为什么？
8. 挑战 3：在 GFF3 文件中，识别编码区（CDS，Coding Sequence）的注释，并将其与在 GenBank 格式中找到的坐标进行比较。
9. 挑战 4：在 GFF3 文件中，只出现编码区（CDS）的坐标而非完整序列。你认为为什么会这样？在什么情况下 GFF3 文件比 FASTA 或 GenBank 文件更有用？除编码区外，GFF3 文件中还可能注释哪些其他类型的元素？

### 📝 反思问题（练习后）

这些问题旨在让你不仅仅下载文件，而是分析它们在微生物学实验室中的用途：

**关于数据结构**

1. FASTA 格式的语言：在文本编辑器中打开 .fasta 文件时，什么符号标识了序列的起始位置？如果你试图将此文件加载到分析软件中，而意外删除了该符号，你认为会发生什么？
2. 元数据 vs. 序列：比较 FASTA 文件和 GenBank (.gb) 文件。列举三个在 GenBank 格式中出现但在 FASTA 格式中无法找到的信息。在哪种实验情况下你必须使用 GenBank 文件？
3. 版本的重要性：观察登录号（例如 NM_000207.3）。你认为小数点后的数字意味着什么？为什么微生物学家在科学出版物中报告完整的登录号至关重要？

**关于微生物学背景**

4. 分类学鉴定：如果你正在分析一份环境样本并获得了一条未知序列，为什么 Nucleotide 数据库会是你使用 NCBI 其他数据库（如 PubMed 或 Structure）之前的第一步？
5. 数据管理（curation）：在你的搜索中，是否出现了 "Synthetic constructs"（合成构建体）或 "Cloning vectors"（克隆载体）的结果？如果你要研究的是受污染土壤中细菌的自然进化，这将如何影响你的研究？
