# 模块 1：生物信息学导论

## 引言

在本部分中，我们将为你迈入"干实验"（dry lab）这个新世界奠定基础。我们将首先定义什么是生物信息学，以及它为何已成为现代科学中不可或缺的学科。我们将探索其多学科交叉的本质，回顾塑造该领域的里程碑事件。此外，我们还会涉及基本的生物学概念，如中心法则和"组学"科学。最后，我们将进入实践层面，讨论所需的计算能力，并熟悉每个生物信息学从业者都必须了解的基本数据库和文件格式。完成本模块后，你将对开始处理生物数据所需的关键概念和工具有扎实的理解。请记住，本模块以及整个课程提供了入门生物信息学所需的基础。持续练习数据库操作和文件格式是掌握这些技能的关键。

---

## 第一部分：基础知识和基本概念

### 1.1 什么是生物信息学？

生物信息学是一门将生物学、计算机科学、数学和统计学相结合的科学学科，用于分析和解读生物数据。其主要目标是通过计算工具来理解复杂的生物过程。

**关键定义：**

- **生物信息学（Bioinformatics）：** 应用计算工具来捕获、存储、分析和分发生物数据。
- **计算生物学（Computational Biology）：** 开发理论方法、数学模型和计算技术来研究生物系统。
- **系统生物学（Systems Biology）：** 一种整体性方法，研究生物系统各组分之间的相互作用，以及这些相互作用如何产生系统的功能和行为。
- **合成生物学（Synthetic Biology）：** 设计和构建新的生物组件、装置和系统，或重新设计已有的自然系统。

### 1.2 生物信息学的重要性

生物信息学至关重要，因为它：

- 能够处理现代技术（下一代测序）产生的大规模数据量
- 加速药物发现和个性化医疗的发展
- 促进对疾病在分子水平上的理解
- 使得研究物种进化及其亲缘关系成为可能
- 优化农业和生物技术

### 1.3 多学科交叉的本质

生物信息学整合了多个领域：

- **分子生物学（Molecular Biology）：** 提供生物学背景
- **计算机科学（Computer Science）：** 开发算法和软件
- **统计学（Statistics）：** 数据分析和解读
- **数学（Mathematics）：** 生物系统建模
- **化学（Chemistry）：** 分子结构
- **物理学（Physics）：** 分子特性

> [!IMPORTANT]
> **多学科交叉过程的示例：**
>
> **生物学问题：** 鉴定与*幽门螺杆菌*（*Helicobacter pylori*）相关癌症的基因
>
> 1. **生物学：** 从患者和健康人获取 DNA 样本。
> 2. **分子生物学：** 对基因组进行测序。
> 3. **生物信息学/数学/系统工程：** 使用 BLAST 等工具将序列比对到参考基因组，并使用组装程序构建基因组。
> 4. **统计学：** 进行关联检验（GWAS，全基因组关联分析），以找出在患者中显著更频繁出现的遗传变异（SNP，单核苷酸多态性）。
> 5. **计算生物学/物理学/化学：** 建模特定变异如何影响蛋白质的 3D 结构及其功能，预测生物学影响。
> 6. **生物学（实验室）：** 通过细胞或动物模型实验验证基因功能及突变的影响。

### 1.4 计算能力要求

推荐硬件配置：

- **初学者：**
    - 处理器：Intel Core i5/i7 或 AMD Ryzen 5/7
    - 内存：最低 8GB，推荐 16GB
    - 存储：256GB SSD + 用于存储数据的外置硬盘
    - GPU：可选，对深度学习（deep learning）有帮助
- **高级工作：**
    - 处理器：多核（16+ 核心）
    - 内存：32GB 或更高
    - 存储：1 TB SSD + 网络存储
    - 集群/云：AWS, Google Cloud, 机构服务器
    - 操作系统：
    - Linux（Ubuntu, CentOS）：首选，大多数工具原生支持
    - macOS：兼容许多工具
    - Windows：推荐使用 WSL（Windows Subsystem for Linux）

**真实案例**：细菌基因组组装

- 输入数据：5GB 的 Illumina 测序读长（reads）
- 所需内存：16-32GB
- 处理时间：2-6 小时
- 软件：SPAdes, Velvet
- 系统：首选 Linux

总结：

- 个人计算机（笔记本/台式机）：
    - **用途**：适用于小规模分析、数据可视化、编写脚本（Python, R），以及处理单条序列或小型基因组（如细菌）。
    - **真实示例：** 对人类胰岛素基因进行序列比对，使用 UCSF ChimeraX 可视化蛋白质结构，或运行简单的核苷酸计数脚本。
    - **操作系统**：macOS 或 Linux（以及 Windows 下的子系统如 WSL）是首选，因为它们具有原生的命令行终端，这对于大多数生物信息学工具至关重要。
- 高性能计算服务器（HPC/集群）：
    - **用途：** 用于需要大量计算能力和内存的任务，如从头组装（*de novo* assembly）基因组、大量样本的转录组分析（RNA-seq），或分子动力学模拟。
    - **真实示例：** 从数百万条测序读长中组装植物基因组，或分析 100 个癌症肿瘤的基因表达数据。

### 1.5 生物信息学简史

时间线：

- 1953：Watson 和 Crick 发现 DNA 结构
- 1965：第一个蛋白质序列数据库（Margaret Dayhoff）
- 1977：Sanger 测序法
- 1982：GenBank 作为公共存储库建立
- 1990-2003：人类基因组计划（Human Genome Project）
- 1990：BLAST（Basic Local Alignment Search Tool，基本局部比对搜索工具）
- 2000s：下一代测序（NGS，Next-Generation Sequencing）时代
- 2010s：CRISPR，单细胞测序（single-cell sequencing）
- 2020s：人工智能和机器学习（machine learning）在生物信息学中的应用

### 1.6 组学科学

组学研究从全局层面研究生物分子：

- **基因组学（Genomics）：** 对整个基因组的研究
- **转录组学（Transcriptomics）：** RNA/基因表达分析
- **蛋白质组学（Proteomics）：** 全套蛋白质
- **代谢组学（Metabolomics）：** 细胞代谢物
- **表观基因组学（Epigenomics）：** 表观遗传修饰

### 1.7 分子生物学中心法则

> [!IMPORTANT]
> `DNA → RNA → 蛋白质`
>
> `（复制）→（转录）→（翻译）`

- **DNA：** 存储遗传信息
- **RNA：** 中间媒介，携带信息
- **蛋白质：** 执行细胞功能

现代延伸：

- RNA 可以被复制（RNA 病毒）
- 逆转录（逆转录病毒）
- 具有调控功能的非编码 RNA（non-coding RNA）

最后总结，生物信息学是一门将生物学、计算机科学和统计学相结合的关键交叉学科，用于分析海量生物数据。它需要编程、统计学和分子生物学方面的技术知识，以及适当的计算基础设施。组学科学和中心法则提供了理解信息如何在生物系统中流动的概念框架。

---

## 第二部分：数据库和生物信息学资源

### 2.1 数据库和生物数据库的类型

- **一级数据库（Primary Databases）：** 包含直接的实验数据。存储由研究者直接提交的原始数据。它们是历史档案。
    - GenBank/NCBI：核苷酸序列
    - UniProt：蛋白质序列
    - PDB：蛋白质 3D 结构
    - SRA：原始测序数据

- **二级数据库（Secondary Databases）：** 从一级数据库的信息衍生而来，通过人工审阅（curation）、注释（annotation）和数据整合增加价值。
    - Pfam：蛋白质家族
    - GO (Gene Ontology，基因本体论)：基因功能
    - KEGG：代谢通路
    - Ensembl：注释基因组

- **专业数据库（Specialized Databases）：** 专注于特定生物、特定数据类型或特定生物学问题。
    - ClinVar：临床变异
    - dbSNP：多态性
    - TCGA：癌症数据
    - GTEx：组织中的基因表达
    - SGD (Saccharomyces Genome Database)：酿酒酵母（*Saccharomyces cerevisiae*）数据库

### 2.3 生物信息学文件格式

#### `.fasta` / `.fa` / `.fna`

- **描述：** 序列的文本格式。由以 `>` 开头的标题行（后跟标识符和描述）以及随后的核苷酸或氨基酸序列行组成。
- **用途：** 存储序列的最常用格式。被 BLAST、比对工具等使用。
- **结构：**

```
>gi|345678|ref|NM_007294.4| Homo sapiens BRCA1
ATGGATTTATCTGCTCTTCGCGTTGAAGAAGTACAAAATGTCATTAATGCTATGCAGAAAATCTTAGAGT
GTCCCATCTGTCTGGAGTTGATCAAGGAACCTGTCTCCACAAAGTGTGACCACATATTTTGCAAATTTTG
```

- **特征：**
    - 标题行：`>` 后跟标识符
    - 序列：多行文本
    - 简单、通用

#### `.fastq`

- **描述：** 类似 FASTA，但包含每个碱基的质量分数。每条序列有 4 行：1) 标题行（`@`），2) 序列，3) 分隔符（`+`），4) 质量分数（ASCII 编码）。
- **用途：** 下一代测序仪（NGS）的标准输出格式。
- **结构：**

```
@SEQ_ID
GATTTGGGGTTCAAAGCAGTATCGATCAAATAGTAAATCCATTTGTTCAACTCACAGTTT
+
!''*((((***+))%%%++)(%%%%).1***-+*''))**55CCF>>>>>>CCCCCCC65
```

- **特征：**
    - 每条读长（read）4 行
        - 第 1 行：`@` + 标识符
        - 第 2 行：序列
        - 第 3 行：`+`（可选地重复 ID）
        - 第 4 行：质量（Phred 编码）

#### `.gff` (General Feature Format) / `.gtf`

- **描述：** 用于描述基因组特征的表格文本格式（制表符分隔）。每行代表一个特征（基因、外显子、CDS），并指定其位置（染色体、起始、终止）。
- **用途：** 基因组注释（annotation）。
- **结构：**

```
chr1	HAVANA	gene	11869	14409	.	+	.	ID=gene1;Name=DDX11L1
chr1	HAVANA	transcript	11869	14409	.	+	.	ID=transcript1;Parent=gene1
chr1	HAVANA	exon	11869	12227	.	+	.	ID=exon1;Parent=transcript1
列：seqid, source, type, start, end, score, strand, phase, attributes
```

#### `.genbank` / `.gb`

- **描述：** 富文本格式，将序列与大量注释（位点、定义、登录号、特征、来源、参考文献）组合在一起。
- **用途：** NCBI 的 GenBank 数据库中的标准记录格式。
- **结构：**

```
LOCUS       NM_007294               7207 bp    mRNA    linear   PRI 15-MAR-2020
DEFINITION  Homo sapiens BRCA1, DNA repair associated (BRCA1), transcript
            variant 1, mRNA.
ACCESSION   NM_007294
FEATURES             Location/Qualifiers
     source          1..7207
                     /organism="Homo sapiens"
     gene            1..7207
                     /gene="BRCA1"
ORIGIN
        1 atggatttat ctgctcttcg cgttgaagaa gtacaaaatg tcattaatgc tatgcagaaa
        2 atggatttat ctgctcttcg cgttgaagaa gtacaaaatg tcattaatgc tatgcagaaa
```

#### `.csv`（逗号分隔值）/ `.tsv` / `.txt`（制表符分隔值）

- **描述：** 以逗号（`.csv`）或制表符（`.tsv`）分隔值的纯文本文件。是通用的表格数据格式。
- **用途：** 广泛用于存储结果表格，如基因表达计数、遗传变异列表或样本元数据。便于导入 R、Python（使用 Pandas）或 Excel。

`.csv` 示例：

```
Gene,Expression,P-value,FoldChange
BRCA1,456.2,0.001,2.3
TP53,789.1,0.0001,3.5
EGFR,234.8,0.05,1.2
```

`.tsv` 示例：

```
Gene	Expression	P-value	FoldChange
BRCA1	456.2	0.001	2.3
TP53	789.1	0.0001	3.5
```

### 2.4 何时使用每种格式？

| 格式 | 使用场景 |
|---|---|
| `.fasta` | 简单序列、序列比对、BLAST |
| `.fastq` | NGS 原始数据、质量控制 |
| `.gff` | 基因组注释、浏览器中的可视化 |
| `.genbank` | 完整信息、提交到数据库 |
| `.csv`/`.tsv` | 数值结果、统计分析、Excel |

#### 附加资源

- NCBI Handbook：完整的资源指南
- BLAST 教程：https://blast.ncbi.nlm.nih.gov/Blast.cgi?CMD=Web&PAGE_TYPE=BlastDocs
- 《Bioinformatics Data Skills》（书籍）：Vince Buffalo 著
- Rosalind：实践练习平台 https://rosalind.info/

---

## 模块练习

| 练习 | 描述 |
|---|---|
| [生物数据库](./exercises/01_databases.md) | 从 NCBI 及其他公共数据库搜索、浏览和下载序列 |
