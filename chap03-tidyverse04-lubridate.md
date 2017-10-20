
## **lubridate** 简介

**lubridate** 包是 Hadley 开发的用于高效处理时间数据的 R 包。

### 关于时区、UTC 和 GMT 的说明

为克服国际时间的混乱，1884年的国际经度会议规定，以英国格林尼治（Greenwich）天文台的经线为零度经线（即本初子午线），将全球划分为24个时区，每个时区都有自己的本地时间，同时提倡使用标准时间，即格林尼治标准时间，简称 GMT（Greenwich Mean Time）。理论上讲，格林尼治标准时间的正午是指当太阳横穿格林尼治子午线时的时间。但由于地球自转并不完全规则，故这一时间与实际的太阳时可能差到16分钟。为调整误差，自1924年2月5日开始，格林尼治天文台每隔一小时会向全世界发放调时信息。为进一步协调时间误差，1972年1月1日，精度更高的协调世界时（Universal Time Coordinated，简称 UTC）成为新的世界标准时间，广泛应用于国际无线电通信场合。但为了方便，在不需要精确到秒的情况下，通常也将 GMT 和 UTC 视为等同。通常所谓的 GMT 手表，仅指显示两个或两个以上时区时间的手表，并不涉及时间确定方式。

时区编码通常采用`国家名（或大洲名）/城市名`的方式列示，如`America/Chicago`、`Asia/Tokyo`。中国采用北京所在地东八区的时间为全国统一使用时间，称为北京时间，或称中国标准时间（China Standard Time ，简称 CST）。北京时区是东八区，领先 UTC 八个小时，在电子邮件信头的 Date 域记为+0800。

但在协调世界时的国际时区代码中，并不使用`Asia/Beijing`作为标记，而使用`Asia/Shanghai`。这也是 **lubridate** 包中`tz = `参数中使用北京时间的标记方式。

### 日期格式转化

```r
library(lubridate)
raw_time1 <- "2017/9/12  12:47:28"
```

在`raw_time1`中，时间的显示方式为“年/月/日 时：分：秒”。其中，年、月、日之间用`/`分隔，日与时之间用空格分隔，时、分、秒之间用`:`分隔。这会给数据处理带来不便。**lubridate** 包的一大优势就在于它可自动识别多种分隔符，并按人类自然理解的方式进行操纵。下面使用`ymd_hms()`函数进行格式转化，其中`tz`表示设置时区（time zone）。

```r
r_time1 <- ymd_hms(raw_time1, tz = "Asia/Shanghai")
r_time1
```
此时数据已转为 R 中的标准日期格式，`CST`表示中国标准时间（即北京时间）。


若时间中只包含日期、时、分的信息，可使用`ymd_hm()`函数。

```r
raw_time2 <- "20170412 12:45"
r_time2 <- ymd_hm(raw_time2, tz = "Asia/Shanghai")
```

若包含日期与时的信息，则可用`ymd_h()`函数。这里不再举例。

使用`ymd()`或`ydm()`可将“年月日”或“年日月”日期格式转为 R 中的标准格式。
```r
raw_time3 <- "20170405"
ymd(raw_time3)
ydm(raw_time3)
```
类似地，`dmy()`和`mdy()`也可转化相应的日期格式。


### 时刻信息提取

对符合指定格式的日期数据，**lubridate** 包中的函数可方便地提取日期（年月日）、年、月、日、时、分、秒、时区、周几等信息，这主要涉及`date()`、`year()`、`month()`、`day()`、`hour()`、`minute()`、`second()`、`tz()`和`wday()`等函数。

```r
date(r_time1)
year(r_time1)
month(r_time1)
day(r_time1)
hour(r_time1)
minute(r_time1)
second(r_time1)
tz(r_time1)
```

使用`wday()`可给出指定的日子是周几，周日至周六依次编码为整数1-7。也可设置参数`label = TRUE`显示 Sunday 至 Saturday 的字母缩写。

```r
wday(r_time1)
wday(r_time1, label = TRUE)
```

使用`with_tz()`可将当前时间转换为另一时区的时间，使用`force_tz()`可将当前数据的时区强制替换为另一时区。

```r
with_tz(r_time1, "America/Chicago")
force_tz(r_time1, "America/Chicago")
```

使用`leap_year()`可判断该时间是否为闰年。

```r
leap_year(r_time1)
r_time4 <- ymd(20120923)
leap_year(r_time4)
```


### 时段数据

**lubridate** 包内的函数可处理三种类型的时段数据，他们分别是 Interval 型、Duration 型和 Period 型。

+ Interval: 由两个时间点构成。
+ Duration: 以秒为单位，不考虑闰年闰秒。
+ Period: 时钟周期，单位包含年（y）、月（m）、日（d）、时（H）、分（M）和秒（S），考虑闰年闰秒。

#### Interval

可通过两种方式创建 Interval 数据。

```{r}
itv_time1 <- interval(r_time2,r_time1)
itv_time1
```
```{r}
r_time3 <- ymd(raw_time3, tz = "Asia/Shanghai")
r_time5 <- ymd(20170813, tz = "Asia/Shanghai")
itv_time2 <- r_time3 %--% r_time5
itv_time2
```

使用`int_overlaps()`可判断两时段数据是否有重合，使用`%witnin%`判断某时段是否在另一时段当中。

```{r}
int_overlaps(itv_time1, itv_time2)
```
```{r}
itv_time1 %within% itv_time2
```

之后可使用`setdiff()`将`itv_time1`中未与`itv_time2`重合的部分识别出来。

```{r}
setdiff(itv_time1,itv_time2)
```

#### Duration & Period

Duration 和 Period 是两种更普遍的时段数据类型，记录的不是起止时间，而是持续时长，试通过以下命令观察二者差异。

```{r}
minutes(4)
dminutes(4)
```

与 period 有关的函数通常以时间单位的复数形式命名，如：`minutes()`、`years()`;与 duration 有关的函数通常在对应的 period 函数前加 d，如：`dminutes()`、`dyears()`。

```{r}
leap_year(2017)
ymd(20170101) + years(1)
ymd(20170101) + dyears(1)
leap_year(2016)
ymd(20160101) + years(1)
ymd(20160101) + dyears(1)
```

对 duration 类型来说，闰年依然是365天。

可以利用加减除的运算完成一些其他工作，例如列举`r_time1`后几个月的这一天。 

```{r}
r_time1 + months(0:4)
```

以及判断这五个时间是否都包含在时段`itv_time1`中。

```{r}
(r_time1 + months(0:4)) %within% itv_time1
```

或者`itv_time2`一共有几天、几分钟？或是几个五分钟？

```{r}
itv_time2 / ddays(1)
itv_time2 / dminutes(1)
itv_time2 / dminutes(5)
```

由于除法求月数时余数没有太多意义，故采用整除、除余运算，整除运算符：`%/%`，除余运算符：`%%`。

```{r}
itv_time2 / months(1)
itv_time2 %/% months(1)
itv_time2 %% months(1)
```

命令`itv_time2 %% months(1)`返回的是 interval 数据，可用`as.period()`或`as.duration()`转换成指定类型。

```{r}
as.period(itv_time2 %% months(1))
as.duration(itv_time2 %% months(1))
```

**注意**：加减运算后会出现无效的日期，如1月30日加一个月后的2月30日，此时 R 会返回 `NA`。

```{r}
ymd(20170130) + months(1)
```




### 以下

（老师对不起！后一段程序我实在是读不懂，没办法针对那个进行补充，只根据包的介绍整理了一些内容码了上来。）

```r
library(readxl)
library(dplyr)
library(tibble)
library(lubridate)
time_data <- read_excel("PDSurveyBasic.xlsx") %>% select(time1) %>% as_tibble() 
head(time_data)
```


```r, eval=FALSE
library(readxl)
library(tibble)
library(tidyverse)
library(stringr)
PDSurveyBasic <- read_excel("PDSurveyBasic.xlsx")
time2 <- PDSurveyBasic$time2
ip.location <- str_extract(PDSurveyBasic$ip, "(?<=\\().*(?=\\))") %>% 
  str_split_fixed("-", n = 2) %>%
  as_tibble %>%
  transmute(province = .[[1]], city = .[[2]]) %>% 
  group_by(city) %>% 
  summarise(n = n()) %>% 
  arrange(desc(n))
```



