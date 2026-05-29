# 🐍 练习：面向生物信息学的 Python 编程入门

## 引言

到目前为止，你已经学会了导航文件系统以及从 Unix 终端操作生物文件。在本练习中，你将迈出重要的一步：使用 **Python** 编程——当今生物信息学中使用最广泛的语言。

Python 能让你超越终端命令的限制：根据数据做出决策、对数以千计的序列重复操作、计算统计量以及构建可复用的工具。在本练习中，你将从基础开始——变量、数据类型、运算符和控制结构——逐步进展到编写完整的脚本来分析你在前一练习中已经熟悉的那些生物文件。

本练习对应**模块 2 的第 2 部分**。这里应用的所有理论概念（变量、数据类型、运算符、条件判断、循环和函数）在模块 README 的第 2.2 至 2.4 节中有详细解释。如果在练习中遇到概念上的疑惑，请先查阅该参考资料再继续。

---

## 🎯 目标

- 遵守 Python 的命名规则和数据类型来声明变量。
- 在生物数据上使用算术、比较和逻辑运算符。
- 使用条件结构（`if/elif/else`）和循环（`for`, `while`）处理序列和表格。
- 从 Python 脚本读取生物文件（FASTA, CSV, TSV）。
- 编写完整的脚本来解决真实的生物分析问题。

---

## 📦 前提条件

- 已完成**练习 1 和 2**（GitHub Codespaces 和 Unix 终端）。
- 已阅读模块 2 README 的**第 2 部分**（第 2.2 至 2.4 节）。
- 已打开课程仓库并具有活跃的 Codespace。

> [!TIP]
> **你已经有活跃的 Codespace 了吗？** 请复用它。前往 [github.com/codespaces](https://github.com/codespaces)，选择你已创建的那个，从上次离开的地方继续。不要每次都创建新的。

---

## 🗂️ 可用的数据文件

所有文件都位于 `data/` 文件夹中。你将在练习过程中逐步使用它们：

| 文件 | 格式 | 描述 |
|---|---|---|
| `01_sequences.fasta` | FASTA | 10 条 DNA 序列 |
| `02_results_pcr_96.csv` | CSV | 96 孔板的 PCR 结果 |
| `03_nucleotides.fa` | FASTA | 单条 DNA 序列 |
| `05_genome.gff3` | GFF3 | 两条染色体中的基因注释 |
| `06_bacterial_growth.tsv` | TSV | 三种细菌菌株的 OD600 吸光度 |
| `07_mutations.fasta` | FASTA | 参考序列和两个突变体 |
| `08_antibiogram.csv` | CSV | 药敏试验结果（R/S/I） |
| `09_short_sequences.fasta` | FASTA | 不同长度的序列 |
| `10_temperature.csv` | CSV | 三次实验中的孵育温度 |
| `11_pcr_ct.csv` | CSV | 定量 PCR 的 Ct 值 |
| `12_gene_annotations.tsv` | TSV | 基因的功能注释（KO IDs） |
| `13_glucose_comsumption.tsv` | TSV | 10 个培养物在 3 个重复中的葡萄糖消耗量 |
| `14_bacterias_muestreo.csv` | CSV | 环境采样中的细菌鉴定结果 |
| `15_aa_sequences.fa` | FASTA | 蛋白质序列（氨基酸） |

---

## 🔬 操作步骤

### 第 1 部分：第一步 — 变量、数据类型和常见错误

在这一部分，你将从零开始**自己创建变量**，运用你在理论中学到的规则。目标不仅仅是让代码运行：而是要理解当出现问题时**为什么会失败**。

#### 步骤 1：打开 Python 交互式解释器

在 Codespaces 终端中，输入：

```bash
python3
```

你将看到提示符 `>>>`，这表明 Python 已准备好逐行接收指令。完成操作后，输入 `exit()` 退出。

---

#### 步骤 2：创建你的第一个变量 — 先思考再动手

> 📝 **在打开 Codespaces 之前，请拿出一张纸。**
> 对于下面的每个变量，你会看到一个题目，然后是**两种或三种声明方式**。在纸上：
> 1. 指出你认为哪些是**正确的**，哪些会**失败**。
> 2. 对于你认为会失败的，写下你认为它们**为什么会失败**。
> 3. 只有在写下你的答案之后，才在 Python 解释器中测试它们并比较结果。
>
> 纸上没有错误的答案——目标是让你先推理，然后再与 Python 反馈的结果进行对照。

---

**变量 1 — 研究者的姓名**

一位研究者名叫 *Ana García*（安娜·加西亚）。她需要将她的名字存储在一个名为 `nombre` 的变量中。

在纸上评估每个选项，然后进行测试：

```python
# 选项 A
nombre = Ana García

# 选项 B
nombre = "Ana García"

# 选项 C
nombre = 'Ana García'

# 选项 D
Ana García = "investigadora"
```

在解释器中逐一测试。然后回答：
- 哪些成功运行了？哪些失败了？
- 你的预测与结果一致吗？
- 成功运行的那些有什么共同点？
- `nombre` 是哪种数据类型？用 `print(type(nombre))` 验证。

<details>
<summary>💡 查看解释（请仅在尝试后打开）</summary>

- **选项 A** 失败：`SyntaxError`。没有引号，Python 将 `Ana` 和 `García` 解释为两个独立的变量，而不是一个文本值。
- **选项 B** ✅ 成功：双引号内的文本是一个 `str`（字符串）。
- **选项 C** ✅ 成功：Python 对文本同样接受单引号或双引号。
- **选项 D** 失败：`SyntaxError`。变量名不能包含空格。`Ana García`（带空格）作为变量名是无效的。

</details>

---

**变量 2 — 年龄**

一名学生 28 岁。他需要存储年龄，然后加 1 来计算明年的年龄。

在纸上评估每个选项，然后进行测试：

```python
# 选项 A
edad = 28
print(edad + 1)

# 选项 B
edad = "28"
print(edad + 1)

# 选项 C
edad = "28"
print(edad + "1")

# 选项 D
edad = 28
edad_texto = "1"
print(edad + edad_texto)
```

逐一测试。然后回答：
- 哪些按预期运行了？
- 哪些报错了？哪些产生了一个意想不到的*意外结果*而没有报错？
- 在 Python 中，两个数字相加与两个文本"相加"有什么不同？

<details>
<summary>💡 查看解释（请仅在尝试后打开）</summary>

- **选项 A** ✅ 成功：`28 + 1 = 29`。正常的数值加法。
- **选项 B** 失败：`TypeError`。`"28"` 是一个 `str`，不是 `int`。即使文本看起来像数字，Python 也不能将文本与数字相加。
- **选项 C** ✅ 没有失败，但结果是 `"281"`，而不是 `29`。当两个 `str` "相加"时，Python 将它们**拼接**（concatenate）在一起。它不执行算术加法。
- **选项 D** 失败：`TypeError`。与 B 同样的原因：不能将 `int` 和 `str` 用 `+` 混合。

**解决方法** — 当你需要对以文本形式出现的数字进行操作时（这在读取 CSV 文件时非常常见）：
```python
edad = "28"
print(int(edad) + 1)    # → 29  （先将 str 转换为 int）
```
这被称为**类型转换（casting）**，在生物信息学中至关重要，因为当 Python 读取文件时，所有值默认都是以 `str` 形式到达的。

</details>

---

**变量 3 — 实验室地址**

你需要将地址 *"Calle 50 # 10-25, Laboratorio 302"*（第 50 街 10-25 号，302 实验室）存储在一个变量中。

在纸上评估每个选项，然后进行测试：

```python
# 选项 A
mi dirección = "Calle 50 # 10-25, Laboratorio 302"

# 选项 B
2direccion = "Calle 50 # 10-25, Laboratorio 302"

# 选项 C
mi_direccion = "Calle 50 # 10-25, Laboratorio 302"

# 选项 D
MiDireccion = "Calle 50 # 10-25, Laboratorio 302"

# 选项 E
mi_direccion = Calle 50 # 10-25, Laboratorio 302
```

逐一测试。然后回答：
- 哪些成功运行了？
- 那些失败了的违反了哪条变量命名规则？
- 选项 C 和 D 都成功，但哪个更易读？按 Python 的惯例，哪个更常用？

<details>
<summary>💡 查看解释（请仅在尝试后打开）</summary>

- **选项 A** 失败：`SyntaxError`。变量名中不允许有空格（`mi dirección`）。
- **选项 B** 失败：`SyntaxError`。变量名不能以数字开头。
- **选项 C** ✅ 成功。使用了 `snake_case`（以下划线连接单词）：Python 的约定风格。
- **选项 D** ✅ 成功。使用了 `CamelCase`（首字母大写的单词）。这是有效的，但在 Python 中按约定保留给类名，不用于变量。
- **选项 E** 失败：`SyntaxError`。值没有用引号括起来，因此 Python 试图将 `Calle` 解释为一个不存在的变量。

**Python 变量命名规则：**
- 只能包含字母、数字和下划线 `_`。
- 不能以数字开头。
- 不能包含空格。
- 大小写敏感：`Mi_Direccion` ≠ `mi_direccion`。
- Python 约定：对变量和函数使用 `snake_case`。

</details>

---

**变量 4 — 混合类型的实验数据：**

现在声明一组代表实验室样本的变量并对它们进行操作：

```python
cepa           = "Escherichia coli"
num_colonias   = 156
concentracion  = 0.45        # µg/µL
es_patogena    = True
nucleotidos    = ["A", "T", "G", "C"]
conteo_bases   = {"A": 45, "T": 42, "G": 38, "C": 40}

# 验证每个变量的类型
print(type(cepa))
print(type(num_colonias))
print(type(concentracion))
print(type(es_patogena))
print(type(nucleotidos))
print(type(conteo_bases))
```

对它们进行操作：
```python
# 通过索引访问列表元素（从 0 开始）
print(nucleotidos[0])    # → "A"
print(nucleotidos[-1])   # → "C" （最后一个元素）

# 通过键访问字典值
print(conteo_bases["G"])         # → 38
print(conteo_bases["A"] + conteo_bases["T"])   # → 87

# 计算碱基总数
total = sum(conteo_bases.values())
print(f"碱基总数：{total}")

# 直接从字典计算 GC%
gc = conteo_bases["G"] + conteo_bases["C"]
gc_porcentaje = (gc / total) * 100
print(f"GC 含量：{gc_porcentaje:.2f}%")
```

> [!TIP]
> **字典常见错误：** 尝试访问一个不存在的键：
> ```python
> print(conteo_bases["N"])   # KeyError: 'N'
> ```
> 使用 `.get()` 可以避免错误：`conteo_bases.get("N", 0)` 会在键不存在时返回 `0`。

---

#### 步骤 3：退出解释器并创建脚本

交互式解释器适用于快速测试，但实际工作中使用**脚本**（`.py` 文件）。退出解释器：

```python
exit()
```

创建你的第一个 Python 脚本：
```bash
touch mi_primer_script.py
```

在编辑器（Codespaces 左侧面板）中打开该文件，将以下所有代码复制粘贴进去：

```python
#!/usr/bin/env python3
"""
我的第一个生物信息学 Python 脚本。
"""

# 实验变量
cepa          = "Escherichia coli K-12"
num_colonias  = 156
gc_content    = 0.508
es_patogena   = False

# 打印基本报告
print("=== 样本报告 ===")
print(f"菌株：{cepa}")
print(f"计数的菌落数：{num_colonias}")
print(f"GC 含量：{gc_content * 100:.1f}%")
print(f"是否致病？：{es_patogena}")

# 基于 GC 的条件判断
if gc_content > 0.60:
    print("高 GC 含量")
elif gc_content >= 0.40:
    print("中等 GC 含量")
else:
    print("低 GC 含量")
```

从终端运行脚本：
```bash
python3 mi_primer_script.py
```

---

### 第 2 部分：使用生物数据进行运算符、条件和循环操作

#### 步骤 4：序列上的运算符操作

再次打开 Python 解释器（`python3`），使用 `03_nucleotides.fa` 文件中的序列进行操作：

```python
secuencia = "TACTTGATTTTATTACTACAGGATCCAAACTGGCTAGTATCGGATTCAAGGACAGGCTAATATGTAGACTATCCTCCAGATAACGAATCAGGCAAATGCCTCCTAGGGGTATTGCAGATATTTAAGCGTCAGTGGTAAAATCTGTTCGTCAGTGCGCTCCGTGGGATCGTGACGACGGCTCAATCTACATTCAGTCCAAC"

# 长度
longitud = len(secuencia)
print(f"长度：{longitud} 个碱基")

# 核苷酸计数
A = secuencia.count("A")
T = secuencia.count("T")
G = secuencia.count("G")
C = secuencia.count("C")
print(f"A={A}, T={T}, G={G}, C={C}")

# 验证它们之和等于总长度
print(f"总和：{A+T+G+C} == {longitud}: {A+T+G+C == longitud}")

# GC%
gc_porcentaje = (G + C) / longitud * 100
print(f"GC%：{gc_porcentaje:.2f}%")

# 可能的密码子数（不考虑阅读框）
num_codones = longitud // 3
residuo = longitud % 3
print(f"密码子数：{num_codones}，剩余碱基数：{residuo}")
```

#### 步骤 5：药敏试验数据的条件判断

```python
# 药敏试验数据（08_antibiogram.csv）
resultados = [
    ("E.coli",       "Ampicilina",      "R"),
    ("E.coli",       "Ciprofloxacina",  "S"),
    ("S.aureus",     "Ampicilina",      "R"),
    ("S.aureus",     "Eritromicina",    "I"),
    ("K.pneumoniae", "Ampicilina",      "S"),
]

print("=== 药敏试验解读 ===")
for cepa, antibiotico, resultado in resultados:
    if resultado == "R":
        interpretacion = "⛔ 耐药（Resistant）— 请勿使用该抗生素"
    elif resultado == "I":
        interpretacion = "⚠️  中介（Intermediate）— 谨慎使用"
    else:
        interpretacion = "✅ 敏感（Susceptible）— 治疗可行"
    print(f"{cepa} | {antibiotico}: {interpretacion}")

```

#### 步骤 6：FASTA 序列上的循环操作

```python
# 模拟 07_mutations.fasta 的内容
secuencias = {
    "Ref":  "ATGCGTACGTTAGC",
    "Mut1": "ATGCGTACGCTAGC",
    "Mut2": "ATGCGTACGCTTGC",
}

referencia = secuencias["Ref"]

print("=== 突变检测 ===")
for nombre, seq in secuencias.items():
    if nombre == "Ref":
        continue   # 跳过参考序列

    mutaciones = 0
    posiciones = []
    for i in range(len(referencia)):
        if referencia[i] != seq[i]:
            mutaciones += 1
            posiciones.append(i + 1)   # 1-based 位置
    print(f"{nombre}：{mutaciones} 个突变，位置 {posiciones}")

```

---

### 第 3 部分：文件读取和完整脚本

从这里开始，你将处理 `data/` 文件夹中的真实文件。每个练习创建一个新脚本。

> [!TIP]
> **读取文件时：** 确保使用正确的文件路径，路径是相对于脚本所在位置的。例如，如果脚本在 `scripts/` 中，文件在 `data/` 中，那么路径应为 `../data/archivo.ext`。

#### 步骤 7：读取 FASTA 文件

创建文件 `leer_fasta.py`：

```python
#!/usr/bin/env python3
"""
读取 FASTA 文件并显示每条序列的基本信息。
"""

archivo = "data/01_sequences.fasta" # 根据你的文件夹结构更改此路径
secuencias = {}
id_actual = None

with open(archivo, "r") as f:
    for linea in f:
        linea = linea.strip()
        if linea.startswith(">"):
            id_actual = linea[1:]       # 去掉 ">"
            secuencias[id_actual] = ""
        elif id_actual:
            secuencias[id_actual] += linea

# 输出报告
print(f"序列总数：{len(secuencias)}")
print("-" * 40)
for nombre, seq in secuencias.items():
    gc = (seq.count("G") + seq.count("C")) / len(seq) * 100
    print(f"{nombre}: {len(seq)} bp | GC%：{gc:.1f}%")

```

运行：
```bash
python3 leer_fasta.py
```

#### 步骤 8：读取 CSV 文件

创建文件 `leer_csv.py`：

```python
#!/usr/bin/env python3
"""
读取药敏试验文件并对结果进行分类。
"""

archivo = "data/08_antibiogram.csv" # 根据你的文件夹结构更改此路径
conteo = {"R": 0, "I": 0, "S": 0}

with open(archivo, "r") as f:
    encabezado = f.readline()   # 跳过第一行
    for linea in f:
        linea = linea.strip()
        if linea:
            partes = linea.split(",")
            resultado = partes[2]
            if resultado in conteo:
                conteo[resultado] += 1

print("=== 药敏试验摘要 ===")
print(f"耐药（R）：   {conteo['R']}")
print(f"中介（I）：   {conteo['I']}")
print(f"敏感（S）：     {conteo['S']}")
```

运行：
```bash
python3 leer_csv.py
```

#### 步骤 9：读取 TSV 文件

创建文件 `crecimiento_bacteriano.py`：

```python
#!/usr/bin/env python3
"""
计算每种细菌菌株的 OD600 平均值。
"""

archivo = "data/06_bacterial_growth.tsv" # 根据你的文件夹结构更改此路径

with open(archivo, "r") as f:
    encabezado = f.readline()
    for linea in f:
        linea = linea.strip()
        if linea:
            partes = linea.split("\t")
            cepa = partes[0]
            replicas = [float(partes[1]), float(partes[2]), float(partes[3])]
            promedio = sum(replicas) / len(replicas)
            print(f"{cepa}：OD600 平均值 = {promedio:.3f}")
```

运行：
```bash
python3 crecimiento_bacteriano.py
```

---

### 待办：练习 — 使用数据文件应用所学知识

这些练习必须通过为每个练习编写独立的 Python 脚本（`.py`）来解决。使用 `data/` 文件夹中的文件作为输入。将所有脚本保存在你将在模块 `02_coding_basics` 的 `exercises` 目录中创建的 `scripts/` 文件夹中。

```bash
mkdir -p scripts/
```

---

**练习 1 — 统计 FASTA 文件中的序列数**
文件：`data/01_sequences.fasta`
编写一个脚本，打开文件并统计其中包含多少条序列。

> [!TIP]
> 记住：每条序列以 `>` 开头。统计这些行即可。

---

**练习 2 — 统计 96 孔 PCR 板的结果**
文件：`data/02_results_pcr_96.csv`
读取文件并统计有多少孔发生了扩增以及有多少孔未扩增。

---

**练习 3 — 核苷酸计数**
文件：`data/03_nucleotides.fa`
读取序列并统计 A、T、C 和 G 的数量。打印每个计数。

---

**练习 4 — GC 百分比**
文件：`data/03_nucleotides.fa`
使用与上一练习相同的序列，计算并打印 **GC%**。

---

**练习 5 — 统计 GFF 文件中的基因数**
文件：`data/05_genome.gff3`
逐行读取文件并统计第三列（元素类型）中包含单词 `gene` 的行数。

> [!TIP]
> 使用 `.split()` 将行拆分为列，并检查 `columnas[2] == "gene"`。

---

**练习 6 — 计算细菌生长**
文件：`data/06_bacterial_growth.tsv`
对每种菌株，计算其三组重复数据的 **OD600 平均值**并打印。

---

**练习 7 — 突变检测**
文件：`data/07_mutations.fasta`
读取文件中的三条序列。以 `Ref` 序列作为参考，逐碱基比较每个突变体，统计有多少个位置不同。

---

**练习 8 — 分类药敏试验结果**
文件：`data/08_antibiogram.csv`
读取文件并统计有多少菌株是**耐药（R）**、**中介（I）**和**敏感（S）**的。

---

**练习 9 — 识别短序列**
文件：`data/09_short_sequences.fasta`
读取 FASTA 文件并统计有多少条序列长度**小于 15 个核苷酸**。

---

**练习 10 — 孵育温度平均值**
文件：`data/10_temperature.csv`
对每次实验，计算三次测量温度的**平均值**并打印。

---

**练习 11 — 按 Ct 值分类 PCR 样本**
文件：`data/11_pcr_ct.csv`
读取文件。如果 `Ct < 30` → **阳性**；如果 `Ct ≥ 30` → **阴性**。统计并打印阳性和阴性的总数。

---

**练习 12 — 已注释基因的数量**
文件：`data/12_gene_annotations.tsv`
读取文件并统计有多少个基因有实际的功能注释（即 `KO_ID` **不为** `NA`）。

---

**练习 13 — 葡萄糖消耗平均值**
文件：`data/13_glucose_comsumption.tsv`
对每个培养物，计算其三组重复数据的**平均消耗量**。最后，计算并打印所有培养物的**总平均值**。

---

**练习 14 — 细菌采样中的物种计数**
文件：`data/14_bacterias_muestreo.csv`
读取文件并打印每个物种出现了多少次。最后指出发现了多少种**不同的物种**。

> [!TIP]
> 使用字典，其中键是物种，值是计数。

---

**练习 15 — 按长度筛选蛋白质序列**
文件：`data/15_aa_sequences.fa`
读取氨基酸序列的 FASTA 文件，并统计有多少条序列的长度**超过 500 个氨基酸**。

---

### 📝 反思问题（练习后）

**关于变量和数据类型**

1. **类型错误：** 在步骤 2 中，你尝试将 `int` 与 `str` 相加并得到了 `TypeError`。在生物信息学中，当使用 Python 读取 CSV 文件时，**所有值默认都以 `str` 的形式到达**。这对练习 6、10 和 13（你需要计算平均值）有什么影响？你会使用什么函数在操作它们之前将这些值进行转换？
2. **变量名称：** 为什么你认为选择描述性名称如 `gc_porcentaje` 而不是 `x` 或 `resultado` 很重要？想象一下，一位同事必须在你没有任何文档的情况下，在 6 个月后阅读你的脚本。如果所有变量都叫 `a`、`b`、`c`，会发生什么？

**关于控制结构**

3. **步骤 6 中的 `continue`：** 在突变检测循环中，你使用 `continue` 跳过了参考序列。如果你没有包含它，会发生什么？会产生错误还是仅仅得到不正确的结果？
4. **使用字典进行计数：** 在练习 14 中，建议你使用字典来统计物种。为什么字典比列表更适合这项任务？一旦构建好字典，你将如何专门访问*大肠杆菌*（*Escherichia coli*）的计数？

**关于文件读取**

5. **标题行：** 在步骤 8 和 9 的脚本中，你使用 `f.readline()` 跳过了文件的第一行。如果你不跳过它会发生什么？会产生立即的错误还是悄无声息地产生错误的结果？在科学分析中，哪种情况更危险？
6. **可复现性 vs. 结果：** 你有两个选择来报告样本的 GC%：（a）用计算器手动计算该值并通过电子邮件发给你的导师，或（b）编写一个 Python 脚本直接读取文件来计算。如果明天序列文件更新了修正版本，哪种选择能让你立即无差错地获得更新后的结果？
