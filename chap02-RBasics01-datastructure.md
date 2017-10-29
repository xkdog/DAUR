
## R 数据结构基础

### 数据对象（Object）

R 中储存和操纵的实体（entity）可统称为对象（object），其基础数据类型包括向量（vector）、矩阵（matrix）、数组（array）、数据框（data frame）和列表（list），也可包括由这些实体定义的更一般性的结构（structures）。实体、对象和结构，以及后面会涉及的“类”、“S3”、“S4”等，都是抽象的计算机术语，初学者不必过分在意其“本质定义”，仅从功能性或操作性的视角加以理解即可。

R 中规范的对象赋值（assign）符号使用箭头符号`<-`或`->`，后者比较少用。箭头实际上表示赋值的方向，箭头所指处通常为对象名。例如

```{r}
x <- rnorm(10, mean = 0, sd = 1) 
x
```

第一行语句表示产生10个服从标准正态分布$N(0, 1)$的随机数，并将其赋值为`x`，即储存为数据对象`x`。仅执行第一条语句并不能直接在 R 的控制台显示这一对象本身，需要像第二行那样输入对象名让 R 去调用这一对象，从而进行展示。由于是随机生成的，故读者在重复这一代码时会产生不同的结果。如想使结果具有可重复性，可使用`set.seed()`函数设定随机数种子数（seed），相同的种子数总会产生相同的结果，但这一结果仅对`set.seed()`之后的第一条命令适用。

```{r}
set.seed(1234)
y <- rnorm(10, mean = 0, sd = 1) 
y
```

注意上述命令中的参数（parameter）`mean = 1, sd = 1`其实可以省略，即写成`rnorm(10)`；或者写成`rnorm(10, 0, 1)`也可。因为`rnorm()`函数默认的参数取值与顺序就是如此。除非要设定其他均值或标准差，否则没有必要申明参数取值。


使用`ls()`函数可查看当前环境中的所有已赋值对象。`ls`其实是英文 list（列出）的缩写。

```{r}
ls()
```

而要移除（remove）某一对象，可使用`rm()`命令，`rm`正是 remove 的缩写。

```{r}
rm(x, y)
```

如要移除所有对象，可使用命令

```{r}
rm(list = ls()) # 移除所有对象
ls() # 显示空对象时的结果
```

`#`号表示注释，注释掉的内容不会作为程序执行。结果显示`character(0)`，即不存在任何对象。`rm(list = ls())`经常用在新建某个工作任务的时候。若该任务未完成，要谨慎使用这一命令，否则会移除所有前期工作结果。


一般意义上，R 中的数据类型可以分为如下类型：

- 数值型（numeric），又可分为整型（integer）和双精度型（double）
- 逻辑型（logical），取值只能为真（`TRUE`）或假（`FALSE`）
- 字符型（character），夹在双引号（`""`）或单引号（`''`）之间的字符串（string）
- 复数型（complex），表示数学上的复数（complex nubmer），具有`a + bi`的形式
- 原味型（raw），即二进制形式保存的数据

最后两种类型的数据对象在一般的数据分析中很少用到，这里不加介绍。前三种类型的具体内容在后面详细说明。

在 R 中，还有一些特殊符号，表示数据对象的特殊取值。

- `Inf`，表示无穷大（infinity），负无穷大表示为`-Inf`。如输入`1/0`即可显示`Inf`。
- `NA`，缺失值（not available）。
- `NaN`，表示不确定（not a number），如`0/0`的结果即是`NaN`。
- `NULL`，表示意义为空的对象。

要注意对包含`NA`值的变量施加任何运算，结果均为`NA`。

```{r}
z <- c(1, 2, 3, NA)
mean(z)
```

上述第一行命令生成一个由数字1、2、3和缺失值`NA`四个元素构成的数列（数值向量）。第二行命令表示对这一数据求均值（mean）。结果返回为`NA`，这是因为`z`中存在缺失值 的原因。若想计算时剔除缺失值，仅对有具体取值的对象进行计算，可将`mean()`函数（及其他类似函数）中的`na.rm = `参数设定为`TRUE`。

```{r}
mean(z, na.rm = TRUE)
```


根据其中所包含的元素（element）是否异质（是否可以包含不同类型的数据）及其存储的维度，可对向量、矩阵、数组、数据框、列表这几种常见的数据结构或数据类型做如下表格分类。

|          | 向量 | 矩阵 | 数组 | 数据框 | 列表 |
|:--------:|:----:|:----:|:----:|:------:|:----:|
| 数据类型 | 同质 | 同质 | 同质 |  异质  | 异质 |
| 存储维度 | 一维 | 二维 | 多维 |  二维  | 一维 |

下面进行具体介绍，并在此基础上介绍 R 中两种特殊的数据类型：因子（factor）与 tibble。


R 中所有对象的定义可参见其官方网页的说明：

<https://cran.r-project.org/doc/manuals/r-release/R-lang.html#Objects>



### 向量（Vector）

向量是 R 中最简单的数据类型，它通常用来存储数值型（numeric）、字符型（character）或逻辑型（logical）数据，且一个向量只能存储一种类型的数据。向量使用`c()`函数进行创建，其中`c`表示 concatenate（联结、串联），或者 combine。R 中不存在“标量”（scalar），单个数字或字符串等所谓的标量其实是只含一个元素的向量。

```{r}
vector_num <- c(1, 2, 3)  
vector_char <- c("one", "two", "three") 
vector_log <- c(T, TRUE, F, FALSE)   
```

其中

- 三行命令分别用于创建数值型、字符型和逻辑型向量 
- 字符型向量中的元素应放在引号（`""`或`''`）中
- `T`与`F`是逻辑型数据`TRUE`与`FALSE`的缩写。多数情况下，`T`与`TRUE`、`F`与`FALSE`分别等价，故定义变量名时尽量不要使用这两个大写字母。从规范性出发，尽量不要用缩写表示逻辑型数据。
- `c(1, 2, 3)`与`c(1:3)`等价。还可使用类似`vector_num <- 1:5`的简化命令，但更推荐规范的创建方式。


使用`vector_name[]`的形式可调用向量中的元素，`[]`中填入表示元素位置的整数。

```{r}
vector_char <- c("one", "two", "three")
vector_char[2]
vector_char[2:3]
vector_char[c(1, 3)]
```
以上三种方式分别调用`vector_char`向量的第2个元素、第2到第3个元素、第1和第3个元素。

在 R 中，向量的长度（length）是指其所包含的元素个数，这可用函数`length()`判定。

```{r}
length(vector_char)
```

可使用`is.numeric()`、`is.character()`、`is.logical()`命令来判断向量是否为指定类型。

```{r}
is.numeric(vector_num)
is.character(vector_char)
is.logical(vector_log)
is.numeric(vector_char)
```

在 R 中，用`typeof()`函数可判定对象的类型（数值型、字符型或逻辑型），而`mode()`函数可判定对象的模式。两者之间存在微妙的区别，试观察如下结果。

```{r}
x <- c(1:5)
typeof(x)
mode(x)
y <- x / 5
typeof(y)
mode(y)
z <- c("中国", "天津", "南开")
typeof(z)
mode(z)
```

所谓的数值型向量（numeric）其实包含两种类型数据：数型（integer）与双精度型（double）。前者只存储整数而不包含小数，后者可存储`2e-308`至`2e+308`之间的实数，其中`E`表示科学计数法，`2E-308`表示 $2 \times 10^{-308}$，`2E+308`表示 $2 \times 10^{308}$。在计算机中，即使无限小数也有一定的限定精度，因此会出现如下情形

```{r}
(sqrt(2)) ^ 2 == 2
```

其中`sqrt(2)`表示取2的算术平方根，然后再对其平方（`^2`），再让 R 去判断是否精确等于（`==`）数字2，返回结果为否。这正是计算机语言与数学语言的一个区别。好在就通常意义上的数据分析而言，如此微小的计算误差通常可以忽略不计。

若要从一般的数学意义上比较两个数字或数字向量之间的大小差别，可使用 **dplyr** 包中的`near(x, y)`函数，其中`x`、`y`表示两个数字向量。

```{r}
dplyr::near(sqrt(2) ^ 2, 2)
```

向量是 R 数据结构的基础。R 中的诸多函数都是**向量化（vectorization）**的，即对向量施加的运算会作用于该向量的每一个元素。前面命令中的`y <- x / 5`已经体现了向量化运算的实质。以下是其他示例。

```{r}
x <- c(1:10)
x + 100
sqrt(x) 
y <- c(10:1)
x > y
pmax(x, y)
```

其中

- `sqrt()`表示求算术平方根（square root）
- `x > y`给出逻辑向量，要求`x`与`y`具有同样的长度，检测相同位置上`x`中的元素是否大于`y`中的元素
- `pmax(x, y)`给出两个同长度数值型向量中对应位置上的较大值

从中不难理解为什么 R 要求向量中的元素必须具备同样的模式，否则就无法进行向量化的运算。

另外可以发现，R 中的对象名称可以重复定义，最近一次赋值的对象会覆盖（即替换）原有对象，且不会有Windows中常见的“你是否确定……”的弹窗提醒。例如

```{r}
x <- c(1:10)
x 
x <- c(10:1)
x
x <- x + 1
x
```

尤其值得注意的是`x <- x + 1`这种赋值方式。这在传统数学意义上并不常见，但在计算机程序中却很常用，`<-`不妨理解一个箭头，代表着赋值的方向。如果不想覆盖原对象，最好将运算后的对象赋值于新的对象名。


### 矩阵（Matrix）

矩阵是仅包含同质数据的二维数据结构，可以理解为许多同类型、等长度向量的组合。矩阵使用`matrix()`函数进行创建。

```{r}
matrix_01 <- matrix(1:6, nrow = 2, ncol = 3)
matrix_01
```

这是最基础的示例，其中`nrow = `参数用于设定行数（number of rows），`ncol = `参数用于设定列数（number of columns）。两者确定一个，即可确定矩阵的形式。

从`matrix_01`的结果可看出，`matrix()`函数中默认是以“按列填充”的方式排列数据的。若要按行填充，则可使用`byrow = TRUE`参数。

```{r}
matrix_02 <- matrix(1:6, nrow = 2, byrow = TRUE)
matrix_02
```

还可通过如下方式设置矩阵的维度名称，以增强矩阵的可读性。

```{r}
cells <- c(1:6)
row_names <- c("R1", "R2", "R3")
col_names <- c("C1", "C2")
matrix_03 <- matrix(cells, nrow = 3, byrow = TRUE, dimnames = list(row_names, col_names))
matrix_03
```


另可通过`dim()`函数对向量添加维度（dimention）属性创建矩阵。

```{r}
vector_matrix <- c(1, 3, 5, 7, 9, 11)
dim(vector_matrix) <- c(3, 2)
vector_matrix
```


可使用`matrix_name[, ]`的方式调用矩阵中的元素，逗号前填入行数，逗号事情填入列数，若这两个位置中某个位置留空，则表示选择整行或整列数据。

```{r}
matrix_04 <- matrix(1:8, nrow = 4)
matrix_04
matrix_04[2, ]
matrix_04[, 1]
matrix_04[4,2]
matrix_04[c(1, 3),2]
```

最后一行命令使用了`c(1, 3)`的方式指定了多个不同行。


### 数组（Array）

数组是矩阵的拓展形式，同样只能存储同质数据，但可有2个以上维度。数组使用`array()`函数创建。对初学者而言，这种形式的数据结构较少遇见。

以下命令可创建 $3 \times 2 \times 2$、且各维度均有命名的数组，并填充数字1到12。

```{r}
dim1 <- c("a1","a2","a3")
dim2 <- c("b1","b2")
dim3 <- c("c1","c2")
array_01 <- array(1:12, c(3, 2, 2), dimnames = list(dim1, dim2, dim3))
array_01
```

同样也可通过对向量添加`dim()`属性来创建数组。

```{r}
vector_array <- c(1, 3, 5, 7, 9, 11, 13, 15)
dim(vector_array) <- c(2, 2, 2)
vector_array
```

数组中元素的调用方式与矩阵类似，可使用`array_name[,,]`的形式。

```{r}
array_01[1, , ]
array_01[2, 1, ]
array_01[, , 2]
array_01[1, 1, 1]
```


### 数据框（Data Frame）

数据框是 R 里应用最多的数据结构，能存储不同类型的数据，可视为许多等长度（但类型可不相同）向量的组合，也可视为解除了存储数据类型限制的矩阵。数据框使用`data.frame()`函数创建。


以下命令可在 R 中创建一个学生信息数据框。

```{r}
student_id <- c(1:4)
student_name <- c("a001", "a002", "a003", "a004")
male <- c(TRUE, TRUE, FALSE, FALSE)
student_data01 <- data.frame(student_id, student_name, male)
student_data01
```

可通过`row.names = `参数设定行标志符（case identifier），即把每行的名称等同于某列中对应行的元素取值。试比较如下命令创建的`student_data02`与`student_data01`的显示结果。

```{r}
student_data02 <- data.frame(student_id, student_name, male, row.names = student_name)
student_data02
```


当两个数据框行数相同时，可使用`cbind()`进行合并（bind columns），也可使用此函数将独立的向量合并到数据框中；当两个数据框列数相同且列名一一对应时，可使用`rbind()`进行合并（bind rows）。

先看`cbind()`的示例。

```{r}
age  <-  c(18, 18, 17, 16)
cbind(student_data01, age)
```

再看`rbind()`的示例。

```{r}
rbind(student_data01, data.frame(student_id = 5, student_name = "a005", male = TRUE))
```
上例中临时创建了未命名的数据框，将其合并到了已有数据框`student_data01`中。

在数据框内，列表示**变量（variables）**，行表示**观测（observations）**。此后行文中，列与变量、行与观测分别同义，可替换使用。

与矩阵类似，可使用`dataframe_name[,]`的形式来读取数据框中的元素。
```{r}
student_data01[2:3]
student_data01[1, ]
student_data01[, 1]
student_data01[1, 2]
```

也可使用美元符号`$`提取数据框中的列。

```{r}
student_data01$student_id
student_data01$male
```

还可使用双方括号`[[]]`（通常也简写与`[[`）的形式提取元素。使用`[[]]`时，首先提取数据框中下一个层级的元素（即列），还可在`[[]]`后附加`[]`提取再下一层级的元素（即向量中的基础元素）。请看下例。

```{r}
student_data01[[2]]
student_data01[[2]][3]
```

`[[]]`的链式提取特征在复杂数据结构的元素提取中是较为方便的。


使用绑定函数`attach()`和解绑函数`detach()`可以让命令变得简单。绑定后可省略数据框名进行框内数据访问。若同时绑定多个数据框，涉及重名的变量以最近一次绑定的数据框中的变量为准。在真实的数据处理中，应避免过多使用这种方式绑定数据框。

```{r}
attach(mtcars)
mpg
detach(mtcars)
```

如果未绑定`mtcars`数据框，则会出现如下错误提示：

```r
mpg
Error: object 'mpg' not found
```


另外，可使用`with()`和`within()`绑定数据框，前者只可绑定但不可修改数据框，后者则可对绑定的数据框进行修改。

使用`with()`命令时，左括号`(`后写入数据框名，待实现的命令需放在`{}`中，可分行写入不同命令。注意此时一般应保存命令结果或使用`print()`输出单行命令的结果，否则默认输出最后一行命令。例如：

```{r}
with(mtcars, {
  print(summary(mpg))
  plot(mpg, wt)
})
```

其中

- `summary()`函数用于输出变量的描述统计结果，分别是（括号中为输入结果中对应的英文）最小值（`Min`）、第一四分位数（`1st Qu`）、中位数（`Median`）、均值（`Mean`）、第三四分位数（`3rd Qu`）和最大值（`Max`）。
- `plot()`函数用于绘制两个变量之间的散点图（scatterplot）。

试比较：

```{r}
with(mtcars, {
  summary(mpg)
  plot(mpg, wt)
})
```

此时结果中并没有输入`summary(mpg)`的结果。

`within()`命令的用法与`with()`类似，但可修改数据框，但应注意保存操作结果。

```{r}
mtcars_new <- within(mtcars, {
  high <- NA
  high[mpg >= median(mpg)] <- TRUE
  high[mpg < median(mpg)] <- FALSE
})
```

- `high <- NA`命令在`mtcars`数据框中新生成一个变量，先将其赋值为缺失值（`NA`）
- `high[mpg >= median(mpg)]`这种方括号的用法在 R 的数据框操纵中非常常见，在此`[]`表示特定的条件。若其对应的`mpg`大于等于中位数，则将`high`赋值为`TRUE`；反之赋值为`FALSE`。

最后可用`head()`命令观察增加新变量的数据框的前6行。

```{r}
head(mtcars_new)
```

此例实际上给出了 R 中为指定数据框新增变量的基本方法。

### 列表（List）

列表是 R 中最复杂的数据类型，可包含任何类型的数据，包括向量、矩阵、数组、数据框，还可嵌套包含其他列表，各成分的元素性质与长度可不统一。列表使用`list()`函数创建，并可以为列表内项目命名。

```{r}
a <- c(1:3)
b <- matrix(1:6, c(2, 3))
c <- array(1:12, c(2, 2, 3))
d <- student_data01
list_01 <- list(name_01 = a, name_02 = b, name_03 = c, name_04 = d)
```

上述命令创建了包含向量、矩阵、数组和数据框的列表，并将列表中下一层级的数据分别命名为 `name_01`至`name_04`。

```{r}
list_01
```


列表同样可使用`[[`或`$`符号进行内部元素的提取。

```{r}
list_01[[2]]
list_01[[2]][2]
list_01$name_01
```

在知道列表内部元素的命名时，使用`$`提取是较为方便的。否则，使用双方括号的形式提取可能更有针对性。

使用`str()`可展示列表（及其他数据类型）的结构。

```{r}
str(list_01)
```

这对了解某些复杂结构的数据对象很有帮助。以下命令给出了`mtcars`数据中以`wt`为自变量，建立`mpg`与`wt`之间的一元线性回归方程模型后，R 中保存的拟合模型（此处命名为`fit`）的数据结构信息。

```{r}
fit <- lm(mpg ~ wt, data = mtcars)
summary(fit)
str(fit)
```

`str(fit)`的具体内容此时暂不必深究，仅供了解数据结构使用。R 中诸多统计模型的命令结果均储存为列表，若要提取其中某些元素，宜先用此命令检视其数据结构。

### 因子（Factor）

一般数据分析中称为类型变量（categorical data）的数据 ，在 R  中都以因子的形式储存。类型变量依测量水平高低又可分为定类（nominal level，又译称名尺度）和定序（ordinal level，又译顺序尺度）两种类型，前者只有类别属性之分，如通常意义上的颜色（非光谱学意义上）、性别、种族等，后者则有程度大小之别，如年级、以优良中差等方式标注的等级成绩，以及以满意、中立、不满意等方式呈现的对某项公共政策的满意程度等。前者可称为无序因子，后者可称为有序因子（ordered factor）。

技术上讲，因子是建立在整型（integer）向量基础上、只能包含预先定义数值的向量，它具有不同的水平（levels），用于表示因子的所有可能取值。因子和水平的翻译，熟悉实验设计或方差分析的读者应该并不陌生。因子可使用`factor()`函数创建，其用法如下：

```r
factor(x, levels = , labels = levels, ordered = , ...)
```
其中

- `x`表示待因子化的向量
- `levels`表示因子的取值（水平）
- `labels`表示因子的取值标签（默认等同于因子水平本身）
- `ordered`用于设定有序因子
- 其他参数请使用`?factor`命令查看

以下通过示例说明`factor()`函数的用法。

```{r}
factor_01 <- factor(c("male", "female" ,"male", "female"))
factor_01
mode(factor_01)
as.numeric(factor_01)
levels(factor_01)
```

从结果中可看出，R 自动将因子`factor_01`存储为向量`(1, 2, 2, 1)`的形式，其中`female = 1`, `male = 2`，这实际是按变量取值的字母序进行赋值，即确定其水平的排序。使用`levels()`命令可查看因子型数据的具体取值。

若要指定因子水平的特定顺序（非字母序）时，可使用`factor()`中的`levels = `参数进行设定。

```{r}
factor_02 <- factor(c("male", "female" ,"male", "female"), levels = c("male", "female"))
levels(factor_02)
```

针对定序数据，可能需要指定因子的取值排序，使之成为有序因子。此时需使用参数`ordered = TURE`加以设定。

```{r}
grade_01 <- factor(c("freshman", "sophomore", "junior", "senior"))
levels(grade_01)
as.numeric(grade_01)
grade_02 <-
  factor(
    c("freshman", "sophomore", "junior", "senior"),
    levels = c("freshman", "sophomore", "junior", "senior"),
    ordered = TRUE
  )
levels(grade_02)
as.numeric(grade_02)
```

对于数值型向量，进行因子化时可采用`labels`参数进行设置。

```{r}
gender <- c(1, 0, 0, 1)
gender_factor <- factor(gender, levels = c(0, 1), labels = c("female", "male"))
gender_factor
levels(gender_factor)
```


需要说明的是，R 在读入外部数据转为数据框的过程中，默认将所有字符型变量转化为了因子数据加以储存。这对于早期的计算机具有节约内存的效果，但对当下的计算机硬件性能而言，这种优势已几乎不复存在。因此，许多后起的 R 包在读入外部数据，不再默认对字符型变量进行因子化，具体宜参考其说明文档加以识别。


### tibble

tibble 并不是 R 自有的数据类型。它是 Hadley 等人在数据框函数`data.frame()`基础上衍生的一种扩展性数据框。它本质就是一种数据框，但在显示形式上更加人性化，应用更加方便，可存储任意类型数据。tibble 暂无明确的中文翻译，这里直接使用英文（通常缩写为 tbl）。此类型数据依赖于 **tibble** 包（需自行安装），可使用其中`tibble()`函数创建。向量、矩阵和数据框均可通过`as_tibble()`函数转换为 tibble。

以下示例假定已安装 **tibble** 包。

```{r}
library(tibble)
tbl_01 <- tibble(x = 1:5, y = letters[1:5])
tbl_01
```

可以看到 tibble 类型的数据框列名下标示出此列数据类型的缩写，这是它不同于传统数据框的一个特征。同时，在显示 tibble 时，首先给出该 tibble 的维度（行数 $\times$ 列数）。tibble 定义了7种数据类型的缩写：int（integer，整型）、dbl（double，双精度）、chr（character ，字符型）、dttm（date + time，日期 + 时间）、lgl（logical，逻辑型）、fctr（factor，因子）、date（日期）。


对于较长的数据框，tibble 默认只显示前10行（相当于`head(x, 10)`的效果），并给出剩余的行数；其显示的列数则视显示窗口的大小而自动调整。这对于展示大型数据框的基本信息是非常有利的。

```{r}
mtcars_tibble <- as_tibble(mtcars)
mtcars_tibble
```

再看一个较大的数据框，以`cgss2013.dta`为例 ^[`cgss2013`是中国综合社会调查（Chinese General Social Survey，CGSS）2013年数据的简称，数据储存为`.dta`格式，即 Stata 格式。中国综合社会调查始于2003年，是我国最早的全国性、综合性、连续性学术调查项目。该系列数据可从[CGSS官网](http://www.chinagss.org/)注册后下载。]进行。这里使用 **haven** 包中的`read_dta()`函数读入。读入后自动转为 tibble。

```{r}
library(haven)
cgss2013 <- read_dta("cgss2013.dta")
length(names(cgss2013))
```

`names()`用于列示数据框中的所有变量名，`length()`用于显示其长度，即变量个数。结果显示有722个。若将所有变量与数据进行展示，无疑会占据很大的屏幕空间，多数情况下不是必要的。tibble 可根据屏幕宽度自动调整，并只显示前10行，然后给出相关信息。

```{r}
cgss2013
```


tibble 本身即是数据框，但具有普通数据框不具备的特征；但并不是所有的数据框都是 tibble。这一点从以下命令中可以看得很清楚。

```{r}
is.data.frame(mtcars_tibble)
is.tibble(mtcars_tibble)
is.tibble(mtcars)
```

其他关于 tibble 的更详细特征，可参见其说明文档：

<https://cran.r-project.org/web/packages/tibble/vignettes/tibble.html>


### R 数据结构进阶



