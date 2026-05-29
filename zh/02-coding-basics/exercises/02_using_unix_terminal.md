# 🖥️ 练习：使用 Unix 命令行操作生物文件

## 引言

到目前为止，你已经配置了 GitHub Codespaces 工作环境，并在 Unix 终端中迈出了第一步。在本练习中，你将更进一步：学习在文件系统中自如移动、直接从命令行检查和操作真实的生物文件，以及使用管道和重定向构建你的第一个数据流程序（pipeline）。

命令行不仅仅是一个技术工具：它是生物信息学从业者与计算机对话的语言。熟练掌握它意味着能够在几秒钟内检查包含数千条序列的文件、统计基因组中已注释的基因而不打开任何程序，或用一条命令筛选药敏试验（antibiogram）结果。这些本来需要手动通过桌面软件完成的所有工作，现在可以以可复现、自动化且可扩展的方式来实现。

在本练习中，你将处理在任何真实生物信息学项目中都会遇到的文件类型：**FASTA** 格式的序列、**PCR** 结果、**GFF3** 格式的基因组注释、**药敏试验**（antibiogram）数据、细菌生长曲线等。所有文件均可在仓库的 `data/` 文件夹中找到。

---

## 🎯 目标

- 使用绝对路径和相对路径从终端导航 Linux 文件系统。
- 在不离开终端的情况下检查不同格式（FASTA, CSV, TSV, GFF3）的生物文件。
- 在真实生物文件上使用搜索和筛选命令（`grep`, `cut`, `sort`, `wc`）。
- 使用管道 `|` 构建基本的数据流并将结果重定向到新文件中。
- 使用正确的目录结构组织生物信息学项目。

---

## 📦 前提条件

- 已激活的 **GitHub** 账户（见练习 1）。
- 已打开课程仓库并可以访问 **GitHub Codespaces**。
- 已阅读模块 2 README 的**第 1 部分**（第 1.1 至 1.8 节）。

---

## 🗂️ 可用的数据文件

本练习中的所有文件都位于练习目录下的 `data/` 文件夹中。在开始之前，请先熟悉其内容：

| 文件 | 格式 | 描述 |
|---|---|---|
| `01_sequences.fasta` | FASTA | 10 条示例 DNA 序列 |
| `02_results_pcr_96.csv` | CSV | 96 孔板的 PCR 结果 |
| `03_nucleotides.fa` | FASTA | 单条 DNA 序列（用于分析） |
| `05_genome.gff3` | GFF3 | 两条染色体中的基因注释 |
| `06_bacterial_growth.tsv` | TSV | 三种细菌菌株的 OD600 吸光度 |
| `07_mutations.fasta` | FASTA | 参考序列和两个突变体 |
| `08_antibiogram.csv` | CSV | 药敏试验结果（R/S/I） |
| `09_short_sequences.fasta` | FASTA | 不同长度的序列 |
| `12_gene_annotations.tsv` | TSV | 基因的功能注释（KO IDs） |

---

## 🔬 操作步骤

### 第 1 部分：环境导航和探索

#### 逐步操作：

1. **打开你已有的 Codespace。** 如果你在练习 1 中已经创建了一个 Codespace，请**直接使用它**。不要每次都创建新的：每个 Codespace 会占用你的免费计划配额，而且在一个 Codespace 中创建的文件在另一个中不可用。
   - 前往 [github.com/codespaces](https://github.com/codespaces)，你将看到活跃的 Codespace 列表。
   - 点击你已创建的那个，从上次离开的地方继续。
   - 如果它被自动删除了（30 天不活动后），那么请按照练习 1 的步骤从课程仓库创建一个新的。

   进入后，**打开终端**（`Terminal` → `New Terminal` 或 `` Ctrl + ` ``）。

2. **定位到正确的目录。** 导航到练习文件夹：
   ```bash
   cd exercises/
   pwd
   ```
   确认路径以 `.../02-coding-basics/exercises` 结尾。

3. **探索可用的文件结构：**
   ```bash
   ls
   ls -lh data/
   ```
   - `data/` 文件夹中有多少个文件？
   - 哪个文件最大？哪个最小？

4. **为本练习创建目录结构：**
   ```bash
   mkdir -p results/sequences
   mkdir -p results/annotations
   mkdir -p results/reports
   ```
   验证是否创建成功：
   ```bash
   tree .
   ```
   > [!TIP]
   > `mkdir` 的 `-p` 选项也会创建尚不存在的中间目录。如果 `tree` 不可用，请使用 `ls -R`。

5. **将数据文件复制到你的工作区域：**
   ```bash
   cp data/*.fasta results/sequences/
   cp data/*.fa results/sequences/
   ls -lh results/sequences/
   ```

---

### 第 2 部分：检查生物文件

生物信息学中最常见的任务之一是在不打开图形编辑器的情况下检查文件。以下命令可以让你直接从终端完成此操作。

#### 处理 FASTA 文件

6. **查看**序列文件的**前几行**：
   ```bash
   head data/01_sequences.fasta
   ```
   什么符号标识了每条序列的开头？

7. **统计文件中有多少条序列：**
   ```bash
   grep -c ">" data/01_sequences.fasta
   ```
   符号 `>` 标志着 FASTA 格式中每条序列的标题行。`grep -c` 统计匹配模式的行数。

8. **仅查看序列的标题行（标识符）：**
   ```bash
   grep ">" data/01_sequences.fasta
   ```

9. **查看完整的突变文件：**
   ```bash
   cat data/07_mutations.fasta
   ```
   有多少条序列？哪条是参考序列？

10. **直观比较短序列之间的长度：**
    ```bash
    cat data/09_short_sequences.fasta
    ```
    哪条似乎最短？哪条最长？

#### 处理表格文件（CSV / TSV）

11. **检查药敏试验文件：**
    ```bash
    cat data/08_antibiogram.csv
    ```
   值 R、S 和 I 代表什么含义？
    > R = 耐药（Resistant），S = 敏感（Susceptible），I = 中介（Intermediate）

12. **仅查看 PCR 文件的前几行：**
    ```bash
    head -5 data/02_results_pcr_96.csv
    ```

13. **统计 PCR 板中有多少个孔发生了扩增：**
    ```bash
    grep -c "Amplificó" data/02_results_pcr_96.csv
    ```
    然后统计**未**扩增的数量：
    ```bash
    grep -c "No amplificó" data/02_results_pcr_96.csv
    ```

14. **检查 GFF3 基因组注释：**
    ```bash
    cat data/05_genome.gff3
    ```
    - 基因注释在多少条染色体上？
    - 有多少个基因在正链（`+`）上，多少个在负链（`-`）上？

---

### 第 3 部分：搜索、筛选和处理

这里终端开始展现它真正的威力。这些命令可以让你即时从大文件中提取特定信息。

15. **仅从 GFF3 文件中提取基因**（按元素类型筛选）：
    ```bash
    grep "gene" data/05_genome.gff3
    ```

16. **仅提取在 `chr2` 上注释的基因：**
    ```bash
    grep "chr2" data/05_genome.gff3
    ```

17. **统计每条染色体上有多少个基因**（结合 `grep` 和 `wc -l`）：
    ```bash
    grep "chr1" data/05_genome.gff3 | grep "gene" | wc -l
    grep "chr2" data/05_genome.gff3 | grep "gene" | wc -l
    ```

18. **使用 `cut` 仅查看 GFF3 文件的第一列**（染色体）：
    ```bash
    cut -f1 data/05_genome.gff3
    ```
    > [!NOTE]
    > `cut -f1` 使用制表符作为默认分隔符来提取第一列。对于 CSV 文件，使用 `cut -d',' -f1`。

19. **提取药敏试验的结果列并排序：**
    ```bash
    cut -d',' -f3 data/08_antibiogram.csv | sort
    ```
    有多少菌株是耐药（R）的？

20. **仅筛选耐药菌株**并保存结果：
    ```bash
    grep ",R" data/08_antibiogram.csv > results/reports/cepas_resistentes.txt
    cat results/reports/cepas_resistentes.txt
    ```

21. **在注释文件中搜索具有功能注释的基因**（即不是 `NA` 的基因）：
    ```bash
    grep -v "NA" data/12_gene_annotations.tsv | head -10
    ```
    > [!NOTE]
    > `grep -v` 反转筛选条件：显示**不**包含该模式的行。

22. **统计有多少个基因有/没有功能注释：**
    ```bash
    grep -c "NA" data/12_gene_annotations.tsv
    grep -v "NA" data/12_gene_annotations.tsv | grep -v "gene_ID" | wc -l
    ```

---

### 第 4 部分：管道和构建数据处理流程

23. **提取主文件中所有序列的 ID**并保存到文件中：
    ```bash
    grep ">" data/01_sequences.fasta | sed 's/>//' > results/reports/sequence_ids.txt
    cat results/reports/sequence_ids.txt
    ```
    > [!NOTE]
    > `sed 's/>//'` 将符号 `>` 替换为空（即删除它），只保留序列名称。

24. **统计 `03_nucleotides.fa` 文件中的总碱基数：**
    ```bash
    grep -v ">" data/03_nucleotides.fa | wc -c
    ```
    > [!NOTE]
    > `wc -c` 统计字符数，包括换行符（`\n`）。将结果减去 1 以获得实际的碱基数。

25. **为所有 FASTA 文件构建快速报告** — 每个文件的序列数：
    ```bash
    for archivo in data/*.fasta data/*.fa; do
        echo -n "$archivo: "
        grep -c ">" "$archivo"
    done
    ```
    保存此报告：
    ```bash
    for archivo in data/*.fasta data/*.fa; do
        echo -n "$archivo: "
        grep -c ">" "$archivo"
    done > results/reports/resumen_fasta.txt

    cat results/reports/resumen_fasta.txt
    ```

26. **提取所有唯一的功能注释**：
    ```bash
    cut -f4 data/12_gene_annotations.tsv | grep -v "NA" | grep -v "KO_annotation" | sort | uniq | head -20
    ```
    你认识列出的任何功能吗？

---

### 第 5 部分：从终端下载数据

在生物信息学中，数据很少从一开始就在你的计算机上。最常见的方式是直接从 NCBI、UniProt 或 Ensembl 等公共数据库下载，而不离开终端。`wget` 命令是完成此操作的标准工具。

> [!NOTE]
> `wget`（"World Wide Web get"）从 URL 下载文件，就像浏览器那样，但没有图形界面。这使其非常适合自动化脚本和远程服务器。

#### 使用 `wget` 从 NCBI 下载 FASTA 文件

NCBI 通过其 **Entrez** API 提供对其数据库的程序化访问。使用正确构建的 URL，你可以直接下载任何序列。

27. **直接从 NCBI 下载*大肠杆菌*（*Escherichia coli*）K-12 的 16S rRNA 基因序列：**
    ```bash
    wget -O data/ecoli_16S.fasta "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=nuccore&id=NR_102804&rettype=fasta&retmode=text"
    ```
    - `-O data/ecoli_16S.fasta` 指定输出文件的名称和位置。
    - 参数 `id=NR_102804` 是 NCBI 中*大肠杆菌* K-12 16S rRNA 基因的登录号（accession number）。

28. **验证文件是否下载成功：**
    ```bash
    head data/ecoli_16S.fasta
    grep -c ">" data/ecoli_16S.fasta
    ```
    你认得该格式吗？它包含多少条序列？

29. **下载第二条序列用于比较** — *枯草芽孢杆菌*（*Bacillus subtilis*）的 16S rRNA：
    ```bash
    wget -O data/bsubtilis_16S.fasta "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=nuccore&id=NR_112116&rettype=fasta&retmode=text"
    ```

30. **将下载的两条序列合并为一个 FASTA 文件**（就像系统发育分析前需要做的那样）：
    ```bash
    cat data/ecoli_16S.fasta data/bsubtilis_16S.fasta > results/sequences/16S_comparison.fasta
    grep ">" results/sequences/16S_comparison.fasta
    ```

> [!IMPORTANT]
> **关于 FTP：** 一些大型数据库，如 **NCBI FTP** 或 **Ensembl**，通过 **FTP**（File Transfer Protocol，文件传输协议）分其文件。`wget` 也支持以 `ftp://` 开头的 URL。例如，要从 NCBI FTP 下载*大肠杆菌* K-12 的完整基因组，可以使用：
> ```bash
> wget ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/005/845/GCF_000005845.2_ASM584v2/GCF_000005845.2_ASM584v2_genomic.fna.gz
> ```
> 基因组文件通常经过压缩（`.gz`）。要解压缩，使用 `gunzip archivo.fna.gz`。**不要在本次练习中运行此命令**，因为文件较大（约 4.6 MB），但在你的实际项目中可以记住这一点。

---

### 第 6 部分：项目最终整理

31. **将生成的文件移动到正确的目录：**
    ```bash
    mv results/reports/cepas_resistentes.txt results/reports/
    mv results/reports/sequence_ids.txt results/reports/
    mv results/reports/resumen_fasta.txt results/reports/
    ```

32. **验证工作区的最终结构：**
    ```bash
    tree results/
    ```

33. **为你的结果生成一个 README 文件：**
    ```bash
    echo "# 结果 - Unix 终端练习" > results/README.txt
    echo "日期: $(date)" >> results/README.txt
    echo "本练习生成的文件:" >> results/README.txt
    ls results/reports/ >> results/README.txt
    cat results/README.txt
    ```
    > [!NOTE]
    > `$(date)` 是一个命令替换：执行 `date` 并将其结果粘贴到该行中。这与我们在 Bash 脚本中使用 `$(grep -c ...)` 的机制相同。

---

### 待办：附加挑战

这些挑战将多个命令整合到一个管道中。请尝试先不看答案来解决它们。

**挑战 1：** 从 `08_antibiogram.csv` 文件中，仅提取与*大肠杆菌*（*E. coli*）对应的行，并保存到 `results/reports/ecoli_antibiogram.txt`。

**挑战 2：** 从 `12_gene_annotations.tsv` 文件中，仅提取**确实有注释**（即不为 `NA`）的基因的 `KO_ID` 列，删除重复值，并统计有多少个唯一注释。

**挑战 3：** 从 `05_genome.gff3` 文件中，仅提取**负链**（`-`）上的基因，并保存到 `results/annotations/genes_hebra_negativa.txt`。

**挑战 4：** 在 `results/` 文件夹中创建一个名为 `mi_resumen.sh` 的 Bash 脚本，运行后打印以下内容：
   - `01_sequences.fasta` 中的序列总数。
   - `08_antibiogram.csv` 中耐药菌株的数量。
   - `12_gene_annotations.tsv` 中已注释基因（非 NA）的数量。

   ```bash
   #!/bin/bash
   echo "=== 分析摘要 ==="
   echo -n "01_sequences.fasta 中的序列数："
   # 在此处补全命令
   echo -n "耐药菌株数："
   # 在此处补全命令
   echo -n "具有功能注释的基因数："
   # 在此处补全命令
   ```

---

### 📝 反思问题（练习后）

**关于导航和文件**

1. **绝对路径 vs. 相对路径：** 在练习中，你使用了相对路径，如 `data/01_sequences.fasta`。在什么情况下使用完整的绝对路径会更安全？请考虑一个将从系统的不同位置运行的脚本。
2. **命令 `grep -v`：** 在第 21 步中，你使用 `grep -v "NA"` 来排除行。如果你知道所有有问题的序列在标题行中都有一个特定模式，你如何利用同样的原理从 FASTA 文件中筛选低质量序列？
3. **使用 `wc -c` 统计碱基数：** 为什么 `wc -c` 对 FASTA 序列的统计结果不完全等于碱基数？还有哪些其他字符被统计进去了？

**关于管道和可复现性**

4. **作为分析工具的管道：** 在第 26 步中，你串联了 `cut | grep | grep | sort | uniq`。请用你自己的话描述该管道中每一步的作用，就像在描述一个一步步的实验方案。
5. **可复现性：** 如果你将本练习中的所有命令保存在一个 `.sh` 脚本中并分享到 GitHub，另一位研究者就能精确复现你的结果。这与直接向同事发送结果文件有何不同？哪种方式在科学上更有价值，为什么？
6. **临床微生物学中的 `grep`：** 假设你有一个包含 500 名患者药敏试验结果的文件，需要紧急识别所有耐碳青霉烯类抗生素的*肺炎克雷伯菌*（*Klebsiella pneumoniae*）。如何用 `grep` 构建一个命令或管道在几秒钟内提取该信息？
