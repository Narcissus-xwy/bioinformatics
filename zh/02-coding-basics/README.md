# 模块 2：脚本与命令行基础

## 引言

如果说上一个模块向你介绍了生物信息学的"是什么"和"为什么"，那么本模块将教你"怎么做"。在命令行中工作并编写脚本的能力是整个生物信息学中最具通用性的技能：没有它，就无法处理数千条序列、无法自动化重复性分析、无法运行科学界数十年来开发的工具。

在本模块中，你将学会在 **Linux/Unix** 环境中自如操作——这是计算服务器、高性能集群以及 GitHub Codespaces 等云环境中最主要的操作系统。你将学习用于浏览文件系统、直接从终端操作生物文件、管理权限以及运行自动化脚本的基本命令。

之后，你将迈出使用 **Bash** 和 **Python** 编程的第一步，这是生物信息学中最常用的两种语言。Bash 用于编排工具和自动化工作流，Python 则用于操作数据、处理序列以及用清晰且可复用的代码构建更复杂的分析。

完成本模块后，你将能够在 Unix 终端中独立操作、编写基本脚本，并将这些知识直接应用于分析 FASTA 序列和遗传数据等生物文件。

> [!IMPORTANT]
> 🧪 本模块的实验练习将使用 **GitHub Codespaces** 完成，这是在练习 1（creating_an_github_account）中配置的云端开发环境。请在开始前确保你的 GitHub 账户已激活。

---

## 第一部分：Linux 操作系统导论

### 1.1 Unix、Linux 和 macOS：共同的起源

要理解为什么 Linux 在生物信息学中占主导地位，首先需要了解其历史。一切都始于 **Unix**，一个由 AT&T 贝尔实验室在 20 世纪 70 年代开发的操作系统。Unix 奠定了我们今天所知的命令行哲学的基础：小型、专用的工具，可以彼此串联起来构建复杂的分析。

从 Unix 直接衍生出两个你可能已经知道的操作系统：

- **Linux：** 一个受 Unix 启发的开源操作系统，由 Linus Torvalds 于 1991 年创建。严格来说它并非 Unix，但遵循相同的哲学，并共享绝大多数命令和行为。
- **macOS：** Apple 的操作系统构建于 **Darwin** 之上，这是一个直接衍生自 BSD Unix 的开源内核。因此，macOS 的终端（`zsh`/`bash`）几乎与 Linux 完全一样，大多数命令都相同。

```
        Unix (AT&T 贝尔实验室, 20世纪70年代)
               /               \
         BSD Unix             System V
             |                   |
       Darwin (Apple)    Linux (Torvalds, 1991)
             |                   |
        macOS / iOS     Ubuntu, CentOS, Debian...
```

> [!IMPORTANT]
> 💡 这意味着如果你学会了在 Linux 中使用终端（例如使用 GitHub Codespaces），你就可以将完全相同的知识应用于 macOS 和世界上任何 Unix 服务器。

**那么 Windows 呢？**

Windows 有完全不同的起源，不属于 Unix 家族。然而，这**并不意味着它不能在生物信息学中使用**。Microsoft 提供了 **WSL（Windows Subsystem for Linux，适用于 Linux 的 Windows 子系统）**，允许在 Windows 内部运行完整的 Linux 环境，并可从终端访问。大多数生物信息学工具在 WSL 中都能完美运行。本课程我们使用 GitHub Codespaces 正是为了消除这种差异：所有学生，无论使用何种操作系统，都在云端的同一个 Linux 环境中工作。

### 1.2 为什么生物信息学中使用 Linux/Unix？

绝大多数生物信息学工具——基因组组装器、序列比对器、变异检测器——都是为在 **Unix/Linux** 系统上运行而设计的。大学和研究中心的高性能计算服务器（HPC）几乎普遍运行 Linux。因此，掌握这一环境不是可选的：它是一项基本能力。

Linux/Unix 在生物信息学中占主导地位的主要原因有：

- **自由和开源软件：** 大多数工具（BLAST、SAMtools、BWA、SPAdes）是免费的，可以在 Linux/macOS 上原生安装。
- **强大的终端：** 命令行可以自动化那些用图形界面操作不可能完成或非常缓慢的任务。
- **稳定性和性能：** Linux 针对长时间、高资源消耗的进程进行了优化，不会中断。
- **可复现性（Reproducibility）：** 脚本记录了具体执行了哪些命令，有助于科学可复现性。
- **通用性：** 在 Linux 上编写的脚本可以在 macOS 和世界上任何 Unix 服务器上运行。

### 1.3 Linux 文件系统结构

与 Windows（以驱动器如 `C:\`、`D:\` 来组织文件）不同，Linux 将所有内容组织在以 **根目录** `/` 为起点的目录层次结构中。

```
/
├── home/        → 用户个人目录（例如 /home/usuario）
├── bin/         → 系统基本程序（ls, cp, mv...）
├── usr/         → 已安装的程序和工具
├── etc/         → 系统配置文件
├── tmp/         → 临时文件
├── var/         → 可变数据（日志、本地数据库）
└── data/        → （惯例）服务器上的分析数据
```

**关键导航概念：**

| 符号 / 术语 | 含义 |
|---|---|
| `/` | 根目录（系统的"顶端"） |
| `~` | 用户个人目录（`/home/usuario`） |
| `.` | 当前目录（current directory） |
| `..` | 父目录（上一级） |
| **绝对路径** | 从 `/` 开始，例如 `/home/usuario/datos/seq.fasta` |
| **相对路径** | 相对于当前目录，例如 `datos/seq.fasta` |

### 1.4 基本导航命令

导航命令是你需要首先记住的命令。它们允许你在文件系统中移动并了解工作环境。

| 命令 | 描述 | 示例 |
|---|---|---|
| `pwd` | 显示当前目录（*print working directory*） | `pwd` |
| `ls` | 列出文件和文件夹 | `ls -la` |
| `cd` | 更改目录（*change directory*） | `cd /home/usuario` |
| `mkdir` | 创建新目录 | `mkdir resultados` |
| `rmdir` | 删除空目录 | `rmdir temp` |
| `tree` | 以树状结构显示目录结构 | `tree datos/` |

**`ls` 的常用选项：**

```bash
ls -l       # 列出详细信息（权限、大小、日期）
ls -a       # 显示隐藏文件（以 . 开头）
ls -la      # 以上两者的组合
ls -lh      # 人类可读的文件大小（KB, MB, GB）
ls *.fasta  # 仅列出扩展名为 .fasta 的文件
```

### 1.5 文件操作命令

一旦你能够导航，就需要创建、复制、移动和删除文件。这些命令是生物信息学日常工作中最常用的。

| 命令 | 描述 | 示例 |
|---|---|---|
| `touch` | 创建空文件 | `touch secuencias.fasta` |
| `cp` | 复制文件或目录 | `cp seq.fasta backup/` |
| `mv` | 移动或重命名文件 | `mv seq.fasta datos/` |
| `rm` | 删除文件 | `rm temp.txt` |
| `cat` | 显示文件内容 | `cat seq.fasta` |
| `less` | 分页查看文件（适用于大文件） | `less genome.fastq` |
| `head` | 显示文件的前几行 | `head -10 seq.fasta` |
| `tail` | 显示文件的最后几行 | `tail -5 resultados.txt` |
| `wc` | 统计行数、单词数和字符数 | `wc -l seq.fasta` |

> [!WARNING]
> Linux 中的 `rm` 命令**不会将文件发送到回收站**。它会永久且不可恢复地删除文件。请谨慎使用，尤其是 `rm -r`（删除整个目录）。

**搜索和筛选命令——生物信息学的基础：**

| 命令 | 描述 | 生物信息学示例 |
|---|---|---|
| `grep` | 在文件中搜索文本模式 | `grep ">" seq.fasta`（统计序列数） |
| `sort` | 对文件行排序 | `sort genes.txt` |
| `uniq` | 删除连续重复行 | `sort genes.txt \| uniq` |
| `cut` | 从表格文件中提取列 | `cut -f1,3 anotaciones.gff` |
| `sed` | 流编辑器，用于文本替换 | `sed 's/chr/chromosome/g' archivo.gff` |
| `awk` | 按列进行文本处理 | `awk '$3 == "gene"' anotaciones.gff` |

### 1.6 管道 `|` 和重定向

Unix 终端最强大的特性之一是能够**串联命令**并将一个命令的**输出重定向**为另一个命令的输入。这允许通过组合简单命令来构建复杂的分析。

**输出重定向：**
```bash
# 将命令输出保存到文件
ls -la > lista_archivos.txt

# 追加（不覆盖）到现有文件
echo "nueva línea" >> notas.txt

# 将错误重定向到日志文件
blast.exe 2> errores.log
```

**管道 `|`：**
```bash
# 统计 FASTA 文件中有多少条序列
#（每条序列以 ">" 开头）
grep ">" secuencias.fasta | wc -l

# 查看前 5 个序列标识符
grep ">" secuencias.fasta | head -5

# 在 GFF 文件中搜索注释为 CDS 的基因并排序
grep "CDS" anotacion.gff | sort -k1,1 -k4,4n
```

> 💡 将 `|` 想象成流水线：每个工人的输出直接传递给下一个工人。这种"做好一件事并传给下一个"的哲学是 Unix 的设计原则之一。

### 1.7 Linux 中的权限

Linux 是一个多用户系统。每个文件和目录都有一组**权限**，控制谁可以读取、写入或执行该文件。这在共享服务器上工作或运行脚本时尤为重要。

**读取权限：**

```bash
$ ls -la
-rwxr-xr-- 1 usuario grupo 4096 Feb 25 10:00 mi_script.sh
```

| 部分 | 含义 |
|---|---|
| `-` | 类型：`-` 文件，`d` 目录，`l` 链接 |
| `rwx` | **所有者**的权限：读（r）、写（w）、执行（x） |
| `r-x` | **组**的权限：读、无写、执行 |
| `r--` | **其他人**的权限：仅读 |

**使用 `chmod` 修改权限：**

```bash
chmod +x mi_script.sh        # 赋予执行权限
chmod 755 mi_script.sh       # rwxr-xr-x（所有者：全部；组和其他人：读和执行）
chmod 644 datos.fasta        # rw-r--r--（所有者：读/写；其他人：仅读）
```
> [!IMPORTANT]
> 在生物信息学中，运行脚本时最常见的错误是 `Permission denied`。解决方案几乎总是 `chmod +x nombre_script.sh`。

### 1.8 在 Linux 中执行脚本

**脚本（script）** 是一个文本文件，包含一系列自动执行的命令。要在 Linux 中运行脚本：

1. 文件必须具有执行权限（`chmod +x`）。
2. 必须告诉系统使用哪个解释器（第一行的 **shebang**）。
3. 使用 `./nombre_script.sh` 或 `bash nombre_script.sh` 执行。

**可执行脚本的基本结构：**

```bash
#!/bin/bash
# 这是一个注释
# 描述：示例脚本

echo "开始分析..."
mkdir -p resultados/
grep ">" secuencias.fasta > resultados/ids.txt
echo "处理完成。ID 已保存到 resultados/ids.txt"
```

**运行它：**
```bash
chmod +x mi_analisis.sh
./mi_analisis.sh
```

---

## 第二部分：生物信息学编程基础

### 2.1 为什么在生物信息学中需要编程？

终端命令很强大，但也有其局限性。当你需要：
- 基于数据内容做出**决策**。
- 对不同的文件或序列**重复**一个操作数千次。
- **计算**统计量、转换数据或构建模型。
- 创建带有可定制参数的**可复用**工具。

……那么你就需要编程。生物信息学中最常用的两种语言是 **Bash**（用于自动化工作流）和 **Python**（用于数据分析和工具开发）。

---

### 2.2 基本概念：什么是编程？

在编写任何一行代码之前，理解构建任何程序所依赖的概念是很重要的。我们将使用微生物学实验室的类比来使这些概念更加直观。

#### 🧪 算法（Algorithm）

想象一下你要执行一个 DNA 提取方案（protocol）。在开始之前，你在实验笔记本上写下了一系列有序的步骤：离心、弃上清液、加入裂解缓冲液、65°C 孵育 10 分钟，等等。每个步骤都依赖于前一个步骤，顺序很重要。

**算法**正是如此：一个有限且有序的指令序列，用于解决问题或执行任务。它还不是代码；它是过程的*逻辑*，独立于用来实现它的语言。每个程序在被转化为代码之前，都是从纸上的算法（或程序员脑海中的算法）开始的。

> [!NOTE]
> **类比：** 实验方案 = 算法。代码 = 用计算机能读取和执行的语言编写的方案。

#### 📄 脚本（Script）

**脚本**是一个文本文件，包含用特定编程语言（Bash, Python, R...）编写的算法，以便计算机逐行自动执行它。

如果算法是你的实验方案，那么脚本就是同一个方案，用计算机能理解的语言转录。优势在于：脚本可以在几秒内执行你可能需要数小时或数天手动完成的工作，并且可以以完全相同的方式一遍又一遍地重复，不会出现转录错误。

在生物信息学中，脚本是日常工作的工具：从提取 FASTA 文件中的序列，到编排完整的基因组分析流程（pipeline）。

---

#### 🧫 变量（Variables）：你的试管的标签

想象一下，在你的实验室中，你需要为不同的溶液或样本给试管贴标签，而这些试管的内容物可能在实验过程中发生变化。今天，"样本 A"试管含有 5 mL 的*大肠杆菌*培养物；明天，它可能含有经过不同处理的同一菌株。

在编程中，这些试管就是**变量**。变量是一个在计算机内存中存储数据的容器，就像试管标签一样，它有一个我们用来引用其内容的**名称**。最重要的是：变量中存储的值在程序执行期间**可以改变**。

**变量命名规则：**
- 名称必须以**字母**开头（不能以数字或特殊符号开头）。
- 可以包含字母、数字和下划线 `_`。
- **不允许有空格**（使用 `gc_content` 而不是 `gc content`）。
- 名称应该**具有描述性**：`longitud_gen` 比 `x` 更好。
- 大小写敏感：`Secuencia` 和 `secuencia` 是不同的变量。

```python
# ✅ 有效且具有描述性的名称
secuencia_adn = "ATGGCTAGC"
num_muestras = 24
gc_content = 0.573
organismo = "Escherichia coli"

# ❌ 无效的名称
2muestra = "..."      # 以数字开头
gc content = 0.5      # 包含空格
```
> [!TIP]
> **语法（Syntax）：** 语法是定义编程语言结构的一组规则。就像西班牙语有语法规则（主语 + 动词 + 谓语）一样，每种编程语言都有自己的语法。如果不遵守语法，计算机就无法解释代码，就像没有语法结构的句子无法正确传达信息一样。

---

#### ⚗️ 数据类型（Data Types）：试剂瓶里装的是什么物质？

一旦你有了一个变量（贴了标签的试剂瓶），你需要知道**它要存储的是哪种物质**：是液体、固体还是气体？是水溶液还是油？物质的类型决定了应该如何操作它。

在编程中，这被称为**数据类型**（*data type*）。每个变量都有一个数据类型，该类型决定了它可以存储哪种值，以及可以对它执行哪些操作。

基本数据类型有：

| 类型 | 实验室类比 | 描述 | 示例 |
|---|---|---|---|
| `int`（整数） | 计数的菌落数（无小数） | 整数，正数或负数 | `num_colonias = 156` |
| `float`（浮点数） | 体积（mL）或浓度（µg/µL） | 带小数的数字 | `concentracion = 0.45` |
| `str`（字符串） | 凝胶上写的序列或菌株名称 | 文本：引号内的字母、数字、符号 | `cepa = "E. coli K-12"` |
| `bool`（布尔值） | 测试结果：阳性或阴性 | 只有两个值：`True` 或 `False` | `es_patogena = True` |
| `list`（列表） | 一个有多个有序试管的试管架 | 有序且可修改的值集合 | `muestras = ["M1", "M2", "M3"]` |
| `dict`（字典） | 实验室笔记本：键 → 值 | 键值对，可通过名称访问 | `conteo = {"A": 45, "T": 42}` |
| `tuple`（元组） | 基因组中基因的固定坐标 | 有序且**不可变**的集合 | `coords = (1450, 2300)` |

> [!IMPORTANT]
> 在 Python 中，数据类型会根据存储的值**自动**分配。无需像其他语言那样显式声明。Python 推断 `num_colonias = 156` 是整数，`cepa = "E. coli"` 是字符串。

---

#### ➕ 运算符（Operators）：对样本的操作

**运算符**是指示程序对变量中存储的值执行何种操作的符号。它们相当于你在实验室中执行的操作：混合、测量、比较、决策。有四大类别：

| 运算符 | 类型 | 含义 | 生物信息学示例 | 结果 |
|---|---|---|---|---|
| `+` | 算术 | 加法 | `A + T + G + C` | `total_bases` |
| `-` | 算术 | 减法 | `longitud - gc` | 非 GC 碱基 |
| `*` | 算术 | 乘法 | `stock * factor_dilucion` | 最终浓度 |
| `/` | 算术 | 真除法 | `gc / longitud * 100` | 带小数的 GC% |
| `//` | 算术 | 整除（无小数） | `longitud // 3` | 密码子数 |
| `%` | 算术 | 取模 — 除法余数 | `longitud % 3` | 如果能被 3 整除则为 `0` |
| `**` | 算术 | 幂运算 | `4 ** 10` | 可能的 10 碱基 k-mer 数 |
| `=` | 赋值 | 将值赋给变量 | `gc_content = 0.57` | — |
| `+=` | 赋值 | 加后赋值 | `conteo += 1` | 自增 1 |
| `-=` | 赋值 | 减后赋值 | `restantes -= 1` | 自减 1 |
| `==` | 比较 | 等于 | `organismo == "E. coli"` | `True` 或 `False` |
| `!=` | 比较 | 不等于 | `organismo != "humano"` | `True` 或 `False` |
| `>` | 比较 | 大于 | `longitud > 500` | `True` 或 `False` |
| `<` | 比较 | 小于 | `calidad < 30` | `True` 或 `False` |
| `>=` | 比较 | 大于等于 | `cobertura >= 20` | `True` 或 `False` |
| `<=` | 比较 | 小于等于 | `longitud <= 200` | `True` 或 `False` |
| `and` | 逻辑 | 两者都为真 | `gc > 0.4 and longitud > 200` | 仅当两者都为 `True` 时为 `True` |
| `or` | 逻辑 | 至少一个为真 | `org == "E. coli" or org == "Salmonella"` | 如果有任何一个为 `True` 则为 `True` |
| `not` | 逻辑 | 反转结果 | `not es_patogena` | 如果原本是 `False` 则为 `True` |
| `in` | 成员 | 检查元素是否在集合中 | `"A" in secuencia` | `True` 或 `False` |
| `not in` | 成员 | 检查元素是否不在集合中 | `"X" not in nucleotidos` | `True` 或 `False` |

> [!TIP]
> **常见错误：** `=` 和 `==` 完全不同。`=` **赋值**一个值（`gc = 0.5` 将数字存入变量）。`==` **比较**两个值（`gc == 0.5` 询问它们是否相等，返回 `True` 或 `False`）。混淆二者是初学者编程时最常见的错误之一。

---

#### 🔬 逻辑运算符与真值表

逻辑运算符（`and`, `or`, `not`）处理布尔值（`True`/`False`），是程序中任何决策系统的核心。要理解它们的行为，我们使用**真值表**。

**实验室类比：** 想象你有两种诊断测试来鉴定一种细菌：**革兰氏染色**和**过氧化氢酶测试**。根据使用的逻辑运算符，组合结果会发生变化。

**运算符 `and` — 两个条件都必须为真：**

> *"该细菌是革兰氏阳性 **且** 过氧化氢酶阳性"* → 只有当两项测试都呈阳性时才成立。

| 条件 A | 条件 B | A `and` B |
|---|---|---|
| `True` | `True` | ✅ `True` |
| `True` | `False` | ❌ `False` |
| `False` | `True` | ❌ `False` |
| `False` | `False` | ❌ `False` |

**运算符 `or` — 至少一个条件必须为真：**

> *"该细菌是革兰氏阳性 **或** 产生 β-内酰胺酶"* → 只要两者中有一个成立即可。

| 条件 A | 条件 B | A `or` B |
|---|---|---|
| `True` | `True` | ✅ `True` |
| `True` | `False` | ✅ `True` |
| `False` | `True` | ✅ `True` |
| `False` | `False` | ❌ `False` |

**运算符 `not` — 反转结果：**

> *"该细菌**不**是革兰氏阴性"* → 如果原本是 `False`，就变成 `True`，反之亦然。

| 条件 A | `not` A |
|---|---|
| `True` | ❌ `False` |
| `False` | ✅ `True` |

---

> [!IMPORTANT]
> **常见疑问：`False and False` 不应该是 `True` 吗？因为"两者都一样满足"？**
>
> 不是。关键在于理解**每个运算符问的是什么问题**：
>
> - `and` 问：*"两个条件都是**真**的吗？"* — 它不是问它们是否相等。
> - `False and False` 的意思是：`False` 是真的吗？→ 不是。`False` 是真的吗？→ 不是。→ 结果：`False`。
>
> 两个值彼此相等的事实对 `and` 来说**不重要**。唯一重要的是每个值是否为 `True`。
>
> **类比：** 实验室规则：*"只有当样本已解冻**且**已贴标签时，你才能使用它。"*
>
> | 是否已解冻？ | 是否已贴标签？ | 可以使用吗？（`and`） |
> |---|---|---|
> | ✅ 是 | ✅ 是 | ✅ 可以 |
> | ✅ 是 | ❌ 否 | ❌ 不可以 |
> | ❌ 否 | ✅ 是 | ❌ 不可以 |
> | ❌ 否 | ❌ 否 | ❌ 不可以 |
>
> 在最后一行，样本**既没有**解冻，**也没有**贴标签。两个条件都以相同方式失败的事实并不改变结果：仍然是**不可以**。
>
> **如果你想问的是"它们相等吗？"，那应该用 `==` 运算符，而不是 `and`：**
>
> ```python
> False == False   # → True  ✅（它们彼此相等）
> True  == True    # → True  ✅（它们彼此相等）
> False == True    # → False ❌（它们不相等）
>
> False and False  # → False ❌（两者都不是真的）
> ```
>
> | 表达式 | 它所问的问题 | 结果 |
> |---|---|---|
> | `False and False` | 两者都是**真的**吗？ | ❌ `False` |
> | `False or False` | **有任何一个**是真的吗？ | ❌ `False` |
> | `not False` | `False` 的反面是什么？ | ✅ `True` |
> | `False == False` | 它们彼此**相等**吗？ | ✅ `True` |

---

#### 🔢 运算顺序与括号的使用

就像数学中一样，运算符有**优先级顺序**：某些运算符先于其他运算符被计算。当存在歧义时，计算机遵循此顺序（从最高到最低优先级）：

| 优先级 | 运算符 | 描述 |
|---|---|---|
| 1（最高） | `( )` | **括号** — 括号内的内容首先计算 |
| 2 | `**` | 幂运算 |
| 3 | `*`, `/`, `//`, `%` | 乘法和除法 |
| 4 | `+`, `-` | 加法和减法 |
| 5 | `==`, `!=`, `<`, `>`, `<=`, `>=` | 比较 |
| 6 | `not` | 逻辑非 |
| 7 | `and` | 逻辑与 |
| 8（最低） | `or` | 逻辑或 |

**括号是控制运算顺序和使代码更易读的最佳工具：**

```python
# 无括号 — 可能产生歧义或意外结果
resultado = gc > 0.4 and longitud > 200 or es_patogena

# 有括号 — 意图清晰，按期望的顺序计算
resultado = (gc > 0.4 and longitud > 200) or es_patogena

# 算术示例：正确计算 GC%
# ❌ 无括号：先将 gc 除以 longitud，再加上 C
porcentaje = G + C / longitud * 100

# ✅ 有括号：先将 G+C 相加，再除以 longitud
porcentaje = (G + C) / longitud * 100
```
> [!TIP]
> **黄金法则：** 当在一行中组合多个逻辑或算术运算符时，即使严格来说不需要，也请使用括号。你的代码会更清晰，并避免难以发现的错误。

---

#### 🔀 条件结构（Conditionals）：方案中的决策点

在实验室中，许多方案包含决策点：*"如果 pH 小于 7，加入中和缓冲液；如果在 7 到 8 之间，继续；如果大于 8，丢弃样本"*。这种决策逻辑在编程中也存在，称为**条件结构**。

条件结构允许程序**根据条件是否为真来执行不同的代码块**。这是程序基于数据做出智能决策的基本机制。

> [!NOTE]
> **类比：** 它就像微生物临床诊断流程图中的决策树：*"革兰氏染色阳性？"* → 是 → *"形态为葡萄串状？"* → 是 → 可能是*葡萄球菌*（*Staphylococcus*）。树的每个分支都依赖于前一个结果。

**基本结构（`if` / `elif` / `else`）：**

```
如果 <条件> 为真：
    执行此代码块
否则，如果 <另一个条件> 为真：
    执行另一个代码块
如果以上都不成立：
    执行默认代码块
```

**Python 中：**

```python
gc_content = 0.65

# if: 如果条件为 True 则执行
if gc_content > 0.60:
    print("高 GC 含量 — 可能是放线菌门（Actinobacteria）")

# elif (else if): 备选条件，仅在前一个条件为 False 时才计算
elif gc_content >= 0.40:
    print("中等 GC 含量")

# else: 如果前面的条件都不为 True 则执行
else:
    print("低 GC 含量 — 可能是低 GC 厚壁菌门（Firmicutes）")
```

**结合逻辑运算符的条件判断：**

```python
longitud = 850
gc = 0.58
es_completo = True

# and: 两个条件都必须满足
if longitud > 500 and gc > 0.50:
    print("长序列，高 GC 含量")

# or: 至少一个条件必须满足
if longitud < 100 or gc < 0.30:
    print("⚠️ 可疑序列：太短或 GC 含量太低")

# not: 取反
if not es_completo:
    print("警告：基因组不完整")

# 使用括号组合，使意图更清晰
if (longitud > 200 and gc > 0.40) or es_completo:
    print("序列可用于分析")
```

**嵌套条件** — 一个 `if` 嵌套在另一个 `if` 内，就像诊断树的各个分支：

```python
gram = "positivo"
morfologia = "cocos"
agrupacion = "racimos"

if gram == "positivo":
    if morfologia == "cocos":
        if agrupacion == "racimos":
            print("可能为：Staphylococcus（葡萄球菌）")
        elif agrupacion == "cadenas":
            print("可能为：Streptococcus（链球菌）")
        else:
            print("革兰氏阳性球菌 — 排列方式未确定")
    elif morfologia == "bacilos":
        print("可能为：Bacillus 或 Clostridium")
else:
    print("革兰氏阴性 — 继续进行其他测试")
```

**Bash 中：**

```bash
#!/bin/bash
ARCHIVO="datos.fasta"
MIN_SEQS=10

# 检查文件是否存在
if [ -f "$ARCHIVO" ]; then
    NUM_SEQS=$(grep -c ">" "$ARCHIVO")

    # 嵌套条件：检查最低序列数量
    if [ "$NUM_SEQS" -ge "$MIN_SEQS" ]; then
        echo "✅ 有效文件，包含 $NUM_SEQS 条序列。正在处理..."
    else
        echo "⚠️ 仅找到 $NUM_SEQS 条序列。至少需要 $MIN_SEQS 条。"
    fi
else
    echo "❌ 错误：未找到文件 $ARCHIVO。"
    exit 1
fi
```

**Bash 中的比较运算符**（语法与 Python 不同）：

| Bash 运算符 | 类型 | Python 等价 | 含义 |
|---|---|---|---|
| `-eq` | 数值 | `==` | 等于 |
| `-ne` | 数值 | `!=` | 不等于 |
| `-gt` | 数值 | `>` | 大于 |
| `-lt` | 数值 | `<` | 小于 |
| `-ge` | 数值 | `>=` | 大于等于 |
| `-le` | 数值 | `<=` | 小于等于 |
| `==` | 文本 | `==` | 等于（字符串） |
| `!=` | 文本 | `!=` | 不等于（字符串） |
| `-f` | 文件 | — | 文件存在 |
| `-d` | 目录 | — | 目录存在 |
| `-z` | 文本 | `== ""` | 字符串为空 |
| `!` | 逻辑 | `not` | 取反 |
| `-a` | 逻辑 | `and` | 逻辑与（在 `[ ]` 内使用） |
| `-o` | 逻辑 | `or` | 逻辑或（在 `[ ]` 内使用） |

---

#### 🔄 循环（Loops）：实验室的重复性过程

想象一下你需要测量 96 孔 ELISA 板中每个样本的吸光度。每个孔的操作完全相同：读取数值、记录下来、进行下一个。写 96 遍相同的指令是低效的（也无法想象）。

**循环**（*loop*）允许你多次重复一个指令块，无论是固定次数（`for`）还是在满足某个条件时（`while`）。在生物信息学中，循环无处不在：处理 FASTA 文件中的每条序列、FASTQ 文件中的每条读长、注释中的每个基因。

> [!NOTE]
> **类比：** 循环就像测序仪的机械臂：对数以千计的样本执行完全相同的操作，不知疲倦且不出错。

---

#### 🧩 函数（Functions）：标准化且可复用的方案

在一个组织良好的实验室中，最常用的方案会被标准化并记录下来：DNA 提取、PCR、电泳。一旦方案经过验证，实验室的任何成员都可以遵循它，而无需每次都重新发明流程。

在编程中，这是通过**函数**来实现的。函数是一个有名称的代码块，执行特定任务，可以在程序的任何地方被调用（执行）任意次数，传递不同的输入数据（**参数**）并获得输出结果（**返回值**）。

> [!NOTE]
> **类比：** 函数 = 标准化方案。参数 = 你传递给方案的样本。返回值 = 你从方案中获得的结果。

### 2.3 Bash 脚本入门

**Bash**（*Bourne Again SHell*）是 Linux 的原生脚本语言。它非常适合编排生物信息学工具、自动化工作流和批量处理文件。

#### Bash 中的变量

```bash
#!/bin/bash
# 变量声明（= 周围不能有空格）
ARCHIVO="secuencias.fasta"
ORGANISMO="E. coli"
NUM_SECUENCIAS=$(grep -c ">" $ARCHIVO)   # 捕获命令的输出

echo "正在分析：$ORGANISMO"
echo "$ARCHIVO 中的序列数：$NUM_SECUENCIAS"
```

> [!TIP]
> Bash 中的条件示例和完整的比较运算符表见第 2.2 节。那里也解释了真值表和括号的使用。

#### Bash 中的循环

循环对于自动处理多个生物文件至关重要：

```bash
#!/bin/bash
# 处理目录中的所有 FASTA 文件
for ARCHIVO in *.fasta; do
    echo "正在处理：$ARCHIVO"
    NUM_SEQ=$(grep -c ">" "$ARCHIVO")
    echo "  → 找到 $NUM_SEQ 条序列"
done
```

```bash
#!/bin/bash
# while 循环：逐行读取样本列表文件
while IFS= read -r MUESTRA; do
    echo "正在下载数据：$MUESTRA"
    # 下载命令在此处
done < lista_muestras.txt
```

#### Bash 中的函数

```bash
#!/bin/bash
# 定义一个可复用的函数
contar_secuencias() {
    local archivo=$1
    local count=$(grep -c ">" "$archivo")
    echo "$count"
}

# 调用函数
TOTAL=$(contar_secuencias "genoma.fasta")
echo "序列总数：$TOTAL"
```

### 2.4 Python 生物信息学入门

**Python** 是当今生物信息学中最流行的编程语言。其清晰的语法、庞大的社区以及专业库的可用性（如 **Biopython**、**Pandas**、**NumPy**）使其成为生物数据分析的理想选择。

下面展示如何在 Python 中实现第 2.2 节中定义的概念。

#### Python 中的数据类型和变量

上述所有数据类型（见第 2.2 节）在 Python 中以直接方式声明，无需指定类型：Python 根据分配的值自动推断。

```python
# 整数（int）
longitud_gen = 1200

# 浮点数（float）
gc_content = 0.573

# 字符串（str） — 处理序列的基础
secuencia = "ATGGCTAGCTAGCTAGC"
organismo = "Escherichia coli"

# 布尔值（bool）
es_codificante = True

# 列表 — 有序且可修改的集合
nucleotidos = ["A", "T", "G", "C"]
longitudes = [1200, 850, 2340, 670]

# 字典 — 键值对，用于统计核苷酸数量非常理想
conteo = {"A": 45, "T": 42, "G": 38, "C": 40}

# 元组 — 有序且不可变的集合
coordenadas_gen = (1450, 2300)
```

#### 变量和运算符

```python
# 变量赋值
secuencia = "ATGGCTAGCTAGCTAGC"
longitud = len(secuencia)       # len() 函数获取长度

# 算术运算符
gc = secuencia.count("G") + secuencia.count("C")
gc_porcentaje = (gc / longitud) * 100

# 比较运算符
print(gc_porcentaje > 50)       # True 或 False
print(longitud == 18)           # True

# 字符串（序列）操作
complemento = secuencia.replace("A","t").replace("T","a").replace("G","c").replace("C","g").upper()
print(secuencia[::-1])          # 反向序列
```

#### Python 中的条件结构

> [!NOTE]
> 概念、真值表和括号的使用已在第 2.2 节中解释。这里展示 Python 中的实现及序列分析示例。

```python
gc_content = 0.65

if gc_content > 0.60:
    print("高 GC 含量 — 可能是放线菌门或高 GC 厚壁菌门")
elif gc_content >= 0.40:
    print("中等 GC 含量")
else:
    print("低 GC 含量")
```

#### Python 中的循环

```python
# 遍历一条 DNA 序列
secuencia = "ATGGCTAGCTAGCTAGC"

# 统计每个核苷酸的数量
conteo = {"A": 0, "T": 0, "G": 0, "C": 0}

for nucleotido in secuencia:
    if nucleotido in conteo:
        conteo[nucleotido] += 1

print(conteo)
# 输出：{'A': 2, 'T': 3, 'G': 5, 'C': 4, ...}

# 使用 range 的 for 循环 — 处理密码子
for i in range(0, len(secuencia)-2, 3):
    codon = secuencia[i:i+3]
    print(f"密码子：{codon}")
```

#### Python 中的函数

函数允许封装逻辑并复用。在生物信息学中，它们经常用于处理序列：

```python
def calcular_gc(secuencia):
    """
    计算 DNA 序列的 GC 含量。

    Args:
        secuencia (str): 大写 DNA 序列。

    Returns:
        float: GC 百分比 (0-100)。
    """
    secuencia = secuencia.upper()
    gc = secuencia.count("G") + secuencia.count("C")
    return (gc / len(secuencia)) * 100


def leer_fasta(ruta_archivo):
    """
    读取 FASTA 文件并返回一个字典 {id: 序列}。
    """
    secuencias = {}
    id_actual = None
    with open(ruta_archivo, "r") as f:
        for linea in f:
            linea = linea.strip()
            if linea.startswith(">"):
                id_actual = linea[1:]
                secuencias[id_actual] = ""
            else:
                secuencias[id_actual] += linea
    return secuencias


# 使用函数
secuencias = leer_fasta("bacterias.fasta")

for nombre, seq in secuencias.items():
    gc = calcular_gc(seq)
    print(f"{nombre}: GC = {gc:.2f}%")
```

### 2.5 编程在基因序列分析中的应用

当编程应用于实际问题时才有意义。以下是生物信息学中常规执行的一些操作类型，它们将通过代码来实现自动化：

#### FASTA 文件操作

FASTA 文件是最通用的序列格式。使用 Python 可以：
- 读取和解析包含数千条序列的文件。
- 按长度、GC 含量或其他标准筛选序列。
- 重命名标识符、删除重复项以及重新格式化标题行。
- 将大型 FASTA 文件分割成较小的批次。

#### 核苷酸组成计数和分析

任何基因组分析中的第一个指标是碱基组成：
- **GC 含量：** 物种的指标（高 GC 细菌 vs 低 GC 细菌）。
- **密码子频率：** 对蛋白质异源表达很重要。
- **k-mer 分布：** 许多基因组组装器的基础。

#### 使用脚本进行批量处理

一项基本任务是批量处理数十或数百个文件。Python 或 Bash 脚本可以：
- 遍历目录中的所有 `.fastq` 文件。
- 对每个文件应用相同的分析。
- 生成包含所有结果的综合报告。

```python
import os

directorio = "datos_fastq/"
reporte = []

for archivo in os.listdir(directorio):
    if archivo.endswith(".fastq"):
        ruta = os.path.join(directorio, archivo)
        with open(ruta) as f:
            lineas = f.readlines()
        num_lecturas = len(lineas) // 4   # 每条 FASTQ 读长占用 4 行
        reporte.append(f"{archivo}: {num_lecturas} 条读长")

# 保存报告
with open("reporte_muestras.txt", "w") as out:
    out.write("\n".join(reporte))

print("报告已生成。")
```

### 2.6 生物信息学编程最佳实践

生物信息学代码不仅要能运行：还必须是**可读的**、**可复现的**和**可维护的**。一些基本的最佳实践：

**文档和注释：**
```python
# ✅ 好的注释：解释"为什么"，而不是"做什么"
# 删除长度小于 200 bp 的序列，因为它们是测序伪影
secuencias_filtradas = {k: v for k, v in secuencias.items() if len(v) >= 200}

# ❌ 不好的注释：只是重复代码已经说明的内容
# 筛选序列
secuencias_filtradas = {k: v for k, v in secuencias.items() if len(v) >= 200}
```

**项目组织：**
```
proyecto_genomica/
├── data/
│   ├── raw/          → 原始数据（永不修改）
│   └── processed/    → 分析后的数据
├── code/             → 分析代码或脚本
├── results/          → 分析输出结果
│   ├── raw/          → 原始数据（永不修改）
│   └── processed/    → 分析后的数据
├── docs/             → 报告或发表用文本相关全部内容
│   ├── reports/      → 提交给导师或合作者的报告
│   └── text/         → 用于期刊发表的文本，理想情况下添加三个子文件夹：text, figures, supplementary
└── README.md         → 项目描述
```

> [!TIP]
> **不想每次都手动创建此结构？** 有一个专为计算生物学项目设计的 GitHub 模板，可以自动生成此结构。你可以将其用作任何项目的起点：
> **[github.com/ae-tafur/computational-biology-projects](https://github.com/ae-tafur/computational-biology-projects)**
> 要使用它，前往该仓库并点击 **"Use this template"** → **"Create a new repository"**。GitHub 将在你的账户中创建一个新仓库，包含所有现成的文件夹结构。

**其他最佳实践：**
- 为变量和函数使用**描述性名称**（`gc_content` 而不是 `x`）。
- **永远不要修改原始数据**；始终在副本上工作。
- 记录使用的**软件版本**（Python 3.11, Biopython 1.81...）。
- 对所有脚本使用**版本控制**（Git/GitHub）。
- 编写在出错时能**给出清晰错误信息**的脚本。

## 模块总结

本模块为接下来的所有生物信息学工作奠定了计算基础。Linux 命令行，结合编写 Bash 和 Python 脚本的能力，让你能够触及几乎整个生物信息学工具生态系统。

| 技能 | 工具 | 生物信息学应用 |
|---|---|---|
| 系统导航 | Linux 终端 | 组织项目、访问服务器 |
| 文件操作 | `grep`, `awk`, `sed` | 从终端处理 FASTA, GFF, FASTQ 文件 |
| 自动化 | Bash 脚本 | 数据处理流程（pipeline）、批量处理 |
| 数据分析 | Python | 解析序列、计算统计量、筛选数据 |
| 可复现性 | Git + GitHub | 代码版本控制、分享分析 |

> [!TIP]
> 请记住：编程是一项通过持续练习获得的技能。如果一开始命令或代码对你来说不够直观，不用担心：通过每一次练习、每一个错误和每一个找到的解决方案，你都在构建一种能力，它将改变你做科学的方式。

---

## 附加资源

- **The Linux Command Line** (William Shotts) — 免费在线书籍：https://linuxcommand.org/tlcl.php
- **Python for Biologists** — https://pythonforbiologists.com/
- **Biopython Tutorial** — https://biopython.org/DIST/docs/tutorial/Tutorial.html
- **Software Carpentry: Unix Shell** — https://swcarpentry.github.io/shell-novice/
- **Software Carpentry: Programming with Python** — https://swcarpentry.github.io/python-novice-inflammation/
- **Rosalind**（生物信息学实践练习）— https://rosalind.info/

---

## 模块练习

| 练习 | 描述 |
|---|---|
| [创建 GitHub 账户](./exercises/01_creating_an_github_account.md) | 配置 GitHub 和 GitHub Codespaces 作为本课程的工作环境 |
| [使用 Unix 终端](./exercises/02_using_unix_terminal.md) | 文件系统导航、使用管道和重定向操作生物文件 |
| [Python 脚本编程](./exercises/03_scripting_in_python.md) | 变量、数据类型、条件判断、循环、文件读取以及用于生物分析的脚本 |
