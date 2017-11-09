## **dplyr** 包

**dplyr** 是用于数据操纵的最流行R包之一，使用它并结合 R 的基础函数，已可完成大部分的统计描述工作。

### **dplyr** 中的函数类型

**dplyr** 的基础函数只有5个，其名称和功能如下：

- `mutate()`：新增变量
- `select()`：选择变量
- `filter()`：筛选观测
- `arrange()`：数据排序
- `summarise()`：描述统计

这些函数的功能基本可以“望文生义”。这也是 **tidyverse** 系列包的命名风格：“人”（此处意指函数的功能）如其名。

这5个基础函数，加上用于分组的函数`group_by()`以及管道函数`%>%`，构成了 **dplyr** 的最常用函数。此外，**dplyr** 还提供用于合并数据框、随机选取观测、计算变量频次与取值序位等功能的若干函数。


以下均通过实例说明各函数的用法。请确保已通过以下命令加载 **dplyr** 包。

```{r}
library(dplyr)
```

当然，也可通过`library(tidyverse)`调用此包。

### 新增变量：`mutate()`

出于分析的需要，研究者通常需要基于已有变量创建新变量，并保存至原数据框中，然后再进行后续分析。此时即可使用`mutate()`函数。

`mutate()`函数的基本用法为：

```r
mutate(dataframe,
       newvar1 = expression1,
       newvar2 = epxression2,
       ...
       )
```

其中，

- `dataframe` 表示待操纵的数据框
- `newvar1` 表示想生成的第1个新变量
- `newvar2` 表示想生成的第2个新变量
- `expression1` 表示用以生成`newvar1`的命令
- `expression2` 表示用以生成`newvar2`的命令
- 生成更多变量可依次类推



R 自带数据`women`给出了美国30~39周岁之间女性在不同平均身高下对应的平均体重，但其单位分别为英寸（in）和磅（lb）。将其转换为国际单位制会更容易为其他国家的分析者理解，转换公式如下：

- 1 in = 2.54 cm
- 1 lb = 0.45 kg

首先观察原始数据。

```{r}
women
```

现拟生成新变量 `height_cm`和 `weight_kg`，即以 cm 和 kg 为单位的身高和体重。命令如下：

```{r}
mutate(women, 
       height_cm = height * 2.54, 
       weight_kg = weight * 0.45
       )
```

从实用角度而言，对已用 cm 和 kg 衡量的平均身高和平均体重之类，无需保留小数。为此可再对上述命令进行优化：

```{r}
mutate(women, 
       height_cm = round(height * 2.54), 
       weight_kg = round(weight * 0.45)
       )
```

BMI 指数（身体质量指数，英文为 Body Mass Index，简称 BMI），是用体重（单位：kg）除以身高（单位：m）的平方得出的数字，即：

$$
BMI = \frac{体重（kg）}{{身高}^2(m^2)}    
$$

BMI 指数是目前国际上常用的衡量人体胖瘦程度的一个参考标准。下面生成各平均身高和平均体重下对应的 BMI 指数，变量命名为`bmi`，并保留至小数点后1位。


```{r}
mutate(women, 
       height_cm = round(height * 2.54), 
       weight_kg = round(weight * 0.45),
       bmi = round((weight_kg / (height_cm / 100) ^ 2), 1)
       )
```

如果只想保留新生成的变量、而不保留原有的所有变量，可使用`transmute()`函数。试比较：

```{r}
transmute(women,
          height_cm = round(height * 2.54),
          weight_kg = round(weight * 0.45),
          bmi = round((weight_kg / (height_cm / 100) ^ 2), 1)
          )
```

以上命令并未保存数据对象，如想保存，可采用如下方式：

```{r}
women_bmi <- mutate(
  women,
  height_cm = round(height * 2.54),
  weight_kg = round(weight * 0.45),
  bmi = round((weight_kg / (height_cm / 100) ^ 2), 1)
)
```

BMI       | 肥胖程度
----------|---------
 < 18.5   | 偏瘦（Underweight）
18.5~24.9 | 正常（Normal）
25.0~29.9 | 偏胖（Overweight）
`>=` 30.0 | 肥胖（Obesity）


依据此表，对上前述 BMI 值进行分类，该变量命名为`bmi_class`。

此时可利用 **dlpyr** 包中的`case_when()`函数，此函数用于生成多重`if else`条件下的赋值，即将某一变量按特定取值条件划分为不同类型（因子），具体用法如下

```r
mutate(dataframe,
       type = case_when(
       condition_A ~ "A",
       condition_B ~ "B",
       condition_C ~ "C",
       ...
         )
       )
```

其中

- `dataframe` 表示待操纵的数据框
- `type` 表示新生成的变量名
- `condition_A`、`condition_B`、`condition_C`等表示不同的取值条件，这些条件应当返回为逻辑型向量
- `A`、`B`、`C`等表示对`type`变量的赋值
- `...`表示其他条件

具体效果看下面的代码。

```{r}
women_bmi01 <- mutate(
  women_bmi,
  bmi_class = case_when(
    bmi < 18.5 ~ "Underweight",
    between(bmi, 18.5, 22.9) ~ "Normal",
    between(bmi, 23.0, 29.9) ~ "Overweight",
    bmi >= 30 ~ "Obesity"
    )
  )
```

注意这里利用了 **dplyr** 包中的`between()`函数，`between(x, a, b)`的含义即在于判定向量`x`中的元素取值是否在闭区间[a,b]内，`a`和`b`表示两个实数。

也可使用R的自有函数`within()`来修改数据框。

```{r}
women_bmi02 <- within(women_bmi,
                    {
                    bmi_class = NA
                    bmi_class[bmi < 18.5] = "Underweight"
                    bmi_class[bmi >= 18.5 & bmi <= 24.9] = "Normal"
                    bmi_class[bmi >= 25 & bmi <= 29.9] =  "Overweight"
                    bmi_class[bmi >= 30] =  "Obesity"
                    })
head(women_bmi02)
```

显然所有`class`的取值均为正常（normal）。基于均值计算的BMI指数通常并无实际意义，只有对独立的个体进行体重是否超标的判定才有实际价值。此例只作为对变量的操纵示例。

结合R自有函数`cut()`，仍可在`mutate()`框架下完成同样的工作。

```{r}
women_bmi03 <- mutate(
  women_bmi,
  bmi_class = cut(bmi,
  breaks = c(0, 18.5, 25, 30, 100),
  labels = c("Underweight", "Normal", "Overweight", "Obesity"),
  right = FALSE)
  )
head(women_bmi03)
```

两种方式的效果完全相同。使用`cut()`的便利之处在于可以将这一命令嵌套于整体代码中，使代码变得更为紧凑可读。下面将前述命令整合成一个代码块，一步到位实现数据处理要求。

```{r}
women_bmi <- mutate(
  women,
  height_cm = round(height * 2.54),
  weight_kg = round(weight * 0.45),
  bmi = round((weight_kg / (height_cm / 100) ^ 2), 1),
  bmi_class = cut(
    bmi,
    breaks = c(0, 18.5, 25, 30, 100),
    labels = c("Underweight", "Normal", "Overweight", "Obesity"),
    right = FALSE
    )
  )
women_bmi
```

`cut()`函数可将某一连续型变量转换为因子（即转化为分类变量，也即完成分组），其用法如下：
```r
cut(
  var,
  breaks = ,
  labels = ,
  include.lowest = FALSE,
  right = TRUE,
  ...
  )
```

其中，

- `var` 表示待分类的变量
- `breaks` 用于设定分界点
- `labels` 用于设定分组标签
- `include.lowest` 用于设定最小值是否包含在分组中，默认为否
- `right` 用于设定分组的右侧端点值是否包含在本组之内。

特定要注意的是`breaks =`的设定与`right =`的设定匹配。例如，若要分4组，则需要有5个分界点。若设定：

```r
breaks = c(1, 4, 20, 60, 100)
```
且选择默认设定`right  = TRUE`，则分组如下：

- 第1组：(1, 4]，1不在分组之内，因为默认`include.lowest = FALSE`
- 第2组：(4, 20]
- 第3组：(20, 60]
- 第4组：(60, 100]


若选择设定`right  = FALSE`，则分组如下：

- 第1组：(1, 4)
- 第2组：[4, 20)
- 第3组：[20, 60)
- 第4组：[60, 100)

分析者可按实际需求设定分组是否包含右侧端点值。最小值和最大值的设定可视实际变量的取值范围而自行设定，通常可小于待分组变量的实际最小值或大于其实际最大值，以使所有取值都包含在某一分组之中。


### 选择变量：`select()`

当一个数据框包含太多并不一定在一次分析中使用到的变量、即存在太多无关的列时，选择部分变量（列）另存为某一数据对象，对这一新对象进行数据操纵是更为方便的。这尤其适用于对大型社会经济调查的问卷分析中。研究者通常只需要选取其中的若干个变量进行分析，而无需使用其中的全部变量。此时即可使用`select()`函数。

`select()`函数的用法如下：

```r
select(dataframe, var1, var2, ...)
```

其中，

- `dataframe` 表示待选取变量的数据框
- `var1`, `var2` 表示原数据框中的变量名

以 R 自带的数据`mtcars`为例进行说明。

```{r}
names(mtcars)
mtcars_subset1 <- select(mtcars, wt, mpg)
head(mtcars_subset1, 2)
```

此时，`mtcars_subset1`数据就只剩下`mpg`和`wt`两个变量。

与许多其他统计软件类似，`select()`也允许通过减号`-`来剔除变量。

```{r}
mtcars_subset2 <- select(mtcars, -c(wt, mpg))
head(mtcars_subset2, 2)
```

此时返回除`mpg`和`wt`这两列的所有其他变量。

使用`var1: var2`的形式能够选择指定两个变量之间的所有变量（包括这两个变量本身）。试比较以下两个命令的结果：

```{r}
names(select(mtcars, am: hp))
names(select(mtcars, -c(am: hp)))
```


`select()`还可用于变量的重命名，其方式为：

```r
select(data, new_var_name1 = old_var_name1, new_var_name2 = old_var_name2, ...)
```

例如，

```{r}
mtcars_subset3 <- select(mtcars, x = wt, y = mpg)
head(mtcars_subset3)
```

这对处理许多社会调查问卷的数据是有极大便利的，因为这些数据在录入时通常只被编码为`a101`、`a102`等脱离实际意义的代码，而通过对它们进行重命名，可使分析过程更具可读性。 


`select()`函数自动剔除未选中的变量，欲保留其他未选中变量而只对选中变量进行重命名，可使用`rename()`函数。

```{r}
mtcars_renamed <- rename(mtcars, x = mpg)
head(mtcars_renamed, 2)
```


`select()`的功能不仅如此。在此函数中通过适当的参数设置，可更高效地选择变量，相关参数如下：

- `starts_with(" ")`：选取以特定字符串开头的变量
- `ends_with(" ")`：选取以特定字符串结尾的变量
- `contains(" ")`：选取包含特定字符串的变量
- `matches(" ")`：选取匹配给定正则表达式的变量
- `num_range("x", 1:3)`：选取形如`x1`, `x2`, `x3`之类的、指定数字范围的变量
- `one_of("a", "b", "c")`：选取括号中的所有变量(`a`, `b`, `c`全选中)
- `everything()`：选取所有变量

下面以`cgss2013.dta`数据为例，选择部分参数进行示例，其余参数设置效果可类推得知。

```{r}
library(haven)
cgss2013 <- read_dta("cgss2013.dta")
length(names(cgss2013)) 
```

上述代码中的第三行并未展示结果，而是使用`length()`函数计算了该数据中的变量个数，共计722个变量。显然无须将所有变量纳入分析流程。

例如，若只想选择编码为b1至b6的这6个变量，可用如下设置：

```{r}
cgss2013_b <- select(cgss2013, num_range("b", 1:6))
head(cgss2013_b, 2)
```

如想选取所有以`c2a`开头的变量，可使用如下设置：

```{r}
cgss2013_c2a <- select(cgss2013, starts_with("c2a"))
head(cgss2013_c2a, 2)
```

实际上，在 CGSS2013 问卷中，c2a 代表着一个小规模的态度量表，询问了被调查者关于医疗卫生公共服务10个方面的满意程度。`cgss2013_c2a`数据已提取此量表的所有回答。请自行思考如下命令有何功能：

```r
select(cgss2013, -starts_with("c2a"))
```

`select()`还可用于对变量位置的改动。在一个包含多变量的原始数据中，关键变量可能在很靠后的位置。若要将关键变量的位置前置，可使用通过变量选择的顺序来实现。

```{r}
mtcars_subset4 <- select(mtcars, wt, mpg, everything())
head(mtcars_subset4)
```

此时`wt`和`mpg`已变换为数据框的头两个变量，其余变量依原次序依次呈现。



### 筛选观测：`filter()`

数据分析可能只需使用部分样本的信息。例如，研究者可能需要排除女性样本仅对男性样本做分析，或只选择低收入群体做分析。此时都需要筛选出性别或收入为特定取值的样本。`select()`用于选择符合条件的变量（列），`filter()`则用于选择符合条件的观测（行），其实质是通过设定条件筛选观测。

`filter()`函数的用法如下：

```r
filter(dataframe, condition1, condition2, ...)
```

其中，

- `dataframe` 表示待筛选的数据框
- `condition1` 表示第1个筛选条件
- `condition2` 表示第2个筛选条件
- 不同筛选条件之间可以用 & 、| 等逻辑运算符连接，其中逗号的作用相当于 & 

观察如下命令的结果：

```{r}
x <- filter(mtcars, am == 1, cyl == 4)
y <- filter(mtcars, am == 1 & cyl == 4)
all_equal(x, y)
```

`all_equal(x, y)`的作用在于判断两个数据框是否完全相同，其结果为真，即说明两者完全等同。上述命令实际上都筛选出`mtcars`数据中所有4汽缸的手动档汽车。

`all_equal(x, y)`函数来自于 **dplyr** 包，它相当于R的自带函数`all.equal(x, y)`。

请自行观察以下命令的结果：

```{r}
x <- filter(mtcars, am == 1 & cyl == 4)
y <- filter(mtcars, am == 1 | cyl == 4)
all_equal(x, y)
```

### 变量排序：`arrange()`

排序是最常见的数据操纵方式之一。**dplyr** 中可使用 `arrange()`实现这一功能，该函数及相关函数比 R 自带的`sort()`、`order()`、`rank()`等涉及排序的函数更为直观且不易出错。

`arrange()`函数的用法如下：

```r
arrange(dataframe, var1, var2, ...)
```

其中，

- `dataframe` 表示待排序的数据框
- `var1` 表示作为首选排序标准的变量
- `var2` 表示作为次选排序标准的变量
- 默认升序（从小大到）排序，除非用`desc(var1)`的方式降序（从大到小）排序

下面仍以`mtcars`数据进行示例。

```{r}
mtcars <- select(mtcars, am, mpg, everything())
head(mtcars)
mtcars_arranged <- arrange(mtcars, am, mpg)
head(mtcars_arranged)
```

排序后的数据框中，先按`am`的取值从0到1排序，其次按`mpg`的大小从小到大排序。

试比较：

```{r}
mtcars_arranged <- arrange(mtcars, desc(am), desc(mpg))
head(mtcars_arranged)
```

### 描述统计：`summarise()`

作为数据分析结果的第一步，往往是呈现一系列的描述统计量。使用`summarise()`函数能够实现多数单一统计量的描述。

`summarise()`函数的用法如下：

```r
summarise(dataframe, 
          statistics1 = FUN1,
          statistics2 = FUN2,
          ...
          )
```

其中，

- `dataframe` 表示待描述的数据框
- `statistics1` 表示拟生成的描述统计量的名称
- `FUN1` 表示拟生成var1的统计函数
- 不同统计量可在逗号后依次写出，但只能返回单一统计量，即不能在一个变量名中给出多个统计量值，如**不能**写出如下形式：`quartiles = fivenum(var)`，因为`fivenum()`一次性给出5个统计量的值。

```{r}
result <- summarise(
  mtcars, 
  mpg_mean = mean(mpg),
  mpg_sd = sd(mpg),
  mpg_median = median(mpg)
)
result
class(result)
```

注意`result`的类型实为一个数据框。可见`summarise()`函数实际上将描述统计的结果储存为数据框，这可为后续的进一步处理提供方便。

多数 R 自带的描述统计函数都能与`summarise()`组合使用，如`mean()`、`sd()`、`max()`、`min()`、`median()`、`quantile()`、`range()`、`sum()`等。同时还可与 **dplyr** 包中的相应函数相配合使用：

- `n()`：计数，相当于length()
- `nth()`：给出指定顺序位置上的数值
- `first()`：给出向量中的首位元素
- `last()`：给出向量中的末位元素
- `n_distinct()`：给出向量中不同取值的个数
 
单独的`summarise()`函数本身而言并不比 R 自带的描述统计方式更为便捷，只有在与分组函数`group_by()`组合在一起时，才能显示其便利性。为更好地展示分组描述统计结果，需要了解一个重要的函数：`%>%`。



### 管道操作：`%>%`

所谓管道操作（pipe operation），是将管道操作符（pipe operator）左边的对象传递给右边的函数并成为后者的作用对象。**dplyr** 包引入了其他包中的管道操作符`%>%`，通常也称为管道函数。

前面曾有这样的操作：

```r
mtcars <- select(mtcars, am, mpg, everything())
head(mtcars)
mtcars_arranged <- arrange(mtcars, am, mpg)
head(mtcars_arranged)
```

其逻辑是先选择、后排序，在实际分析中，后续还可能进行分组统计。如此一来，就会出现一些像`mtcars_arranged`这样的中间对象。如果中间对象多，而分析者又无意对之进行保存、但出于运算需要又不得不对之进行命名，就会使分析过程变得繁杂，代码也会显得拖沓。此时，引入管道函数就显得十分必要。试看以下代码：

```{r}
mtcars %>%
  select(am, mpg) %>%
  arrange(am, mpg) %>%
  group_by(am) %>%
  summarise(
    n = n(),
    mpg_mean = mean(mpg),
    mpg_sd = sd(mpg),
    mpg_median = median(mpg)
  ) %>%
  round(2)
```

这一串命令以流水作业的方式完成以下内容：

1. 选取`mtcars`数据中的`am`和`mpg`变量；
2. 按先`am`后`mpg`的方式升序排序；
3. 按`am`对数据进行分组；
4. 分组（即分手动档和自动档车型）分别统计车辆频次、`mpg`的均值、标准差和中位值；
5. 将描述统计结果保留至小数点两位。

如果不使用管道操作，就需要使用命令嵌套的方式编写代码，此时代码就会变得层次复杂、难以理解。

有时上一步命令的结果并不一定能作为下一步函数的第一个参数，此时可使用点号`.`来表示所传递的数据对象。


```{r}
library(ggplot2)
mtcars %>% qplot(wt, mpg, color = am, data = .)
```

上述命令使用了 **ggplot2** 包中的`qplot()`函数，使用不同颜色绘制手动档和自动档车的车重（`wt`）与油耗（`mpg`）之间关系的散点图。此函数的细节暂可略过，此处请仅注意`data = .`的用法。


### 随机取样：`sample_n()`与`sample_frac()`

分析者有时需要从数据中随机抽取若干个观测（行），或随机抽取特定百分比的观测。这可分别通过`sample_n()`与`sample_frac()`实现，其中`frac`是英文 fraction 的缩写，意为“分数”（与整数相对应），即部分的意思。


`sample_n()`与`sample_frac()`的用法如下：

```r
sample_n(dataframe, size = , replace = FALSE)
sample_frac(dataframe, size = , replace = FALSE)
```

其中，

- `dataframe` 表示待抽取的数据框
- `sample_n()` 中的`size`取正整数，表示待抽取的观测数（行数）
- `sample_frac()` 中的`size`通常取[0, 1]之间的小数，表示比例
- 默认`replace = FALSE`表示无放回抽样，若设置`replace = TRUE`表示有放回抽样

试观察以下结果：

```{r}
sample_n(mtcars, 5)
sample_frac(mtcars, 0.2)
mtcars %>% 
  group_by(am) %>% 
  sample_n(5)
```

由于未设定随机数种子数，每次执行上述命令的结果自然有所不同。





```r
# install.packages("memisc")
library(memisc)
x <- as.data.set(spss.system.file("filename.sav))
description(x)[1:10]
codebook(x)[1:10]
```



```{r}
library(readxl)
library(dplyr)
rs2015 <- read_excel("rs2015.xlsx") %>%
  mutate(
    total = round(mid * 0.4 + final * 0.6),
    psy = ifelse(major == "PSY", 1, 0),
    rank = cut(
      total,
      breaks = c(0, 59, 69, 79, 89, 100),
      labels = LETTERS[5:1],
      right = TRUE
    )
  ) %>%
  arrange(psy, desc(total)) 
```

```{r}
samples_by_psy <- group_by(rs2015, psy) %>%
  sample_n(5)
psystudents <- filter(rs2015, psy == 1)
result_by_group <- group_by(rs2015, psy) %>%
  summarise(
    n = n(),
    mean = mean(total),
    sd = sd(total),
    Q1 = quantile(total, 0.25),
    median = median(total),
    Q3 = quantile(total, 0.75),
    IQR = IQR(total),
    min = min(total),
    max = max(total)
  ) %>%
  round(2)
result_by_group
```

### 数据合并：`join()`

分析者有时需要同时操纵两个以上的数据框。

例如，对来自不同区域的同一问卷的调查数据进行合并。

以下内容待补充。




## 二表动词

数据分析中通常有很多表格，我们需要灵活的工具将表格合并。在**dplyr**中，有三个动词可以同时操作两个表格。

-  变化连接（Mutating joins）：通过匹配表格中的行，向表格中增加新的变量。
-  筛选连接（Filtering joins）：基于表格中的观测是否与其他表格的观测相匹配来筛选观测值。
-  集合运算（Set operations）：将属于集合元素的观测组合到数据集中。

（使用这些功能的前提假设是你拥有`tidy data`，即行为观测列为变量的整洁数据。）

所有二表动词运行起来都很相似。若头两个参数为 x 和 y ，然后合并表格，那么最后产生的新表格总是和 x 是相同类型的。


### 变化连接（Mutating joins）

变化连接可以将多个表格中的变量组合到一起。

```{r}
library(dplyr)
T0 <- c(11.4, 9.6, 10.1, 8.5, 10.3, 10.6, 11.8, 9.8, 10.9,
10.3)
C0 <- c(9.1, 8.7, 9.7, 10.8, 10.9, 10.6, 10.1, 12.3, 8.8,
10.4, 10.9, 10.4)
T9 <- c(15.3, 14.0, 11.4, 14.1, 15.0, 11.8, 12.3, 14.1,
17.6, 14.3)
C9 <- c(9.3, 8.8, 8.8, 10.1, 9.6, 8.6, 10.4, 12.4, 9.3,
9.5, 8.4, 8.7)
id.T <- c(1:length(T0)) # 生成序列号, 从1到 T0 的长度值
id.C <- c(1:length(C0)) # 生成序列号, 从1到 C0 的长度值
treated <- data.frame(id = id.T, T0, T9) # 将 T0, T9 两个同长度向量合并为一个数据框
control <- data.frame(id = id.C, C0, C9) # 将 C0, C9 两个同长度向量合并为一个数据框
left_join(treated, control, by="id")
```

#### 控制表格是如何匹配的

和 x、y 一样，每个变化连接都要通过参数`by`来控制使用哪些变量来匹配两个表格中的观测值。

- `NULL`，默认值。在这种情况下，**dplyr**会使用两个表格中的所有变量进行匹配，即自然连接。

```{r}
T0 <- c(11.4, 9.6, 10.1, 8.5, 10.3, 10.6, 11.8, 9.8, 10.9,
10.3)
C0 <- c(9.1, 8.7, 9.7, 10.8, 10.9, 10.6, 10.1, 12.3, 8.8,
10.4, 10.9, 10.4)
T9 <- c(15.3, 14.0, 11.4, 14.1, 15.0, 11.8, 12.3, 14.1,
17.6, 14.3)
C9 <- c(9.3, 8.8, 8.8, 10.1, 9.6, 8.6, 10.4, 12.4, 9.3,
9.5, 8.4, 8.7)
id.T <- c(1:length(T0)) # 生成序列号, 从1 到T0 的长度值
id.C <- c(1:length(C0)) # 生成序列号, 从1 到C0 的长度值
treated <- data.frame(id = id.T, T0, T9) # 将T0, T9 两个同长度向量合并为一个数据框
control <- data.frame(id = id.C, C0, C9) # 将C0, C9 两个同长度向量合并为一个数据框
left_join(treated, control)
```

- 一个字符向量，`by = x`。这种情况和自然连接很像，但是只使用部分共有变量。

```{r}
T0 <- c(11.4, 9.6, 10.1, 8.5, 10.3, 10.6, 11.8, 9.8, 10.9,
10.3)
C0 <- c(9.1, 8.7, 9.7, 10.8, 10.9, 10.6, 10.1, 12.3, 8.8,
10.4, 10.9, 10.4)
T9 <- c(15.3, 14.0, 11.4, 14.1, 15.0, 11.8, 12.3, 14.1,
17.6, 14.3)
C9 <- c(9.3, 8.8, 8.8, 10.1, 9.6, 8.6, 10.4, 12.4, 9.3,
9.5, 8.4, 8.7)
id.T <- c(1:length(T0)) # 生成序列号, 从1 到T0 的长度值
id.C <- c(1:length(C0)) # 生成序列号, 从1 到C0 的长度值
treated <- data.frame(id = id.T, T0, T9) # 将T0, T9 两个同长度向量合并为一个数据框
control <- data.frame(id = id.C, C0, C9) # 将C0, C9 两个同长度向量合并为一个数据框
left_join(treated, control, by="id")
```

- 一个被命名的字符向量，`by = c("x" = "a")`。这会将表`x`中的变量`x`与表`b`中的变量`a`进行匹配。用到的变量也会在输出中被使用。

```{r}
id_psy <- 1:6
id_soc <- 1:5
score_psy <- c(88, 89, 87, 92, 94, 85)
score_soc <- c(85, 87, 82, 95, 93)
gender_psy <- c("male", "male", "female", "male", "female", "female")
gender_soc <- c("male", "female", "female", "female", "male")
class_psy <- data.frame(id = id_psy, score_psy, gender_psy)
class_soc <- data.frame(id = id_soc, score_soc, gender_soc)
left_join(class_psy, class_soc, c("score_psy" = "score_soc"))
```

#### 连接类型

有四种变化连接的类型，它们的区别在于没有找到匹配的变量时会执行的操作。我们将以下面的例子来阐述：

```{r}
exm1 <- data_frame(x = 1:4, y = 3:6 )
exm2 <- data_frame(x = 2:5, a = 5, b = "c")
```

- `inner_join(x, y)`只包括`x`和`y`中都匹配的观测。

```{r}
exm1 %>% inner_join(exm2) %>% knitr::kable()
```

- `left_join(x, y)`包括`x`中的所有观测，不管它们匹配与否。这是最常用的连接类型，因为它保证了不会从原始表格中丢失观测。

```{r}
exm1 %>% left_join(exm2)
```

- `right_join(x, y)`包括`y`中的所有观测。它等同于`left_join(y, x)`，只是列的排列不同。

```{r}
exm1 %>% right_join(exm2)
exm2 %>% left_join(exm1)
```

- `full_join()`包括`x`和`y`中的所有观测。

```{r}
exm1 %>% full_join(exm2)
```

左、右、全连接被称为**外连接**。当一行不匹配外连接时，新变量会被用来填充缺失值。

#### 观测值

尽管变化连接主要被用来增加新的变量，它们也可以产生新的观测。如果匹配不是唯一的，一个连接会将匹配的观测所有可能的组合都加上。

```{r}
exm1 <- data_frame(x = 5:8, y = c(5, 8, 9, 10))
exm2 <- data_frame(x = 5:8, z = c("apple", "orange", "pear", "orange"))
exm1 %>% left_join(exm2)
```

### 筛选连接（Filtering joins）

筛选连接用变化连接的方法来匹配观测，但是影响的是观测值，而不是变量。有两种类型：

- `semi_join(x, y)`**保留**了`x`中与`y`中有匹配的所有观测。
- `anti_join(x, y)`**丢掉**了`x`中与`y`中有匹配的所有观测。

这些在诊断连接不匹配时是非常有用的。

```{r}
id_psy <- 1:6
id_soc <- 1:5
score_psy <- c(88, 89, 87, 92, 94, 85)
score_soc <- c(85, 87, 82, 95, 93)
gender_psy <- c("male", "male", "female", "male", "female", "female")
gender_soc <- c("male", "female", "female", "female", "male")
class_psy <- data.frame(id = id_psy, score = score_psy,  gender_psy)
class_soc <- data.frame(id = id_soc, score = score_soc,  gender_soc)
anti_join(class_psy, class_soc, by = "score")
```

若担心连接会匹配的观测，那就以`semi_join()`或`anti_join()`开始。`semi_join()`和`anti_join()`不会进行复制，它们只能删除变量。

```{r}
exm1 <- data_frame(x = 5:8, y = c(5, 8, 9, 10))
exm2 <- data_frame(x = 4:6, z = c("apple", "orange",  "orange"))
exm1 %>% nrow()
exm1 %>% inner_join(exm2, by = "x")
exm1 %>% semi_join(exm2, by = "x")
```

### 集合运算

两表动词的最后一种类型是集合运算。这些操作要求`x`和`y`的输入有相同的变量，把观测当集合对待。

- `intersect(x, y)`：只返回`x`和`y`中都存在的观测。
- `union(x, y)`：返回`x`和`y`中不相同的观测。
- `setdiff(x, y)`：返回`x`中的观测，不返回`y`中的观测。

比如：

```{r}
exm1 <- data_frame(x = 1:3, y = 4:6)
exm2 <- data_frame(x = 1:3, y = c(7L, 8L, 9L))
```

四种可能的结果是：

```{r}
intersect(exm1, exm2)
union(exm1, exm2)
setdiff(exm1, exm2)
setdiff(exm2, exm1)
```

### 强制规则

连接表格时，**dplyr**在判断变量类型是否相同时比 base R 更保守一些。

- 有不同水平的因子会被警告强制转换成字符。

```{r}
exm1 <- data_frame(x = c(1, 2), y = factor("apple", "orange"))
exm2 <- data_frame(x = 3:4, y = factor("pear", "apple"))
full_join(exm1, exm2) %>% str()
```

- 有相同水平但顺序不同的因子也会被警告强制转换成字符。

```{r}
exm1 <- data_frame(x = 5, y = factor("apple", levels = c("apple", "orange")))
exm2 <- data_frame(x = 3, y = factor("orange", levels = c ("orange", "apple")))
full_join(exm1, exm2) %>% str()
```

- 因子只在水平完全匹配的情况下才会被保留。

```{r}
exm1 <- data_frame(x = 5, y = factor("apple", levels = c("apple", "orange")))
exm2 <- data_frame(x = 3, y = factor("orange", levels = c ("apple", "orange")))
full_join(exm1, exm2) %>% str()
```

- 一个因子和一个字符会被警告强制转换成字符。

```{r}
exm1 <- data_frame(x = 5, y = factor("apple"))
exm2 <- data_frame(x = 3, y = "apple")
full_join(exm1, exm2) %>% str()
```

另外（otherwise），逻辑值（logicals）会默认向上取整，整型变成数值型，但是强制变成字符型会出现错误。

```{r}
exm1 <- data_frame(x = 2.5, y = 2)
exm2 <- data_frame(x = 3L, y = 3)
full_join(exm1, exm2) %>% str()
```
```{r}
exm1 <- data_frame(x = "a", y = 2)
exm2 <- data_frame(x = 3L, y = 3)
full_join(exm1, exm2) %>% str()
```

## 多表动词

**dplyr**没有可以直接操作三个及以上表格的函数。不要像 Advanced R 中提到的使用`purrr:reduce()`或者`Reduce()`，可以迭代将二表动词合并，从而操作你所需要的表格。




## 视窗函数

**视窗函数**是聚集函数的变种。聚集函数有 n 个输入，返回一个值，比如`sum()`和`mean()`；视窗函数返回 n 个值。视窗函数的输出依赖于所有输入值，所以视窗函数中没有直接操作元素的功能，就像`+`或`round()`那样。视窗函数包括：聚集函数的变种，比如`cumsum()`和`cummean()`；排序函数，比如`rank()`；以及补偿函数（taking offsets)，比如`lead()`和`lag()`。

```{r}
exm_cars <- mtcars %>%
  as_tibble() %>%
  select(mpg, cyl, hp, wt) %>%
  arrange(mpg, hp)
```

视窗函数经常和`mutate()`、`filter()`函数一起使用。

```{r}
filter(exm_cars, min_rank(desc(mpg)) <= 6 & mpg > 0)
mutate(exm_cars, hp_rank = min_rank(hp))

filter(exm_cars, hp > lag(hp))
mutate(exm_cars, )  # 这个地方编不出有意义的例子了……
 
filter(exm_cars, mpg > mean(mpg))
mutate(exm_cars, mpg_z = (mpg - mean(mpg)) / sd(mpg))
```

在读懂这些内容之前，应熟悉`mutate()`和`filter()`。

### 视窗函数的类型

有五种视窗函数。其中两种与聚集函数无关。

- 排序函数：`row_number()`、`min_rank()`、`dense_rank()`、`cume_dist()`、`percent_rank()`和`ntile()`。这些函数都是对单个向量进行操作，最后可以返回多种类型的排序。

- 补偿函数`lead()`和`lag()`可以获得向量中前一个和后一个值，使得计算差异和趋势更加容易了。

另外三种视窗函数都是常见聚集函数的变种。

- 累积函数：`cumsum()`、`cummin()`、`cummax()`（from Base R），以及`cumall()`、`cumany()`、`cummean()`（from **dplyr**）

- 在固定宽度的视窗中进行聚集运算。Base R 和**dplyr**中没有这种函数，但是其他包里有，比如**RcppRoll**。

- 循环聚集，在这种情况下会重复聚集运算来匹配输入的长度。这在 R 中是不需要的，因为向量循环会自动在需要的地方进行循环聚集运算。这些函数在 SQL 中是很重要的，因为聚集函数的存在会让数据库每组只返回一行。

每种函数的具体用法在下文有详细介绍，主要关注的是函数的总体目标以及如何在**dplyr**中使用。如果想了解得更多，可以参考单个函数的文档。

### 排序函数

排序函数都是同一个主题的变种，区别在于如何处理连结问题（handle ties）。

```{r}
x <- c(2, 3, 3, 4, 4)
row_number(x)
min_rank(x)
dense_rank(x)
```

如果对 R 比较熟悉的话，你会发现`row_number`和`min_rank()`的功能，可以通过基础的`rank()`和`ties_method`的不同参数值来实现。这些函数可以让我们少打一些代码，而且在 R 和 SQL 之前转换也更容易了。

有两个另外的排序函数可以返回0到1之间的数字。`percent_rank()`可以给出排序的百分数；`cume_dist()`可以给出小于或等于当前值的比例。

```{r}
cume_dist(x)
percent_rank(x)
```

这在你想得到一组值中前10%的数据时会很有用。

```{r}
filter(exm_cars, cume_dist(desc(hp)) < 0.1)
```

最后，`ntile()`把数据平分成了 n 份。这是一种粗糙的排序，可以和`mutate()`一起使用来划分数据，从而更进一步分析。

```{r}
math_psy <- c(88, 89, 90, 85, 86, 91, 94, 93)
age_psy <- c(19, 20, 20, 20, 18, 21, 19, 21)
id_psy <- 1:8
students <- data.frame(math_psy, age_psy, id_psy)
students_psy <- group_by(students, math_psy, id_psy)
class_psy <- summarise(students_psy, score_math = sum(math_psy))
class_psy_quartile <- group_by(class_psy, quartile = ntile(math_psy, 4))   # 例子可能有问题
summarise(class_psy_quartile, mean(math_psy))
```

所有排序函数都是按从小到大的顺序进行排列的，这样小的输入值也会得到小的排序数。可以使用`desc()`来从大到小进行排列。

### 领先和延后（Lead and lag）

`lead()`和`lag()`可以生成输入向量的补偿版本，可以是原始向量前面的，也可以是原始向量后面的。

```{r}
x <- c(1, 3, 5, 6, 7)
lead(x)
lag(x)
```

可以使用这些函数来：

- 计算差异和百分比的变化。

```{r}
mutate(students,math_delta = math_psy - lag(math_psy))
```

`lag()`比`diff()`使用起来更方便，因为当有`n`个输入时，`diff()`只能返回`n - 1`个输出值。

- 可以发现数值的变化。

```{r}
filter(students, id_psy != lag(id_psy))
```

`lead()`和`lag()`都有一个可选参数`order_by`。如果设置这个参数的话，函数会使用另外的变量代替行的顺序来决定哪个值在前哪个值在后。当你还没有将数据分类或者想要以一种方式分类、滞后另一种（sort one way and lag another）的时候，设置这个参数是非常重要的。

下面的例子显示出如果在需要时你没有特定化`order_by`会发生什么。

```{r}
exm <- data.frame(x = 15:20, y = 0:5)
z <- exm[sample(nrow(exm)), ]

wrong <- mutate(z, running = cumsum(y))
arrange(wrong, x)

right <- mutate(z, running = order_by(x, cumsum(y)))
arrange(right, x)
```

### 累积聚集

Base R 提供了累积求和（`cumsum()`）、累积求最小值（`cummin()`）和累积求最大值（`cummax()`），也提供了`cumprod`但是很少用到。其他常见的累加函数有`cumany()`和`cumall()`，`||`和`&&`的累积版本，以及求累积平均值`cummean()`。这些函数没有被包括在 Base R 中，但是**dplyr**提供了一些更高效的版本。

`cumany()`和`cumall()`在一个条件第一次（或最后一次）为真时，选择所有在此条件之前的行或所有在此条件之后的行时是很有用的。

```{r}
filter(mtcars, cumany(mpg > 30))
```

像领先和滞后函数，你可能会想控制累加发生的顺序。但是这些函数都没有`order_by`参数，所以**dplyr**提供了`order_by()`来解决这个问题。将排序时依照的变量和视窗函数的调用赋给这个函数就可以使用了。

```{r}
x <- 1:5
y <- 5:1
order_by(y, cumsum(x))
```

这个函数使用了不太规范的评估，所以不推荐在函数内部使用；在函数内部可以使用`with_order()`这个更简单但精度也更低的函数。

### 循环累积

R 的向量循环在挑选那些大于或小于一个总括性数据的值时会容易一些。之所以称之为循环累积，是因为累积的值和原始向量的长度是一样的。当你想要找到所有大于平均数小于中位数的记录时，循环累积是很有帮助。

```{r}
filter(mtcars, mpg > mean(mog))
filter(mtcars, mpg < median(mpg))
```

尽管大多数 SQL 数据库都没有与`median()`或`quantile()`功能类似的函数，在筛选时可以使用`ntile()`达到同样的效果。比如，`x > median(x)`和`ntile(x, 2) == 2`是等同的；`x > quantile(x, 75)`和`ntile(x, 100) > 75`或`ntile(x, 4) > 3`是等同的。

```{r}
filter(mtcars, ntile(hp, 2) == 2)
```

你也可以用这种方法选出一个字段中最大值（`x == max(x)`）或最小值（`x == min(x)`）的记录，不过排序函数会让你对连结有更多控制，而且可以选择记录中的任何数字。

循环聚集和`mutate()`一起使用时也是很有用的。

```{r}
math_psy <- c(88, 89, 90, 85, 86, 91, 94, 93)
age_psy <- c(19, 20, 20, 20, 18, 21, 19, 21)
id_psy <- 1:8
students <- data.frame(math_psy, age_psy, id_psy)
mutate(students, difference_value = math_psy - min(math_psy))
```

也可以计算 z 分数。

```{r}
mutate(mtcars, mpg_z = (mpg - mean(mpg)) / sd(mpg))
```
