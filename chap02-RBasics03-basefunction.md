
## 演示及修改内容：chap02-RBasics03-base_function
(PS：改动部分较少，只有四处单词拼写错误——第84、89、126、269行，一处命令书写错误——第100行，两处描述修改——第162、201行,有改动的部分在word文档中以红色字体标出)


## R 基础函数

R 内置诸多功能丰富的基础函数，使用这些函数不需要先加载任何包，启动 R 后即可运行。这里粗略地分为数学函数、统计函数、向量计算函数、概率函数、逻辑与集合函数、类型判断与转换函数、日期格式函数、帮助函数、工作目录与工作空间函数、`apply()`族函数等类别进行介绍，并首先介绍了基本的算术运算符和逻辑运算符。部分函数的分类可能有所重叠。R 本身内置的文本数据分析函数、即字符串处理函数（string functions），将在介绍 **stringr** 包时再行介绍。

如无特殊说明，以下举例中的`x`、`y`等均指向量。各函数用法在解释其基本功能后，易于理解者不再举例，复杂者再行举例说明。部分专业术语给出英文，方便记忆与对照。初学者宜花一定时间，尝试以下所有函数，熟悉 R 的命令模式与函数风格。

如需了解更多 R 基础函数，可输入如下命令：

```{r}
help(package = "base")
```

### 算术运算符

运算符       |  功能
-------------|--------------
`+`          |  加/求和（sum）
`-`          |  减/求差（difference）
`*`          |  乘/求积（product）
`/`          |  除/求商（quotient）
`^`或`**`    |  求幂（乘方，power），如`5 ** 2`的结果为`25`
`x %% y`     |  求余运算，如`5 %% 2`的结果为`1`
`x %/% y`    |  整除运算，如`5 %/% 2`的结果为`2`

### 逻辑运算符

 运算符      |  功能
-------------|--------------
`<`          |  小于  
`<=`         |  小于等于
`>`          |  大于 
`>=`         |  大于等于
`==`         |  严格等于
`!=`         |  不等于
`!x`         |  非`x`
`x \| y`     | `x`或`y`
`x & y`      |  `x`和`y`
`isTRUE(X)`  |  测试`x`是否为TRUE
     
### 数学计算
 
 函数名                          |  功能
---------------------------------|--------------
`abs(x)`                         |  求绝对值（absolute  value）
`sign(x)`                        |  求数字向量`x`中各元素的“符号”（正数为1，负数为$-1$，0仍为0），`sign(-2:2)`返回值为`-1 -1 0 1 1`
`sqrt(x)`                        |  求算术平方根（square root）
`max(x)`                         |  求`x`中元素的最大值
`min(x)`                         |  求`x`中元素的最小值
`sum(x)`                         |  求`x`中的元素之和，如`sum(1:5)`返回值为`15`
`prod(x)`                        |  求`x`中元素的乘积（product意为乘积），如`prod(3, 4, 5)`返回值为`60`
`factorial()`                    |  求阶乘（factorial），如`factorial(10)`返回值为`3628800`
`diff(x)`                        |  求`x`中相邻元素之差
`pmax(x, y)`                     |  求`x`，`y`在相应位置上元素的较大值
`pmin(x, y)`                     |  求`x`，`y`在相应位置上元素的较小值
`cummax(x)`                      |  求累计最大值（从左向右），如`cummax(c(3:1, 2:0, 4:2))`返回值为 `3 3 3 3 3 3 4 4 4`
`cummin(x)`                      |  求累计最小值（从左向右），如`cummin(c(3:1, 2:0, 4:2))`返回值为 `3 2 1 1 1 0 0 0 0`
`cummprod(x)`                    |  求`x`各元素的累积乘积值，如`cumprod(1:10)`返回值为 `1 2 6 24 120 720 5040 40320 362880 3628800`
`cumsum(x)`                      |  计算`x`各元素的累加值，如`cumsum(1:10)`返回值为 `1 3 6 10 15 21 28 36 45 55`
`ceiling(x)`                     |  求大于等于`x`的最小整数，如`ceiling(3.14)`返回值为`4`
`floor(x)`                       |  求小于等于`x`的最大整数，如`floor(3.14)`返回值为`3`
`trunc(x)`                       |  向0的方向截取`x`中的整数部分，如`trunc(5.99)`返回值为`5`
`round(x)`                       |  将`x`舍入（round off）为指定数位的小数，如`round(3.1415, digits = 2)`返回值为`3.14`
`signif(x)`                      |  将`x`舍入为指定数位的有效数字（significant figures），如`signif(3.1415, digit=2)`返回值为`3.1`
`choose(n, k)`                   |  求组合数，即 $C _n ^k$ 或 $ _n C _k $，如`choose(10, 2)`返回值为`45`
`sin(x)`,`cos(x)`,`tan(x)`       |  求`x`的正弦值、余弦值、正切值
`asin(x)`,`acos(x)`,`atan(x)`    |  计算`x`的反正弦、反余弦、反正切
`sinpi(x)`,`cospi(x)`,`tanpi(x)` |  即为`sin(pi*x)`，`cos(pi*x)`，`tan(pi*x)`，如`sinpi(1/6)`返回值为`0.5`
`sinh(x)`                        |  双曲正弦函数 $\sinh(x)$
`cosh(x)`                        |  双曲余弦函数 $\cosh(x)$
`tanh(x)`                        |  双曲正切函数 $\tanh(x)$
`log(x, base=n)`                 |  对`x`取以 $n$ 为底的对数（logarithm）
`log(x)`                         |  对`x`取以自然底数 $\mathrm{e}$ 为底的对数
`log10(x)`                       |  对`x`取以10为底的对数
`log2(x)`                        |  对`x`取以2为底的对数
`log1p(x)`                       |  即 $\log_{\mathrm{e}} (1 + x)$,对 $1 + x$ 取以 $\mathrm{e}$ 为底的对数
`exp(x)`                         |  指数函数（exponential function），即 $\mathrm{e} ^ x$  
`expm1(x)`                       |  $\mathrm{e} ^ x - 1$ 
`integrate(f, lower, upper)`     |  求积分，`f`表示被积函数（integrand），`lower`表示积分下限，`upper`表示积分上限
`D(fun, "x")`                    |  求导函数，原函数`fun`通常使用`expression()`函数定义或直接输入
`eval(D(fun, "x"))`              |  求（设定`x`值后）指定点处的导数值，`eval`表示evaluate（计算）


### 统计计算
 
 函数                                |  功能
-------------------------------------|------------------------------------------------------
`mean(x)`                            |  求均值，如`mean(c(1, 3, 5, 7))`返回值为`4`
`median(x)`                          |  求中位数，如`median(c(1, 3, 5, 7))`返回值为`4`
`range(x)`                           |  求最小值与最大值之差（range 在统计中常翻译为极差或全距，在数学中翻译为值域）
`IQR(x)`                             |  求`x`的四分位差（interquartile range）
`cor(x, y)`                          |  求`x`、`y`之间的相关系数  
`sd(x)`                              |  求标准差 （standard deviation，分母为 $n - 1$ ）
`var(x)`                             |  求方差（variance，分母为 $n - 1$）
`quantile(x, probs)`                 |  求分位数，`probs`为一个由[0,1]之间的概率值向量，如求`x`的30%和84%分位点为`quantile(x, c(0.3, 0.84))`
`scale(x, center = , scale = )`      |  对`x`进行中心化（减去平均数，`center = T`）或标准化（减去平均数再除以标准差，`scale = T`）

### 向量操纵

函数                                 |  功能
-------------------------------------|-----------------------------------------------------
`length(x)`                          |  求`x`的长度，如`length(c(1, 2, 3, 4))`返回值为`4`
`seq(from, to, by)`                  |  以`by`为步长生成从`from`到`to`的等差数列，如`seq(1, 10, 2)`返回值为`1 3 5 7 9` 
`rep(x, times = , each = , len = )`  |  重复`x`，`times`控制向量整体的重复次数，`each`控制每个元素的重复次数，`len`控制输出长度
`gl(n, k, labels = c() )`            |  产生因子向量，其中`n`表示因子的水平数，`k`表示每一水平下的取值次数，`labels = c()`用于输入水平取值的标签
`cut(x, n)`                          |  将连续型向量`x`分割为有着 $n$ 个水平的因子
`pretty(x, n)`                       |  创建美观分割点。选取 $n + 1$ 个等距分割点，将连续型向量`x`分割为 $n$ 个区间
`colSums(x)`                         |  求列总和，`x`为数值型矩阵、数组或数据框
`rowSums(x)`                         |  求行总和，`x`为数值型矩阵、数组或数据框
`colMeans(x)`                        |  求列均值，`x`为数值型矩阵、数组或数据框
`rowMeans(x)`                        |  求行均值，`x`为数值型矩阵、数组或数据框
`duplicated(x)`                      |  求向量或数据框的重复元素中的下标较小元素，并返回逻辑向量指出哪些元素重复
`unique(x)`                          |  删除`x`向量、数据框或数组中重复的元素，如`unique(c(1, 2, 2, 4, 5, 4, 6))`返回值为`1 2 4 5 6`
`table()`                            |  制作频次表（frequency table）或列联表（contingency table）
`rev(x)`                             |  对`x`中元素取逆序（reverse order）排列
`sort(x, decreasing = FALSE)`        |  对向量`x`进行升序排序，若设置`decreasing = TRUE`则进行降序排序
`order(x)`                           |  将`x`中元素下标按升序排列，如`order(c(20, 4, 13, 9))`返回值为`2 4 3 1`
`rank(x)`                            |  求秩（即排序位置），返回该向量中对应元素的大小位置排序，如`rank(c(20, 4, 13, 9))`返回值为`4 1 3 2`

需要说明的是`rank()`与`order()`这两个“排序”函数之间的区别。`rank()`给出了向量的排序位置（默认从小到大），这在直觉上是易于理解的。然而`order()`函数的结果却有些晦涩。试看下例。

```{r}
x <- c(20, 4, 13, 9)
rank(x)
```

至此并未有任何难解之处。麻烦在于理解`order()`的结果。

```{r}
order(x)
```

如何理解结果中`2 4 3 1`与原向量`c(20, 4, 13, 9)`之间的对应关系呢？实际上，这里的`2 4 3 1`是指，若对原向量进行升序排序，则该向量中的第2个元素（4）应当排在第1位，第4个元素（9）应当排在第2位，第3个元素（13）应当排在第3位，第1个元素（20）应当排在第4位。也就是说，这里的`2 4 3 1`实际是标志着原向量中各元素的位置下标，并将其重新升序排列，表示第2个元素应当排在第1位，依次类推。这正是`order()`这一函数的本意：给出重新排列后，各元素应当具有的大小顺序关系，只是这一顺序以原元素的位置下标予以体现。

比较如下两行命令，可更好地理解上面的说明。

```{r}
x[order(x)]
sort(x)
```
两者结果相同。其实`x[order(x)]`的意思正是把`x`向量中的元素，按照其“应当出现的位置”（由`order(x)`提供）重新排列。

再看其在数据框的变量排序中的应用。

```{r}
attach(mtcars) # 绑定数据框
mtcars[order(cyl), ]
```

这里的`order(cyl)`出现在行标的位置，意指让`cyl`（气缸数）最少者排在最前的行，逗号后留空表示所有其他列依前面的排序规则重新排序。

若要对多列取值进行排序，在`order()`中用逗号隔开即可，按括号中各列的先后次序进行排序。例如

```{r}
mtcars[order(cyl, -mpg), ]
detach(mtcars) # 解绑数据框
```

另外，`colSums (x)`、`rowSums (x)`、`colMeans(x)`和`rowMeans(x)`这四个函数其实是一类，都是 R 向量化运算的一种体现，用于分行或分列求和或求均值。默认不移除缺失值，如需移除需对`na.rm = `参数加以设定（以`colSums()`为例）

```r
colSums(x, na.rm = TRUE)
```

### 集合与逻辑

函数                                    |  功能
----------------------------------------|---------------------------
`intersect(x, y)`                       |  求`x`、`y`的交集（intersect）
`union(x, y)`                           |  求`x`、`y`的并集（union） 
`setdiff(x, y)`                         |  求`x`、`y`的差集（`x - y`，要求`y`是`x`的真子集，否则结果显示零个元素）
`setequal(x, y)`                        |  判定`x`、`y`是否相等（所有元素相同）
`all()`                                 |  给定一组逻辑向量所有值是否为真
`any()`                                 |  给定一组逻辑向量是否有一个值为真
`which()`                               |  返回`x`为真值的位置下标，如`which(c(T, F, T))`返回值为`1 3`
`ifelse(cond, statement1, statement2)`  |  若`cond`（条件）为真，则执行第一个语句；若`cond`为假，则执行第二个语句
`%in%`                                  |  匹配函数，详见说明
`match(x, table)`                       |  匹配函数，详见说明

`%in%`是常用的匹配函数，其结果返回一组逻辑向量，`x %in% y`给出向量`x`中每个元素是否为向量`y`中的元素。`x`与`y`的长度不一定相同，但`x %in% y`结果向量的长度与`x`的长度相同。例如

```{r}
x <- c(1:5)
y <- c(3:8)
x %in% y
y %in% x
```

`match()`的功能与此类似，但较不易理解。`match(x, y)`返回值为x各元素与y中相匹配的元素在y中的位置信息，没有相匹配的元素则返回NA。试看下例

```{r}
x <- c(5, 2, 1, 4, 3)
y <- c(3:8)
match(x, y)
```

通常使用`%in%`函数更为便捷，也较易理解。


### 概率函数

此处所谓的概率函数并不是严格的定义，而泛指涉及概率计算的函数。R 中的概率函数包括四大类型：
  
- `d`：密度函数（density function），给出密度函数值
- `p`：（累积）分布函数（distribution function），给出 $P(X \leqslant q )$ 的概率值，此处的`p`即表示概率（probability）
- `q`：分位数函数（quantile function），给出 $P(X \leqslant q) = p$的`q`值（分位数值），此时 $p$ 需要指定
- `r`：生成服从特定分布的随机数（random digits）

这四个字母加上不同的分布名称的缩写，即可完成相应的功能。例如

函数                        |  功能
----------------------------|-------------------
`dnorm(x, mean = , sd = )`  |  给出指定正态分布的对应密度函数值
`pnorm(q, mean = , sd = )`  |  给出指定正态分布中 $P(X \leqslant q)$ 的概率值
`qnorm(p, mean = , sd = )`  |  给出指定正态分布中使得 $P(X \leqslant q) = p$  的$q$值
`rnorm(n, mean = , sd = )`  |  给出 $n$ 个服从指定参数的正态分布随机数


下面以正态分布为例进行说明。

设$X \sim N(10, 2)$，求：

- $P(X \leqslant 12)$
- $P(X \geqslant 13)$
- $P(9 < X < 12.5)$
- $P(X \geqslant q = 0.95)$，求 $q$

解法如下：

- `pnorm(12, 10, 2)`，给出$P(X \leqslant 12)$的值，即`0.8413447`
- `1 - pnorm(13, 10, 2)`或`pnorm(13, 10, 2, lower.tail = FALSE)`，给出$P(X \geqslant 13)$ 的值，即`0.0668072`
- `pnorm(12.5, 10, 2) - pnorm(9, 10, 2)` 给出$P(9 < X < 12.5)$的值，即`0.5858127`
- `qnorm(0.95, 10, 2)`给出符合$P(X \leqslant q = 0.95)$的 $q$ 值，即`13.28971`

此处需要说明两点：

- 对于连续型分布，从概率计算角度而言$P(a \leqslant X \leqslant b)$中的等于号是否取到不必计较
- `mean`和`sd`的字样可省略，直接输入数值；若在上述相关命令中不指定`mean`与`sd`，则默认为标准正态分布，即$N(0, 1)$

由此，生成10个服从标准正态随机数的命令如下

```{r}
rnorm(10, 0, 1) # 或简单地 rnorm(10)
```

由于未设定随机数种子数，不同人演示的结果可能各不相同。

其他分布的概率计算与正态分布相似，只是在`d`、`p`、`q`、`r`后代入各自分布的名称即可。当然，命令中的具体参数需要参阅其帮助文档。


R 中概率分布名称的缩写如下

分布名称                                      |   缩写
----------------------------------------------|--------------
二项分布（binomial distribution）             |  `binom`
多项分布（multinomial distribution）          |  `multinom`
正态分布（normal distribution）               |  `norm`
几何分布（geometric distribution）            |  `geom`
超几何分布（hypergeometric distribution）     |  `hyper`
负二项分布（negative binomial distribution）  |  `nbinom`
对数正太分布（log normal distribution）       |  `lnorm`
卡方分布（chi-squared distribution）          |  `chisq`
*F* 分布                                      |  `f`
指数分布（exponential distribution）          |  `exp`
Gamma 分布                                    |  `gamma`
Beta 分布                                     |  `beta`
泊松分布（poisson distribution）              |  `pois`
*t* 分布（student distribution）              |  `t`
均匀分布（uniform distribution）              |  `unif`
Logistic 分布                                 |  `logis`  
柯西分布（Cauchy distribution）               |  `cauchy`
Wilcoxon 符号秩分布                           |  `signrank`
Wilcoxon 秩和分布                             |  `wilcox`
Weibull 分布                                  |  `wilbull`

以下再以二项分布为例进行说明。若有随机变量 $X \sim B(10, 0.2)$，则

- `rbinom(5, 10, 0.2)`输出5个服从 $B(10 ,0.2)$ 分布的随机数，其格式为`rbinom(n, size, prob)`，其中`n`表示输出随机数个数，`size`表示二项试验的总次数，`prob`表示每次试验成功的概率。
- `dbinom(4, 10, 0.2)`求出 $P(X = 4)$ 的概率值，即 `0.08808038`
- `pbinom(4, 10, 0.2)`求出 $P(X \leqslant 4)$ 的概率值，即`0.9672065`
- `qbinom(0.9672065, 10, 0.2)`表示求出 $P(X \leqslant q) = 0.9672065$ 的$q$值，即`4`
  
其余不再一一举例。

### 数据对象操纵

函数                          |  功能
------------------------------|-------------------
`length()`                    |  显示对象中元素的个数
`dim()`                       |  显示某个对象的维度
`str()`                       |  显示某个对象的结构
`class()`                     |  显示某个对象的类
`mode()`                      |  显示某个对象的模式
`names()`                     |  显示某对象中各元素的名称
`head()`                      |  列出某个对象的开始部分
`tail()`                      |  列出某个对象的最后部分
`ls()`                        |  显示当前工作空间中的所有对象
`rm(A, B, ...)`               |  删除一个或多个对象，语句`rm(list = ls())`将删除当前工作环境中的所有对象
`newobject <- edit(object)`   |  编辑对象并另存为新对象
`fix(object)`                 |  直接编辑对象，关闭后自动保存改动至原对象
`cbind()`                     |  根据列进行合并，要求所有的行数相等
`rbind()`                     |  根据行进行合并，要求所有的列数相等
`rownames()`                  |  修改行数据框行变量名
`colnames()`                  |  修改行数据框列变量名
`exists()`                    |  寻找给定名称的 R 对象


### 类型判断与转换

 判断             |  转换
------------------|--------------
`is.numeric()`    |  `as.numeric()`
`is.character()`  |  `as.character()`
`is.vector()`     |  `as.vector()`
`is.matrix()`     |  `as.matrix()`
`is.data.frame()` |  `as.data.frame()`
`is.factor()`     |  `as.factor()`
`is.logical()`    |  `as.logical()`

### 日期格式

 符号      | 含义                  | 示例
-----------|-----------------------|----------
`%d`       | 数字日期（0~31）      | 01~31 
`%a`       | 缩写星期名            | Mon
`%A`       | 非缩写星期名          | Monday
`%m`       | 月份（00~12）         | 00~12
`%b`       | 缩写月份              | Jan
`%B`       | 非缩写月份            | January
`%y`       | 两位数年份            | 07
`%Y`       | 四位数年份            | 2017

### 获取帮助

函数                                |  功能
------------------------------------|-------------------
`help(package = "package_name")`    |  输出某个包的简短描述以及包中所有函数与数据集列表
`help.start()`                      |  打开帮助文档首页
`help("xxx")`或`?xxx`               |  精确搜索，查看函数`xxx`的帮助文档（引号可以省略）
`help.search("xxx")`或`??xxx`       |  模糊搜索，以`xxx`为关键字搜索本地帮助文档
`example("xxx")`                    |  函数`xxx`的使用示例（引号可以省略）
`RSiteSearch("xxx")`                |  以`xxx`为关键字搜索在线文档和邮件列表存档
`apropos("xxx", mode = "function")` |  列出名称中含有`xxx`的所有可用函数
`data()`                            |  列出当前已加载包中所含的所有可用示例数据集
`vignette()`                        |  列出当前已安装包中所有可用的 vignette 文档，即扼要说明文档
`vignette("xxx")`                   |  以`xxx`为主题显示指定的 vignette 文档


### 工作目录与工作空间

函数                                 |  功能
-------------------------------------|-------------------
`getwd()`                            |  显示当前的工作目录
`setwd()`                            |  设定当前工作路径
`install.packages()`                 |  下载和安装 R 包
`update.packages()`                  |  更新已经安装的 R 包
`load("")`                           |  读取 R 格式数据（`.RData`）
`library()`                          |  载入已安装的包
`require()`                          |  将会根据包的存在与否返回`TRUE`或`FALSE`
`options()`                          |  显示或设置当前选项
`save()`                             |  保存指定对象
`q()`                                |  退出 R 
`dir()`                              |  返回文件名、目录名或文件夹名称 
`dirname()`                          |  返回路径中的目录部分
`dir.creat()`                        |  建立新目录
`history()`                          |  显示最近使用过的命令（默认值为25）
`savehistory()`                      |  保存命令历史文件（默认为`.Rhistory`）
`loadhistory()`                      |  载入一个命令历史文件（默认为`.Rhistory`）    
`save.image()`                       |  保存工作空间到文件中（默认值为`.RData`）
`source()`                           |  执行包含在文件中的 R 语句命令集合
`download.file()`                    |  下载文件
`file.path()`                        |  拼接目录字符串
`file.choose()`                      |  返回可选择文件位置的对话框
`file.copy()`                        |  复制单个文件
`file.create()`                      |  返回创建的文件的句柄后，状态就是打开状态
`file.remove()`                      |  删除指定文件
`file.rename()`                      |  文件或目录的重命名
`file.exists()`                      |  检查文件或目录是否存在
`file.info()`                        |  查看当前或指定目录完整信息
`.libPaths()`                        |  能够显示库所在的位置


此处附加说明两个常用函数：`options()`和`require()`。

`options()`函数常用来设置 R 的结果显示，涉及的参数、功能及默认值很多，可用`?options`查看。这里只说明两个参数

- `digits =`，用于控制小数位
- `scipen = `，用于控制科学计数法（scientific notation）显示

先看`digits = `的使用。

```{r}
x <- rnorm(5)
x
options(digits = 3)
x
```

再看`scipen = `的使用。其中`sci`表示科学计数法，`pen`表示penalty，通常译为惩罚因子，实质就是一个用于设定显示方式的临界值。`=`号后输入整数，超过此整数位则使用科学计数法，否则呈现所有数位。

```{r}
factorial(20)
options(scipen = 100)
factorial(20)
```


`require()`函数也常用来加载 R 包，相当于`library()`。两者的不同在于，`require()`某个包之后，若发现未安装此包，则 R 会出现警告信息（warning message），但 R 仍将继续运行后续命令；而`library()`了某个不存在的包后，则会出现错误信息（error message），R 将停止执行后续命令。

例如

```{r}
require(abcd)
```

试比较

```r
library(abcd)
```

```r
## Error in library(abc) : there is no package called ‘abc’
```


但有一种情况中，使用`require()`是比较方便的。

```{r}
if (!require(dplyr)) install.packages("dplyr")
```

其含义是：若加载 **dplyr** 包时发现没有安装此包，则先行安装再加载。这对于不确定是否安装该包的用户进行代码演示时较为方便。


### `apply`族函数

在 R 中，`apply`族函数`apply()`、`lapply()`、`sapply()`、`tapply()`等形式。前三者可将某一函数“应用”（apply）于矩阵、数组、数据框和列表的任一维度，`tapply()`则经常作用于因子数据以进行分组统计。

#### `apply()`
  
`apply()`函数只能作用于数组或矩阵，要求所有数据具有相同类型。使用语法为

```r
apply(x, MARGIN, FUN)
```
其中
- `x`是数据对象
- `MARGIN`是维度下标，`1`表示行，`2`表示列，`c(1, 2)`表示行与列
- `FUN`是指定函数

这里采用虚拟数据进行演示。

```{r}
set.seed(1234)
x <- matrix(rnorm(12), nrow = 3)
x <- round(x, 2)
x
```

上述命令中的`set.seed(1234)`表示设定随机数，`rnorm(12)`表示生成12个服从标准正态分布的随机数，并将这12个随机数按列填充为 $ 3 \times 4$ 的矩阵。下面使用`apply()`计算相关统计量。

```{r}
apply(x, 1, mean)
apply(x, 2, mean)
apply(x, 1, sd)
apply(x, 2, sd)
```

以上分别求矩阵各行均值、各列均值、合行标准差、各列标准差。当然，若要求行或列均值，使用`colMeans()`或`rowMeans()`速度更快（针对大型数据而言）。

#### `lapply()`与`sapply()`

`apply()`只作用于矩阵或数组，要求数据类型相同。`lapply()`与`sapply()`函数则作用于向量或列表。使用语法为

```r
lapply(x, FUN)
```
 
其中

- `x`是列表型数据，其他类型对象可通过函数`as.list()`转换成列表
- `fun`表示任意函数

下面通过 R 自带的`mtcars`数据进行示例。假设要使用 Shapiro 法对该数据中的`mpg`、`disp`、`wt`、`drat`进行正态性检验（命令为`shapiro.test()`），自然可以对这四个变量执行4次`shapiro.test()`，但命令重复较多。


```r
shapiro.test(mtcars$mpg)
shapiro.test(mtcars$disp)
shapiro.test(mtcars$wt)
shapiro.test(mtcars$drat)
```


可通过以下方式简化命令

```{r}
x <- mtcars[, c("mpg", "disp", "wt", "drat")]
lapply(x, shapiro.test)
```


`sapply()`与`lapply()`多数情况下只是结果的显示方式不同。试比较

```{r}
x <- mtcars[, c("mpg", "disp", "wt", "drat")]
sapply(x, shapiro.test)
```

注意这里的`x`本质上是一个数据框，但`shapiro.test()`函数实质上作用于该数据框中的列（向量）。


#### `tapply()`

`tapply()`函数常于对数据进行分组统计。使用语法为

```r
tapply(x, INDEX, FUN)
```
其中

- `x`为向量
- `INDEX`为长度与`x`相同的因子列表，通常填入因子型变量的名称即可
- `FUN`为函数


例如，在`mtcars`数据中，要分气缸数（`cyl`）统计不同气缸数汽车的耗油量（`mpg`），可使用如下命令

```{r}
tapply(mtcars$mpg, mtcars$cyl, mean)
```

其他`apply()`族函数还包括`mapply()`、`vapply()`等，使用相对较少，这里不再细述。
