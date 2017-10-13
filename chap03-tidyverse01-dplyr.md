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

按世界卫生组织（WHO）的分类，成人 BMI 指数与肥胖程度之间的分类如下：

BMI | 肥胖程度
----|---------
< 18.5 | 偏瘦（Underweight）
18.5~24.9 | 正常（Normal）
25.0~29.9 | 偏胖（Overweight）
`>=` 30.0   | 肥胖（Obesity）


依据此表，对前述 BMI 值进行分类，该变量命名为`class`。

此时使用R的自有函数`within()`来修改数据框可能更为方便。

```{r}
women_bmi <- within(women_bmi, {
  class = NA
  class[bmi < 18] = "Underweight"
  class[bmi >= 18 & bmi <= 24.9] = "Normal"
  class[bmi >= 25 & bmi <= 29.9] =  "Overweight"
  class[bmi >= 30] =  "Obesity"
})
head(women_bmi)
```

显然所有`class`的取值均为正常（normal）。基于均值计算的 BMI 指数通常并无实际意义，只有对独立的个体进行体重是否超标的判定才有实际价值。此例只作为对变量的操纵示例。

结合 R 自有函数`cut()`，仍可在`mutate()`框架下完成同样的工作。

```{r}
women_bmi2 <- mutate(
  women_bmi,
  class = cut(
    bmi,
    breaks = c(0, 18.5, 25, 30, 100),
    labels = c("Underweight", "Normal", "Overweight", "Obesity"),
    right  = FALSE
  )
)
head(women_bmi2)
```

两种方式的效果完全相同。使用`cut()`的便利之处在于可以将这一命令嵌套于整体代码中，使代码变得更为紧凑可读。下面将前述命令整合成一个代码块，一步到位实现数据处理要求。

```{r}
women_bmi <- mutate(
  women,
  height_cm = round(height * 2.54),
  weight_kg = round(weight * 0.45),
  bmi = round((weight_kg / (height_cm / 100) ^ 2), 1),
  class = cut(
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
- `right` 用于设定分组的右侧端点值是否包含在本组之内

特别要注意的是`breaks =`的设定与`right =`的设定匹配。例如，若要分4组，则需要有5个分界点。若设定：

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

此时，`mtcars_subset`数据就只剩下`mpg`和`wt`两个变量。

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

`arrage()`函数的用法如下：

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


