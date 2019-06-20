
## **tidyr** 简介

tidyr主要提供了一个类似于EXCEL中数据透视表的功能
tidyr包主要涉及:
1)缺失值的简单补齐 
2)长形表变宽形表与宽形表变长形表
　　- `gather()`:把宽度较大的数据转换成一个更长的形式，它类比于从reshape2包中融合函数的功能
　　- `spread()`:把长的数据转换成一个更宽的形式，它类比于从reshape2包中铸造函数的功能。
　　`gather()`相反的是`spread()`，前者将不同的列堆叠起来，后者将同一列分开
3)列分割与列合并
　　- `separate()`:将一列按分隔符分割为多列
　　- `unite()`:将多列按指定分隔符合并为一列
　　- `extract()`:通过正则表达式提取一列并转化为新的列
以下均通过实例说明各函数的用法。请确保已通过以下命令加载 **dplyr** 包。

```{r}
library(tidyr)
```

当然，也可通过`library(tidyverse)`调用此包。

## gather()
gather函数类似于Excel中的数据透视的功能，能把一个变量名含有变量的二维表转换成一个规范的二维表，实现宽表转长表。
```r
gather(data, key = "key", value = "value", ..., na.rm = FALSE, convert = FALSE, factor_key = FALSE)
```
其中，

- data:数据类型为数据框
- key, value:变量标签，即新转换成的二维表的表头，通常为字符串或符号
- ...:是选中要转置的列，可以指定哪些列聚到同一列中，此参数不写则默认为全部转置
- na.rm:na.rm = TRUE, 将会在新表中去除原表中的缺失值(NA)
- convert:convert = FALSE，自动在key列运行type.convert()，这适用于列的类型是数值型、整数型或是逻辑型

```{r}
stocks <- data.frame(
  time = as.Date('2009-01-01') + 0:9,
  X = rnorm(10, 0, 1),
  Y = rnorm(10, 0, 2),
  Z = rnorm(10, 0, 4)
                    )
stocks
stocksm <- stocks %>% gather(stock, price, -time)
```

```{r}
widedata <- data.frame(person=c('Alex','Bob','Cathy'),grade=c(2,3,4),score=c(78,89,88))
widedata
longdata <- gather(widedata, variable, value,-person)
longdata
```

通过以上两个例子，可以看到，`gather()`函数将一个宽数据框转化为了一个长数据框。

## spread()
为了满足建模或绘图的要求，往往需要将长形表转换为宽形表，或将宽形表变为长形表，使用spread()函数实现长表转宽表
```r
spread(data, key, value, fill = NA, convert = FALSE, drop = TRUE)
```
- data:为需要转换的长形表
- key:需要将变量值拓展为字段的变量
- value:需要分散的值
- fill:对于缺失值，可将fill的值赋值给被转型后的缺失值

```{r}
stocksm <- stocks %>% gather(stock, price, -time)
stocksm %>% spread(stock, price)
```

```{r}
name<-rep(c("Sally","Jeff","Roger","Karen","Brain"),c(2,2,2,2,2))
test<-rep(c("midterm","final"),5)
class1<-c("A","C",NA,NA,NA,NA,NA,NA,"B","B")
class2<-c(NA,NA,"D","E","C","A",NA,NA,NA,NA)
class3<-c("B","C",NA,NA,NA,NA,"C","C",NA,NA)
class4<-c(NA,NA,"A","C",NA,NA,"A","A",NA,NA)
class5<-c(NA,NA,NA,NA,"B","A",NA,NA,"A","C")
stu3<-data.frame(name,test,class1,class2,class3,class4,class5) #总共5门课，每个学生选两门，列出期中、期末成绩。显然，原表是不整洁的数据，表头中含有变量（class1-5），所以先用gather函数。注意，这里面有很多缺失值，就可以用到上面所讲的na.rm=TRUE参数，自动去除有缺失值的记录（一条记录就是一行）
gather(stu3, class, grade, class1:class5, na.rm = TRUE)
gather(stu3, class, grade, class1:class5) #不写 na.rm = TRUE的话，结果是这样
stu3_new<-gather(stu3, class, grade, class1:class5, na.rm = TRUE)
spread(stu3_new,test,grade) #用spread函数将test列分来成midterm和final两列，这两列的值是选的两门课的成绩。
```
通过以上两个例子，可以看到，`gather()`函数将一个长数据框转化为了一个宽数据框。

## unite()
`seperate()`函数的反向操作，多列合并为一列。
```r
unite(data, col, ..., sep = "_", remove = TRUE)
```
- data:为数据框
- col:被组合的新列名称
- ...:指定哪些列需要被组合
- sep:组合列之间的连接符，默认为下划线
- remove:是否删除被组合的列

```{r}
widedata <- data.frame(person=c('Alex','Bob','Cathy'),grade=c(2,3,4),score=c(78,89,88))
wideunite<-unite(widedata, information, person, grade, score, sep= "-")
wideunite
```

```{r}
mtcars %>% unite(vs_am, vs, am)
mtcars %>% unite_("vs_am", c("vs","am"))
```

## separate()
`separate()`负责分割数据，可将一列拆分为多列，把一个变量中就包含两个变量的数据分来，一般用于日志数据或日期时间型数据的拆分.
```r
separate(data, col, into, sep = "[^[:alnum:]]+", remove =TRUE, convert = FALSE, extra = "warn", fill = "warn", ...)
```
- data:要处理的数据框；
- col:需要被拆分的列；
- into:新建的列名，为字符串向量；
- sep:正则表达式，被拆分的分隔符；
- remove:是否删除被分割的列
- extra:在字符串过多的情况下处理；
- fill:在字符串过少的情况下处理。

```{r}
widesep <- separate(wideunite, information,c("person","grade","score"), sep = "-")
widesep
```
可见`separate()`函数和`unite()`函数的功能相反

```{r}
library(tidyr)
df <- data.frame(x = c(NA, "a.b", "a.d","b.c"))
df
df %>% separate(x, c("A", "B"))
df <- data.frame(x = c("a", "a b", "a b c", NA))
df
df %>% separate(x, c("a", "b"), extra = "merge", fill = "left")
df <- data.frame(x = c("x: 123", "y: error: 7"))
df
df %>% separate(x, c("key", "value"), sep = ":", extra = "merge")
```

## extract()
`extract()`负责提取一列并转化为新列。给定用来一个确定组的正则表达式，`extract()`将每个组转换为新列。如果组不匹配，或组的值输入为NA，则输出将为NA。
```
extract(data, col, into, regex = "([[:alnum:]]+)", remove = TRUE,
  convert = FALSE, ...)
```
- data:数据框
- col:列的名称或位置。
- into:新变量名称
- regex:正则表达式
- remove:是否将输入的列从结果中删除
- convert:如果为TRUE，则在新列上运行`type.convert()`和`as.is = TRUE`。这在构成列的元素是整数、数字或逻辑时很有用。


```{R}
df <- data.frame(x = c(NA, "a-b", "a-d", "b-c", "d-e"))
df
df %>% extract(x, "A")
df %>% extract(x, c("A", "B"), "([[:alnum:]]+)-([[:alnum:]]+)")
df %>% extract(x, c("A", "B"), "([a-d]+)-([a-d]+)")
```

