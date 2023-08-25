---
title: Pandas教程
date: 2023-08-21 11:00:04
tags:
- Excel处理
- Python
- 数据处理
categories: Python
---

# 介绍

pandas 是一个 Python 包，它提供快速、灵活且富有表现力的数据结构，旨在使“关系”或“标记”数据的处理变得简单直观。 它的目标是成为用 Python 进行实际、真实世界数据分析的基本高级构建块。 此外，它还有更广泛的目标，即成为任何语言中最强大、最灵活的开源数据分析/操作工具。 它已经在朝着这个目标前进。

pandas 非常适合下列这些类型的数据：

- 具有异构类型列的表格数据，如 SQL 表或 Excel 电子表格
- 有序和无序（不一定是固定频率）时间序列数据
- 带有行和列标签的任意矩阵数据（同质类型或异构）
- 任何其他形式的观察/统计数据集。数据根本不需要被标记就可以放入 pandas 数据结构中

# 主要特点

pandas 的两种主要数据结构[`Series`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.html#pandas.Series)（一维）和[`DataFrame`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame)（二维）处理金融、统计、社会科学和许多工程领域的绝大多数典型用例。对于 R 用户，[`DataFrame`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame)提供 R `data.frame`提供的一切以及更多。[pandas 构建在NumPy](https://numpy.org/)之上，旨在与许多其他第三方库在科学计算环境中良好集成。

以下是 `pandas` 擅长的一些事情：

- 轻松处理浮点和非浮点数据中的缺失数据（表示为 `NaN`、`NA`或`NaT`）
- 大小可变性：可以从 `DataFrame` 和更高维对象中插入和删除列
- 自动和精确的数据对齐：对象可以准确地与一组数据对齐，或者用户可以简单地忽略数据并让`Series`、`DataFrame`等在计算中自动为您对齐数据
- 强大、灵活的分组功能，可对数据集执行拆分-应用-组合操作，以聚合和转换数据
- 可以轻松地将其他 `Python` 和 `NumPy` 数据结构中的不规则、不同索引的数据转换为 `DataFrame` 对象
- 基于智能标签的切片、高级索引和大数据集的子集
- 直观的合并和连接数据集
- 灵活的重塑和翻转数据集
- 坐标轴的分层标签（每个刻度可能有多个标签）
- 强大的`IO`工具，用于从平面文件`(CSV and delimited`)、`Excel`文件、数据集以及从超快`HDF5`格式保存或加载的数据

# 安装

pandas 可以通过[PyPI](https://pypi.org/project/pandas)中的 pip 安装。

```bash
pip install pandas
```

# pandas 处理什么样的数据？

## 导包

```python
import pandas as pd
```

## pandas 数据表表示

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/table_dataframe.png" style="zoom: 50%;" />

示例：

```python
import pandas as pd
df = pd.DataFrame(
    {"Name": [
            "张三",
            "李四",
            "王五",
        ],
        "Age": [22, 24, 26],
        "Sex": ["男", "男", "女"],
    }
)
if __name__ == '__main__':
    print(df)
```

输出:

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/dataframe_print.png" style="zoom:50%;" />

在电子表格软件中，我们数据的表格表示看起来非常相似：

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/df_excel.png" style="zoom:50%;" />

## DataFrame中每一列都是一个 Series

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/01_table_series.png" style="zoom:50%;" />

如果只对`Name`这一列的数据感兴趣：

```python
pring(df["Age"])
```

输出:

```
0    22
1    24
2    26
Name: Age, dtype: int64
```

当选择`DataFrame`中的单列时，结果是 `Series`。要选择列，请使用方括号之间的列标签`[]`。

创建`Series`：

```py
ages = pd.Series([20, 30, 40], name="Age")
print(ages)
```

输出：

```
0    20
1    30
2    40
Name: Age, dtype: int64
```

## 使用 DataFrame 或 Series 做一些事情

我想知道谁的年龄最大？

`DataFrame`我们可以通过选择`Age`列并应用`max()`函数：

```python
ages = pd.Series([20, 30, 40], name="Age")
print(ages.max())
```

输出:

```
40
```

`DataFrame`和`Series`还有许多其他函数供我们使用，可以通过下面链接进行查看。

[Series](https://pandas.pydata.org/docs/reference/series.html)

[DataFrame](https://pandas.pydata.org/docs/reference/frame.html)

假如我们只对数据表的数值数据的一些基本统计感兴趣：

```python
print(df.describe())
```

Out:

```
        Age
count   3.0
mean   24.0
std     2.0
min    22.0
25%    23.0
50%    24.0
75%    25.0
max    26.0
```

`describe()` 方法可快速浏览 `DataFrame` 中的数字数据。由于`Name`和`Sex`列是文本数据，因此 `describe()` 方法默认不考虑这两列。

---------

# 如何读写表格数据？

数据集：

本教程使用**[成人人口普查收入](https://www.kaggle.com/datasets/uciml/adult-census-income?resource=download)**数据集，存储为 `csv 文件`。如下所示:

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/dataframe_dataset.png)

`Pandas`提供了<span style="color: red; font-weight: bold">read_csv()</span>函数将Excel文件读取为`DataFrame`对象。

`pandas` 支持许多不同的文件格式或开箱即用的数据源（`csv`、`excel`、`sql`、`json`、`parquet`...），读取每个文件格式或数据源都使用带有前缀`read_*`的函数。

```python
adult = pd.read_csv("data/adult.csv")
print(adult)
```

Out：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/read_excel.png)

请确保读完数据之后总是有一个数据检查。显示 `DataFrame` 时，默认显示前 5 行和后 5 行。

要查看 DataFrame 的前 N 行，请使用该 <span style="color: red; font-weight: bold">head()</span> 方法并以所需的行数作为参数。

我想查看 `pandas` `DataFrame` 的前 8 行。

```python
print(adult.head(8))
```

Out:

```
   age  workclass  fnlwgt  ... hours.per.week  native.country income
0   90          ?   77053  ...             40   United-States  <=50K
1   82    Private  132870  ...             18   United-States  <=50K
2   66          ?  186061  ...             40   United-States  <=50K
3   54    Private  140359  ...             40   United-States  <=50K
4   41    Private  264663  ...             40   United-States  <=50K
5   34    Private  216864  ...             45   United-States  <=50K
6   38    Private  150601  ...             40   United-States  <=50K
7   74  State-gov   88638  ...             20   United-States   >50K
```

对最后 N 行感兴趣吗？pandas也提供了一种  <span style="color: red; font-weight: bold">tail() </span>方法。例如，`adult.tail(10)`将返回 `DataFrame` 的最后 10 行。

可以通过请求 `pandas`  <span style="color: red; font-weight: bold">dtypes</span> 属性来检查 `pandas` 如何解释每个列数据类型：

```python
print(adult.dtypes)
```

Out:

```
age                int64
workclass         object
fnlwgt             int64
education         object
education.num      int64
marital.status    object
occupation        object
relationship      object
race              object
sex               object
capital.gain       int64
capital.loss       int64
hours.per.week     int64
native.country    object
income            object
dtype: object
```

对于每一列，都会列出所使用的数据类型。其中的数据类型`DataFrame`为整数 ( `int64`)和字符串 ( `object`)。

将`DataFrame`保存为Excel文件：

```
adult.to_excel("adult.xlsx", sheet_name="person", index=False)
```

<span style="color: red; font-weight: bold"> read_*</span>函数常用于读取函数到`Pandas`中，而`to_*`函数常用于存储数据。

该`to_excel()`方法将数据存储为 `Excel` 文件。在此示例中，`sheet_name`名为*“person”*而不是默认的*“Sheet1”*。通过设置 `index=False`行索引标签不会保存在电子表格中。

读取函数`read_excel()`会将数据重新加载到 `DataFrame`：

```python
adult = pd.read_excel("adult.xlsx")
print(adult)
```

Out:

```
       age workclass  fnlwgt  ... hours.per.week  native.country income
0       90         ?   77053  ...             40   United-States  <=50K
1       82   Private  132870  ...             18   United-States  <=50K
2       66         ?  186061  ...             40   United-States  <=50K
3       54   Private  140359  ...             40   United-States  <=50K
4       41   Private  264663  ...             40   United-States  <=50K
...    ...       ...     ...  ...            ...             ...    ...
32556   22   Private  310152  ...             40   United-States  <=50K
32557   27   Private  257302  ...             38   United-States  <=50K
32558   40   Private  154374  ...             40   United-States   >50K
32559   58   Private  151910  ...             40   United-States  <=50K
32560   22   Private  201490  ...             20   United-States  <=50K
```

我对 `DataFrame` 的技术摘要感兴趣

```python
print(adult.info())
```

Out:

```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 32561 entries, 0 to 32560
Data columns (total 15 columns):
 #   Column          Non-Null Count  Dtype 
---  ------          --------------  ----- 
 0   age             32561 non-null  int64 
 1   workclass       32561 non-null  object
 2   fnlwgt          32561 non-null  int64 
 3   education       32561 non-null  object
 4   education.num   32561 non-null  int64 
 5   marital.status  32561 non-null  object
 6   occupation      32561 non-null  object
 7   relationship    32561 non-null  object
 8   race            32561 non-null  object
 9   sex             32561 non-null  object
 10  capital.gain    32561 non-null  int64 
 11  capital.loss    32561 non-null  int64 
 12  hours.per.week  32561 non-null  int64 
 13  native.country  32561 non-null  object
 14  income          32561 non-null  object
dtypes: int64(6), object(9)
memory usage: 3.7+ MB
```

`info()`方法提供了有关 `DataFrame` 的技术信息 ，因此让我们对输出作出更详细地解释：

- 它确实是一个[`DataFrame`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame)
- 有 32561 个`entries`，即 32561 行
- 每行都有一个行标签（也称为`index`），其值范围为 0 到 32560
- 该表有 15列。大多数列的每一行都有一个值（所有 32561 个值都是`non-null`）
- `workclass` `education` `marital.status` `occupation` `relationship`  `race` `sex` `native.country` `income`列都是由文本数据组成（String 又称 Object）
- 其他列是数值数据，其中一些是整数（又名`integer`）
- 不同列中的数据类型（字符、整数……）通过`dtypes`列出
- 还提供了用于保存 DataFrame 的大致 RAM 

--------

# 选择DataFrame的子集

## 如何选择DataFrame的特定列？

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/03_subset_columns.png" style="zoom:50%;" />

假如我们只对`Age`感兴趣:

```python
adult = pd.read_excel("adult.xlsx")
adult_subset = adult.head()
print(adult_subset["age"])
```

Out:

```
0    90
1    82
2    66
3    54
4    41
Name: age, dtype: int64
```

`DataFrame` 中的每一列都是一个`Series`。当选择单个列时，返回的对象是 `pandas Series`。我们可以通过检查输出的类型来验证这一点：

```python
print(type(adult["age"]))
```

Out:

```
<class 'pandas.core.series.Series'>
```

看看`shape`输出：

```python
print(adult["age"].shape)
```

Out:

```
(5,)
```

`DataFrame.shape`是 `Series`、`DataFram` 的一个属性。`DataFrame`包含行数和列数：*(nrows, ncolumns)*。pandas `Series` 是一维的，仅返回行数。

假如对年龄和性别感兴趣：

```python
age_sex = adult[["age", "sex"]]
print(age_sex)
```

Out:

```
   age     sex
0   90  Female
1   82  Female
2   66  Female
3   54  Female
4   41  Female
```

> 要选择多个列，请在选择括号内使用列名称列表`[]`。

返回的数据类型是 `pandas DataFrame`：

```python
print(type(age_sex))
```

Out:

```
<class 'pandas.core.frame.DataFrame'>
```

In:

```python
print(age_sex.shape)
```

Out:

```
(5, 2)
```

返回的`DataFrame`具有5 行 2 列。

-----

## 如何选择DataFrame的特定行？

假如对 60岁以上的客户感兴趣。

```python
print(adult[adult["age"] > 60])
```

Out:

```
   age workclass  fnlwgt  ... hours.per.week  native.country income
0   90         ?   77053  ...             40   United-States  <=50K
1   82   Private  132870  ...             18   United-States  <=50K
2   66         ?  186061  ...             40   United-States  <=50K
```

括号内的条件 `adult["age"] > 60` 检查哪些行 `Age` 列的值大于 60：

```python
print(adult["age"] > 60)
```

Out:

```
0     True
1     True
2     True
3    False
4    False
Name: age, dtype: bool
```

条件表达式（也可以是 `==`, `!=`, `<`, <=,… ）的输出实际上是一组`pandas Series`的布尔值（`True`或`False`），

其行数与原始 `DataFrame` 相同 。这样的一系列布尔值可用于将其放在选择括号 [] 之间来过滤 DataFrame， 仅选择值为 `True` 的行。

假如我们只对`native.country`是`China`和`India`的用户感兴趣。

```python
country = adult[adult["native.country"].isin(["China", "India"])]
print(country.head())
```

Out:

```
     age         workclass  fnlwgt  ... hours.per.week  native.country income
63    51  Self-emp-not-inc  160724  ...             40           China   >50K
90    39           Private  198654  ...             67           India   >50K
214   42      Self-emp-inc   23510  ...             60           India   >50K
244   64           Private  149044  ...             60           China  <=50K
377   30           Private  315640  ...             40           China   >50K
```

与条件表达式类似，`isin()` 条件函数为匹配条件列表中的每一行返回 `True`。要根据此类函数过滤行，请使用括号内的条件函数，括号内的条件函数检查`native.country`是`China`或`India`的行。

与下列等价:

```python
country = adult[(adult["native.country"] == "China") | (adult["native.country"] == "India")]
print(country.head())
```

Out:

```
     age         workclass  fnlwgt  ... hours.per.week  native.country income
63    51  Self-emp-not-inc  160724  ...             40           China   >50K
90    39           Private  198654  ...             67           India   >50K
214   42      Self-emp-inc   23510  ...             60           India   >50K
244   64           Private  149044  ...             60           China  <=50K
377   30           Private  315640  ...             40           China   >50K
```

> 组合多个条件语句时，每个条件必须用括号 <span style="color:red;font-family:bold">()</span> 括起来。 而且，不能使用<span style="color:red;font-family:bold"> or </span>或<span style="color:red;font-family:bold"> and </span>，而需要使用或运算符<span style="color:red;font-family:bold">  | </span>以及与运算符<span style="color:red;font-family:bold">  & </span>。

假如我们只对年龄已知的用户感兴趣。

```
adult = pd.read_excel("adult.xlsx")
age_not_no = adult[adult["age"].notna()]
print(age_not_no.head())
```

Out:

```
   age workclass  fnlwgt  ... hours.per.week  native.country income
0   90         ?   77053  ...             40   United-States  <=50K
1   82   Private  132870  ...             18   United-States  <=50K
2   66         ?  186061  ...             40   United-States  <=50K
3   54   Private  140359  ...             40   United-States  <=50K
4   41   Private  264663  ...             40   United-States  <=50K

[5 rows x 15 columns]
```

<span style="color:red; font-weight: bold">notna() </span>条件函数对于值不是 `Null` 值的每一行返回 `True`。 因此，可以结合选择括号`[]`来过滤数据表。

## 如何选择DataFrame的特定行和列？

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/03_subset_columns_rows.png)

假如对35 岁以上人员的性别感兴趣。

```python
adult_names = adult.loc[adult["age"] > 35, "sex"]
print(adult_names.head())
```

> 在这种情况下，行和列的子集是一次性生成的，仅使用括号选择<span style="color:red;font-weight:bold"> [] </span>已经不够了。 选择括号<span style="color:red;font-weight:bold"> [] </span>前面需要<span style="color:red;font-weight:bold"> loc/iloc </span>运算符。 使用<span style="color:red;font-weight:bold"> loc/iloc </span>时，逗号之前的部分是您想要选择的行，逗号之后的部分是您想要选择的列。

使用列名、行标签或条件表达式时，请在选择括号 [] 前面使用 loc 运算符。 对于逗号之前和之后的部分，您可以使用单个标签、标签列表、标签切片、条件表达式或冒号。 使用冒号指定您要选择所有行或列。

假如对第 10 行到第 25 行和第 3 到第 5 列感兴趣。

```
print(adult.iloc[9:25, 2:5])
```

Out:

```
    fnlwgt     education  education.num
9    70037  Some-college             10
10  172274     Doctorate             16
11  164526   Prof-school             15
12  129177     Bachelors             13
13  136204       Masters             14
14  172175     Doctorate             16
15   45363   Prof-school             15
16  172822          11th              7
17  317847       Masters             14
18  119592    Assoc-acdm             12
19  203034     Bachelors             13
20  188774     Bachelors             13
21   77009          11th              7
22   29059       HS-grad              9
23  153870  Some-college             10
24  135285       HS-grad              9
```

同样，行和列的子集是一次性生成的，仅使用选择括号<span style="color:red;font-weight:bold"> [] </span>已经不够了。 当根据表中的位置对某些行和/或列特别感兴趣时，请在选择括号<span style="color:red;font-weight:bold"> [] </span>前面使用<span style="color:red;font-weight:bold"> iloc </span>运算符。

当使用 loc 或 iloc 选择特定行和/或列时，我们对我们所选的特定行和/或列赋值。 

例如，要将前三行人员的年龄统一改成20：

```python
adult.iloc[0:3, 0] = 20
print(adult.head(5))
```

Out:

```
   age workclass  fnlwgt  ... hours.per.week  native.country income
0   20         ?   77053  ...             40   United-States  <=50K
1   20   Private  132870  ...             18   United-States  <=50K
2   20         ?  186061  ...             40   United-States  <=50K
3   54   Private  140359  ...             40   United-States  <=50K
4   41   Private  264663  ...             40   United-States  <=50K
```

> <span style="color:red;font-weight:bold">Remember</span>:
>
> - <span style="color:green;font-weight:bold">要选择数据子集时，使用方括号[]。</span>
> - <span style="color:green;font-weight:bold">在括号内，您可以使用单个列/行标签、列/行标签列表、标签切片、条件表达式或冒号。</span>
> - <span style="color:green;font-weight:bold">通过 loc 使用行名和列名选择特定的行和列。</span>
> - <span style="color:green;font-weight:bold">通过 iloc 使用坐标轴位置选择特定的行和列。</span>
> - <span style="color:green;font-weight:bold">你可以为 loc 或 iloc 选择的行或列赋新值。</span>

--------------

# 如何在 pandas 中创建绘图？

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/04_plot_overview.png)

导入相关类库：

本教程使用的数据：[空气质量数据](https://github.com/pandas-dev/pandas/blob/main/doc/data/air_quality_no2.csv)

在本教程中，使用了有关的空气质量数据，这些数据由 `OpenAQ` 提供并使用 py-openaq 包。 `air_quality_no2.csv` 数据集提供了分别位于巴黎、安特卫普和伦敦的测量站 FR04014、BETR801 和伦敦威斯敏斯特的值。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/air_quilty_data.png" style="zoom:50%;" />

读取数据：

```python
air_quality = pd.read_csv("data/air_quality_no2.csv", index_col=0, parse_dates=True)
print(air_quality.head())
```

Out:

```
                     station_antwerp  station_paris  station_london
datetime                                                           
2019-05-07 02:00:00              NaN            NaN            23.0
2019-05-07 03:00:00             50.5           25.0            19.0
2019-05-07 04:00:00             45.0           27.7            19.0
2019-05-07 05:00:00              NaN           50.4            16.0
2019-05-07 06:00:00              NaN           61.9             NaN
```

`read_csv` 函数的 `index_col`参数表示将第一（第 0）列定义为结果 `DataFrame` 的索引。

`read_csv` 函数的 `parse_dates`参数表示将列中的日期转换为 `Timestamp` 对象。

通过视图快速的检查数据：

```python
air_quality = pd.read_csv("data/air_quality_no2.csv", index_col=0, parse_dates=True)
air_quality.plot()
plt.show()
```

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/matplot_air.png)

`pandas` 默认为`DataFrame`包含数字的每一列数创建一个线图。

我只想用巴黎的数据列绘制图形。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/station_pairs_draw.png" style="zoom:50%;" />

我想直观地比较NO2在伦敦和巴黎测量的值。

```python
air_quality.plot.scatter(x="station_london", y="station_paris", alpha=0.5)
plt.show()
```

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/matlab_scatter_plot.png" style="zoom:50%;" />

除了使用绘图功能时的默认线图之外，还可以使用许多其他图形来绘制数据。

绘图方法允许使用除默认线图之外的多种绘图样式，可以通过`plot()`方法的`kind`参数进行设置:

- <span style="color:red;font-weight:bold">"bar" </span>或<span style="color:red;font-weight:bold"> "barh" </span>绘制柱状图
- <span style="color:red;font-weight:bold">"hist" </span>绘制直方图
- <span style="color:red;font-weight:bold"> "box" </span>绘制箱形图
- <span style="color:red;font-weight:bold"> "kde" </span>或<span style="color:red;font-weight:bold"> "density" </span>绘制密度图
- <span style="color:red;font-weight:bold"> "area" </span>绘制面积图
- <span style="color:red;font-weight:bold"> "scatter" </span>绘制散点图
- <span style="color:red;font-weight:bold"> "hexbin" </span>绘制六边形箱图
-  <span style="color:red;font-weight:bold"> "pie" </span>绘制饼状图

例如，可以通过以下方式创建箱线图：

```python
air_quality.plot.box()
plt.show()
```

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/air_quality_plot_box.png" style="zoom:50%;" />

我希望每一列都在一个单独的子图中。

```python
axs = air_quality.plot.area(figsize=(12, 4), subplots=True)
plt.show()
```

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/air_quality_subplots.png" style="zoom:50%;" />

我想进一步定制、扩展或保存结果图。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/plt_subplots.png" style="zoom:50%;" />

`pandas` 创建的每个绘图对象都是 [Matplotlib](https://matplotlib.org/)对象。由于 `Matplotlib` 提供了大量的选项来自定义绘图，因此使 `pandas` 和 `Matplotlib` 之间的关联显式化，从而可以将 Matplotlib 的所有功能发挥到绘图上。

---------

# 如何从现有列创建新列

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/05_newcolumn_1.png" style="zoom:50%;" />

教程数据如下：

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/market_sales_excel.png" style="zoom:50%;" />

假设我们想在表格中新增一列表示所有商场第一季度的收入：

```python
market_sales["first_season_sales"] = market_sales.iloc[:, 0:4].sum(axis=1)
print(market_sales)
```

Out:

```
        sales_01  sales_02  sales_03  sales_04  first_season_sales
market                                                            
沃尔玛        10000     30000     80000     60000              180000
家乐福        20000     20000     20000     20000               80000
好又多        30000     50000     10000     10000              100000
大润发        40000     60000     50000     30000              180000
华润万家       50000     40000     60000     20000              170000
永辉         60000     10000     20000     10000              100000
```

其他数学运算符（+、-、*、/、…）或逻辑运算符（<、>、==、…）也按此逻辑工作。

如果您需要更高级的逻辑，您可以通过[`apply()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.apply.html#pandas.DataFrame.apply).

如果你想对某些列名进行重命名操作：

```python
renamed_market_sales = market_sales.rename(
        columns={
          
            "sales_01": "SALES_FIRST_MONTH",
            "sales_02": "SALES_SECOND_MONTH",
            "sales_03": "SALES_THIRD_MONTH",
            "sales_04": "SALES_FOURTH_MONTH"
        }
    )
print(renamed_market_sales)
```

Out:

```
        SALES_FIRST_MONTH  ...  SALES_FOURTH_MONTH
market                     ...                    
沃尔玛                 10000  ...               60000
家乐福                 20000  ...               20000
好又多                 30000  ...               10000
大润发                 40000  ...               30000
华润万家                50000  ...               20000
永辉                  60000  ...               10000
```

> `rename()` 函数可用于行和列。 你需要提供一个字典，其中包含当前名称的键和新名称的值，以更新相应的名称。

映射不应仅限于固定名称，也可以是映射函数。 例如，也可以使用函数将列名转换为小写字母：

```python
renamed_market_sales = renamed_market_sales.rename(columns=str.lower)
print(renamed_market_sales)
```

Out:

```
        sales_first_month  ...  sales_fourth_month
market                     ...                    
沃尔玛                 10000  ...               60000
家乐福                 20000  ...               20000
好又多                 30000  ...               10000
大润发                 40000  ...               30000
华润万家                50000  ...               20000
永辉                  60000  ...               10000
```

----

# 如何计算汇总统计数据

数据集：本教程使用**[成人人口普查收入](https://www.kaggle.com/datasets/uciml/adult-census-income?resource=download)**数据集。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/06_aggregate.png" style="zoom:50%;" />

## 汇总统计数据

所有人员的平均年龄是多少？

```python
adult = pd.read_excel("data/adult.xlsx")
print(adult["age"].mean())
```

Out:

```
38.58164675532078
```

可以使用不同的统计数据，并将其应用于具有数值数据的列。 一般情况下，操作会排除缺失数据并默认跨行(按列)操作。

我们可以使用`median`函数来计算一系列数值的中位数。这个函数可以用于计算 `Series` 或 `DataFrame` 对象中的中位数。：

```python
adult = pd.read_excel("data/adult.xlsx")
print(adult[["age", "hours.per.week"]].median())
```

Out:

```
age               37.0
hours.per.week    40.0
dtype: float64
```

可以使用 `DataFrame.agg()` 方法定义给定列的聚合统计信息的特定组合，而不是预定义的统计信息：

```python
              age  hours.per.week
min     17.000000        1.000000
max     90.000000       99.000000
median  37.000000       40.000000
skew     0.558743        0.227643
```

## 聚合统计数据并按类别分组

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/06_groupby.png" style="zoom:50%;" />

统计所有男性和女性的平均年龄是多少？

```python
print(adult[["age", "sex"]].groupby("sex").mean())
```

Out:

```
              age
sex              
Female  36.858230
Male    39.433547
```

由于我们感兴趣的是每个性别的平均年龄，因此首先对这两列进行子选择：`titanic[["Sex", "Age"]]`。 接下来，对 "Sex" 列应用 `groupby()` 方法，为每个类别创建一个组。 计算并返回每个性别组的平均年龄。

如果我们只对每个性别的平均年龄感兴趣，则分组数据也支持列的选择（像往常一样的矩形括号 []）：

```python
print(adult.groupby("sex")["age"].mean())
```

Out:

```
sex
Female    36.858230
Male      39.433547
Name: age, dtype: float64
```

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/06_groupby_select_detail.png" style="zoom:50%;" />

## 按类别统计记录数

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/06_valuecounts.png" style="zoom:50%;" />

统计每个性别的人数是多少：

```python
print(adult["sex"].value_counts())
```

Out:

```
sex
Female    10771
Male      21790
Name: count, dtype: int64
```

该函数是一个快捷方式，因为它实际上是一个 groupby 操作，并结合了每组内记录数的计数：

```python
print(adult.groupby("sex")["sex"].count())
```

> `size`和两者都`count`可以与 结合使用 `groupby`。而`size`包含`NaN`值并仅提供行数（表的大小），`count`排除缺失值。在该`value_counts`方法中，使用`dropna`参数来包含或排除`NaN`值。

--------

# 如何重塑表格布局

让我们使用[空气质量数据集](https://github.com/pandas-dev/pandas/blob/main/doc/data/air_quality_long.csv)的一小部分。我们专注于 `NO2`数据并仅使用每个位置的前两个测量值。数据子集将被称为`no2_subset`。

```python
air_quality = pd.read_csv(
	"data/air_quality_long.csv", index_col="date.utc", parse_dates=True
)
print(air_quality.head())
```

Out:

```python
                                city country location parameter  value   unit
date.utc                                                                     
2019-06-18 06:00:00+00:00  Antwerpen      BE  BETR801      pm25   18.0  µg/m³
2019-06-17 08:00:00+00:00  Antwerpen      BE  BETR801      pm25    6.5  µg/m³
2019-06-17 07:00:00+00:00  Antwerpen      BE  BETR801      pm25   18.5  µg/m³
2019-06-17 06:00:00+00:00  Antwerpen      BE  BETR801      pm25   16.0  µg/m³
2019-06-17 05:00:00+00:00  Antwerpen      BE  BETR801      pm25    7.5  µg/m³
```

空气质量数据集包含以下列：

- city：使用传感器的城市，巴黎、安特卫普或伦敦
- 国家：使用传感器的国家，FR、BE 或 GB
- location：传感器的 ID，*FR04014*、*BETR801*或 *伦敦威斯敏斯特*
- 参数：传感器测量的参数，或者NO2 或颗粒物
- value：测量值
- 单位：测量参数的单位，在本例中为“μg/m³”

`DataFrame`的索引是`datetime`测量的日期时间。

## 对表行进行排序

根据年龄对表格数据进行排序

```python
adult = pd.read_excel("adult.xlsx")
print(adult.sort_values("age").head())
```

Out:

```
       age workclass  fnlwgt  ... hours.per.week  native.country income
4159    17   Private  130125  ...             20   United-States  <=50K
18810   17   Private  187308  ...             15   United-States  <=50K
25709   17   Private  153021  ...             20   United-States  <=50K
6046    17         ?  103810  ...             40   United-States  <=50K
8682    17         ?  127003  ...             40   United-States  <=50K

[5 rows x 15 columns]
```

根据年龄和受教育等级对所有人员进行排序。

```python
adult_sorted = adult.sort_values(by=["age", "education.num"], ascending=False).head()
print(adult_sorted)
```

```
       age  workclass  fnlwgt  ... hours.per.week  native.country income
1742    90    Private   87372  ...             72   United-States   >50K
1739    90  Local-gov  227796  ...             60   United-States   >50K
8914    90    Private   51744  ...             50   United-States   >50K
20621   90    Private  115306  ...             40   United-States  <=50K
22166   90    Private  206667  ...             40   United-States   >50K

[5 rows x 15 columns]
```

使用 `DataFrame.sort_values()`，表中的行根据定义的列进行排序。 索引将遵循行顺序。

## 从长到宽的表格格式

```python
air_quality = pd.read_csv(
	"data/air_quality_long.csv", index_col="date.utc", parse_dates=True
)
# filter for no2 data only
no2 = air_quality[air_quality["parameter"] == "no2"]
# use 2 measurements (head) for each location (groupby)
no2_subset = no2.sort_index().groupby(["location"]).head(2)
print(no2_subset)
```

Out:

```
                                city country  ... value   unit
date.utc                                      ...             
2019-04-09 01:00:00+00:00  Antwerpen      BE  ...  22.5  µg/m³
2019-04-09 01:00:00+00:00      Paris      FR  ...  24.4  µg/m³
2019-04-09 02:00:00+00:00     London      GB  ...  67.0  µg/m³
2019-04-09 02:00:00+00:00  Antwerpen      BE  ...  53.5  µg/m³
2019-04-09 02:00:00+00:00      Paris      FR  ...  27.4  µg/m³

[5 rows x 6 columns]
```

我希望三个站点的值作为彼此相邻的单独列。（即将某些行转换成列）

```python
print(no2_subset.pivot(columns="location", values="value"))
```

Out:

```
location                   BETR801  FR04014  London Westminster
date.utc                                                       
2019-04-09 01:00:00+00:00     22.5     24.4                 NaN
2019-04-09 02:00:00+00:00     53.5     27.4                67.0
2019-04-09 03:00:00+00:00      NaN      NaN                67.0
```

### 数据透视表

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/07_pivot_table.png" style="zoom:50%;" />

我想要以表格形式获取每个站`NO2`和`PM2.5`的平均浓度。

```python
air_quality_mean = air_quality.pivot_table(
	values="value", index="location", columns="parameter", aggfunc="mean"
)
print(air_quality_mean)
```

Out:

```
parameter                 no2       pm25
location                                
BETR801             26.950920  23.169492
FR04014             29.374284        NaN
London Westminster  29.740050  13.443568
```

数据透视表是电子表格软件中众所周知的概念。当对每个变量的行/列边距（小计）感兴趣时，请将参数`margins`设置为`True`：

```py
		air_quality_mean = air_quality.pivot_table(
        values="value",
        index="location",
        columns="parameter",
        aggfunc="mean",
        margins=True
    )
    print(air_quality_mean)
```

Out:

```
parameter                 no2       pm25        All
location                                           
BETR801             26.950920  23.169492  24.982353
FR04014             29.374284        NaN  29.374284
London Westminster  29.740050  13.443568  21.491708
All                 29.430316  14.386849  24.222743
```

## 从宽到长的表格格式

我们向`DataFrame` 加一个新索引[`reset_index()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.reset_index.html#pandas.DataFrame.reset_index)。

```python
no2_pivoted = no2.pivot(columns="location", values="value").reset_index()
print(no2_pivoted.head())
```

Out:

```
location                  date.utc  BETR801  FR04014  London Westminster
0        2019-04-09 01:00:00+00:00     22.5     24.4                 NaN
1        2019-04-09 02:00:00+00:00     53.5     27.4                67.0
2        2019-04-09 03:00:00+00:00     54.5     34.2                67.0
3        2019-04-09 04:00:00+00:00     34.5     48.5                41.0
4        2019-04-09 05:00:00+00:00     46.5     59.5                41.0
```

我想在单列（长格式）中收集所有空气质量二氧化氮测量值。

```python
no_2 = no2_pivoted.melt(id_vars="date.utc")
print(no_2)
```

Out:

```
                      date.utc            location  value
0    2019-04-09 01:00:00+00:00             BETR801   22.5
1    2019-04-09 02:00:00+00:00             BETR801   53.5
2    2019-04-09 03:00:00+00:00             BETR801   54.5
3    2019-04-09 04:00:00+00:00             BETR801   34.5
4    2019-04-09 05:00:00+00:00             BETR801   46.5
...                        ...                 ...    ...
5110 2019-06-20 20:00:00+00:00  London Westminster    NaN
5111 2019-06-20 21:00:00+00:00  London Westminster    NaN
5112 2019-06-20 22:00:00+00:00  London Westminster    NaN
5113 2019-06-20 23:00:00+00:00  London Westminster    NaN
5114 2019-06-21 00:00:00+00:00  London Westminster    NaN

[5115 rows x 3 columns]
```

> DataFrame 上的 pandas.melt() 方法将数据表从宽格式转换为长格式。 列标题成为新创建的列中的变量名称。
>
> 换句话说，也就是将多列合并成一列。

`pandas.melt()`可以更详细地定义传递给的参数：

```python
no_2 = no2_pivoted.melt(
    id_vars="date.utc",
    value_vars=["BETR801", "FR04014", "London Westminster"],
    value_name="NO_2",
    var_name="id_location",
)
print(no_2)
```

上述参数的解释：

- `value_vars`定义将哪些列*融合*在一起
- `value_name`为列值提供自定义列名，而不是默认列名`value`
- `var_name`为融合的列提供自定义列名称。否则它采用索引名称或默认值`variable`
- `id_vars`中声明的列将不会被融合
- `id_vars`声明的列不会被融合

因此，参数 `value_name` 和 `var_name` 只是两个生成列的用户定义名称。 要融合的列由 `id_vars` 和 `value_vars` 定义。

----

# 如何合并多个表的数据

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/08_concat_row.png" style="zoom:50%;" />

现有两个班的学生数据 `class_01_students.xlsx` `class_02_students.xlsx` 如下:

 `class_01_students.xlsx`：

```python
class_01_students = pd.read_excel("data/class_01_students.xlsx")
print(class_01_students)
```

Out:

```
   name   age  sex   height blood
0   张三   20   男     175     O
1   李四   21   女     160     A
2   王五   50   男     170     B
3   赵六   30   男     173    AB
4   田七   40   男     180     A
```

`class_02_students.xlsx`:

```python
class_02_students = pd.read_excel("data/class_02_students.xlsx")
print(class_02_students)
```

Out:

```
   name   age sex  height blood
0   小明   18   男     181     O
1   小黄   24   男     177     A
2   小红   17   女     170     B
3   小李   32   男     157    AB
4   小花   29   女     168     A
```

将两个具有相似结构的表合并成一个表：

```python
class_01_students = pd.read_excel("data/class_01_students.xlsx")
class_02_students = pd.read_excel("data/class_02_students.xlsx")
students = pd.concat([class_01_students, class_02_students], axis=0)
print(students)
```

Out:

```
    name  age sex  height blood
0   张三   20   男     175     O
1   李四   21   女     160     A
2   王五   50   男     170     B
3   赵六   30   男     173    AB
4   田七   40   男     180     A
0   小明   18   男     181     O
1   小黄   24   男     177     A
2   小红   17   女     170     B
3   小李   32   男     157    AB
4   小花   29   女     168     A
```

> concat() 函数沿其中一个轴（按行或按列）执行多个表的串联操作。

默认情况下，串联是沿着轴 0 进行的，因此生成的表组合了输入表的行。 让我们检查原始表和串联表的形状来验证操作：

```python
print("shape of class_01_students:", class_01_students.shape)
# shape of class_01_students: (5, 5)
print("shape of class_02_students:", class_02_students.shape)
# shape of class_02_students: (5, 5)
print("shape of students:", students.shape)
# shape of students: (10, 5)
```

> axis 参数出现在许多可沿轴应用的 pandas 方法中。 DataFrame 有两个相应的轴：第一个轴垂直向下跨行（轴 0），第二个轴水平跨列（轴 1）。 默认情况下，大多数操作（如串联或汇总统计）是跨行（轴 0）的，但也可以跨列应用。

根据年龄对表进行排序（正序）：

```python
sorted_students = students.sort_values("age")
print(sorted_students)
```

Out:

```
    name  age sex  height blood
2   小红   17   女     170     B
0   小明   18   男     181     O
0   张三   20   男     175     O
1   李四   21   女     160     A
1   小黄   24   男     177     A
4   小花   29   女     168     A
3   赵六   30   男     173    AB
3   小李   32   男     157    AB
4   田七   40   男     180     A
2   王五   50   男     170     B
```

倒序：

```python
sorted_students = students.sort_values("age", ascending=False)
print(sorted_students)
```

Out:

```
    name  age sex  height blood
2   王五   50   男     170     B
4   田七   40   男     180     A
3   小李   32   男     157    AB
3   赵六   30   男     173    AB
4   小花   29   女     168     A
1   小黄   24   男     177     A
1   李四   21   女     160     A
0   张三   20   男     175     O
0   小明   18   男     181     O
2   小红   17   女     170     B
```

> 这些教程中没有提到同时存在多个行/列索引。 分层索引或 MultiIndex 是一种先进且强大的 pandas 功能，用于分析更高维度的数据。
>
> 多重索引超出了 pandas 介绍的范围。 目前，请记住函数reset_index可用于将任何级别的索引转换为列。

## 使用通用标识符连接表

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/08_merge_left.png" style="zoom:50%;" />

现有学生基本信息表 `class_01_students.xlsx` 和学生成绩表 `student_score.xlsx` 如下:

基本信息表 `class_01_students.xlsx` :

```python
class_01_students = pd.read_excel("data/class_01_students.xlsx")
print(class_01_students)
```

Out:

```
  	name  age sex  height blood  student_id
0   张三   20   男     175     O           1
1   李四   21   女     160     A           2
2   王五   50   男     170     B           3
3   赵六   30   男     173    AB           4
4   田七   40   男     180     A           5
```

学生成绩表 `student_score.xlsx` :

```python
student_score = pd.read_excel("data/student_score.xlsx")
print(student_score)
```

Out:

```
   student_id  chinese  math  english  physics
0           1       89    76       88       90
1           2       76    67       50       89
2           3       57    82       91       88
3           4       23    43       54       76
```

根据`student_id`合并两个表格：

```python
class_01_students = pd.read_excel("data/class_01_students.xlsx")
student_score = pd.read_excel("data/student_score.xlsx")
merged_table = pd.merge(class_01_students, student_score, how="left", on="student_id")
print(merged_table)
```

Out:

```
    name  age sex  height blood  student_id  chinese  math  english  physics
0   张三   20   男     175     O           1     89.0  76.0     88.0     90.0
1   李四   21   女     160     A           2     76.0  67.0     50.0     89.0
2   王五   50   男     170     B           3     57.0  82.0     91.0     88.0
3   赵六   30   男     173    AB           4     23.0  43.0     54.0     76.0
4   田七   40   男     180     A           5      NaN   NaN      NaN      NaN
```

`mege`操作类似于数据库的`join`操作

现学生成绩表 `student_score.xlsx` 和学生基本信息表 `class_01_students.xlsx` 没有相同的key。

学生基本信息表 `class_01_students.xlsx` 的数据如下：

```
    name  age sex  student_id
0   张三   20   男           1
1   李四   21   女           2
2   王五   50   男           3
3   赵六   30   男           4
4   田七   40   男           5
```

学生成绩表 `student_score.xlsx` 的数据如下：

```
   stuid  chinese  math  english  physics
0      1       89    76       88       90
1      2       76    67       50       89
2      3       57    82       91       88
3      4       23    43       54       76
```

连接两个表：

```python
 class_01_students = pd.read_excel("data/class_01_students.xlsx")
 student_score = pd.read_excel("data/student_score.xlsx")
 merged_table = pd.merge(class_01_students, student_score, how="left", left_on="student_id",      right_on="stuid")
 print(merged_table)
```

与前面的示例相比，没有公共列名。然而，表  `class_01_students.xlsx` 中的`student_id`列和表`student_score.xlsx` 中的`stuid`字段以通用格式提供了学生Id信息，此处使用参数`left_on` 和参数`right_on` （而不仅仅是`on`）来建立两个表之间的链接。

------------

# 如何轻松处理时间序列数据

本教程使用的数据：[空气质量数据](https://github.com/pandas-dev/pandas/blob/main/doc/data/air_quality_no2_long.csv)

## 使用 pandas 日期时间属性

将纯文本转换成日期时间对象：

```python
air_quality = pd.read_csv("data/air_quality_no2_long.csv")
air_quality["date.utc"] = pd.to_datetime(air_quality["date.utc"])
print(air_quality["date.utc"].head())
```

最初，日期时间中的值是字符串，不提供任何日期时间操作（例如提取年份、星期几等）。 通过应用 `to_datetime` 函数，`pandas` 解释字符串并将其转换为日期时间（即 `datetime64[ns, UTC]`）对象。 在 `pandas` 中，我们将这些类似于标准库中的 `datetime.datetime` 的日期时间对象称为 `pandas.Timestamp`。

由于许多数据集的其中一列中确实包含日期时间信息，因此 `pandas.read_csv()` 和 `pandas.read_json()` 等 `pandas` 输入函数可以在使用 `parse_dates` 参数在读取数据时将一些列日期列转换成时间戳对象。

为什么这些[`pandas.Timestamp`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Timestamp.html#pandas.Timestamp)对象有用？让我们通过一些示例来说明附加值。

我们正在使用的时间序列数据集的开始日期和结束日期是哪天？

```python
print(air_quality["date.utc"].min())
print(air_quality["date.utc"].max())
```

Out:

```
2019-05-07 01:00:00+00:00
2019-06-21 00:00:00+00:00
```

使用 `pandas.Timestamp` 作为日期时间使我们能够使用日期信息进行计算并使它们具有可比性。 因此，我们可以用它来获取时间序列的长度：

```python
print(air_quality["date.utc"].max() - air_quality["date.utc"].min())
```

Out:

```
44 days 23:00:00
```

结果是一个 `pandas.Timedelta` 对象，类似于标准 `Python` 库中的 `datetime.timedelta` 并定义持续时间。

我想向 DataFrame 添加一个新列，仅包含月份

```python
air_quality["month"] = air_quality["date.utc"].dt.month
print(air_quality.head())
```

Out:

```
    city country                  date.utc  ... value   unit  month
0  Paris      FR 2019-06-21 00:00:00+00:00  ...  20.0  µg/m³      6
1  Paris      FR 2019-06-20 23:00:00+00:00  ...  21.8  µg/m³      6
2  Paris      FR 2019-06-20 22:00:00+00:00  ...  26.5  µg/m³      6
3  Paris      FR 2019-06-20 21:00:00+00:00  ...  24.9  µg/m³      6
4  Paris      FR 2019-06-20 20:00:00+00:00  ...  21.4  µg/m³      6

[5 rows x 8 columns]
```

通过使用 `Timestamp` 对象来表示日期，`pandas` 提供了许多与时间相关的属性。 例如月份`month`，还有年份`year`、季度`quarter`……所有这些属性都可以通过<span style="color:red;font-weight:bold"> dt </span>访问器访问。

每个测量位置一周中每一天的平均二氧化氮浓度是多少？

```python
print(air_quality.groupby(
        [air_quality["date.utc"].dt.weekday, "location"])["value"].mean())
```

Out:

```
date.utc  location          
0         BETR801               27.875000
          FR04014               24.856250
          London Westminster    23.969697
1         BETR801               22.214286
          FR04014               30.999359
          London Westminster    24.885714
2         BETR801               21.125000
          FR04014               29.165753
          London Westminster    23.460432
3         BETR801               27.500000
          FR04014               28.600690
          London Westminster    24.780142
4         BETR801               28.400000
          FR04014               31.617986
          London Westminster    26.446809
5         BETR801               33.500000
          FR04014               25.266154
          London Westminster    24.977612
6         BETR801               21.896552
          FR04014               23.274306
          London Westminster    24.859155
Name: value, dtype: float64
```

还记得统计计算教程中 `groupby` 提供的分割-应用-组合模式吗？在这里，我们想要计算给定的统计数据（例如每周每个测量地的二氧化碳的平均浓度)。为了按工作日进行分组，我们使用 `pandas Timestamp` 的 `datetime` 属性 `weekday`（星期一 = 0 和星期日 = 6），也可以通过 `dt` 访问器访问。 可以对地点和工作日进行分组，以拆分每个组合的平均值计算。

将所有站点的时间序列中一天中典型的 NO2 模式绘制在一起。 换句话说，一天中每个小时的平均值是多少？

```python
fig, axs = plt.subplots(figsize=(12, 4))
air_quality.groupby(air_quality["date.utc"].dt.hour)["value"].mean().plot(
    kind='bar', rot=0, ax=axs
)
plt.xlabel("Hour of the day")  # custom x label using Matplotlib
plt.ylabel("$NO_2 (µg/m^3)$")
plt.show()
```

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/hour_of_the_day.png" style="zoom:50%;" />

## 日期时间作为索引

在重塑教程中， [`pivot()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.pivot.html#pandas.pivot)介绍了如何重塑数据表，将每个测量位置作为单独的列：

```python
no_2 = air_quality.pivot(index="date.utc", columns="location", values="value")
print(no_2.head())
```

Out:

```
location                   BETR801  FR04014  London Westminster
date.utc                                                       
2019-05-07 01:00:00+00:00     50.5     25.0                23.0
2019-05-07 02:00:00+00:00     45.0     27.7                19.0
2019-05-07 03:00:00+00:00      NaN     50.4                19.0
2019-05-07 04:00:00+00:00      NaN     61.9                16.0
2019-05-07 05:00:00+00:00      NaN     72.4                 NaN
```

> 通过旋转数据，日期时间信息成为表的索引。一般来说，将某列设置为索引可以通过该`set_index`函数来实现。

使用日期时间索引（即`DatetimeIndex`）提供了强大的功能。例如，我们不需要`dt`访问器来获取时间序列属性，而是直接在索引上使用这些属性：

```python
print(no_2.index.year)
print(no_2.index.month)
```

Out:

```
Index([2019, 2019, 2019, 2019, 2019, 2019, 2019, 2019, 2019, 2019,
       ...
       2019, 2019, 2019, 2019, 2019, 2019, 2019, 2019, 2019, 2019],
      dtype='int32', name='date.utc', length=1033)
Index([5, 5, 5, 5, 5, 5, 5, 5, 5, 5,
       ...
       6, 6, 6, 6, 6, 6, 6, 6, 6, 6],
      dtype='int32', name='date.utc', length=1033)
```

绘制5 月 20 日至 5 月 21 月底不同站点的 NO2 值。

```python
no_2 = air_quality.pivot(index="date.utc", columns="location", values="value")
no_2["2019-05-20":"2019-05-21"].plot()
plt.show()
```

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/pandas/09_time_section.png" style="zoom:50%;" />

------

# 如何操作文本数据

本教程数据`class_01_students.xlsx`：

```python
class_01_students = pd.read_excel("data/class_01_students.xlsx")
print(class_01_students)
```

Out:

```
        name  age  sex  student_id
0  San Zhang   20   男           1
1      Si Li   21   女           2
2    Wu Wang   50   男           3
3   Liu Zhao   30   男           4
4    Qi Tian   40   男           5
```

将`name`设为小写。

```python
class_01_students = pd.read_excel("data/class_01_students.xlsx")
print(class_01_students["name"].str.lower())
```

Out:

```
0    san zhang
1        si li
2      wu wang
3     liu zhao
4      qi tian
Name: name, dtype: object
```

与时间序列教程中具有 `dt` 访问器的日期时间对象类似，使用 `str` 访问器时可以使用许多专用字符串方法。 这些方法通常具有与单个元素的等效内置字符串方法匹配的名称，但按元素应用到列的每个值。

通过提取逗号之前的部分来创建一个新列“姓氏”，其中包含乘客的姓氏。

```python
class_01_students["surname"] = class_01_students["name"].str.split(" ").str.get(1);

print(class_01_students)
```

Out:

```
        name  age sex  student_id surname
0  San Zhang   20   男           1   Zhang
1      Si Li   21   女           2      Li
2    Wu Wang   50   男           3    Wang
3   Liu Zhao   30   男           4    Zhao
4    Qi Tian   40   男           5    Tian
```

使用该[`Series.str.split()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.str.split.html#pandas.Series.str.split)方法，每个值都作为 2 个元素的列表返回。第一个元素是空格之前的部分，第二个元素是空格之后的部分。

假如我们只对姓名中带有"Tian"字的用户感兴趣：

```python
print(class_01_students["name"].str.contains("Tian"))
```

Out:

```
0    False
1    False
2    False
3    False
4     True
Name: name, dtype: bool
```

方法`Series.str.contains()`检查列中的每个值（`Name`如果字符串包含单词`Tian`，并返回每个值`True`（`Tian`是`name`的一部分）或 `False`（`Tian`不是`name`的一部分）。

> 支持更强大的字符串提取，因为 [`Series.str.contains()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.str.contains.html#pandas.Series.str.contains)和[`Series.str.extract()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.str.extract.html#pandas.Series.str.extract)方法接受[正则表达式](https://docs.python.org/3/library/re.html)，但超出了本教程的范围。

哪位用户的名字最长？

```python
print(class_01_students["name"].str.len().idxmax())
```

Out:

```
0
```

为了获得最长的名称，我们首先必须获得列中每个名称的长度`Name`。通过使用 `pandas` 字符串方法，该 [`Series.str.len()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.str.len.html#pandas.Series.str.len)函数将单独应用于每个名称（按元素）。

接下来，我们需要获取表中名称长度最大的对应位置，最好是索引标签。 `idxmax()` 方法正是这样做的。 它不是字符串方法，适用于整数，因此不使用 `str`。

根据行 ( `0`) 和列 ( `name`) 的索引名称，我们可以使用运算符进行选择`loc`:

```python
print(class_01_students.loc[class_01_students["name"].str.len().idxmax(), "name"])
```

Out:

```
San Zhang
```

在“sex”列中，将“男”的值替换为“M”，将“女”的值替换为“F”。

```python
class_01_students["sex"] = class_01_students["sex"].replace({"男":"M", "女":"F"})
print(class_01_students)
```

Out:

```
        name  age sex  student_id
0  San Zhang   20   M           1
1      Si Li   21   F           2
2    Wu Wang   50   M           3
3   Liu Zhao   30   M           4
4    Qi Tian   40   M           5
```

虽然[`replace()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.replace.html#pandas.Series.replace)它不是字符串方法，但它提供了一种使用映射或词汇表来转换某些值的便捷方法。它需要`dictionary`来定义映射。`{from : to}`

------------

关于Pandas的更多教程请访问：https://pandas.pydata.org/pandas-docs/stable/getting_started/tutorials.html。
