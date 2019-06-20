# **lubridate** 简介
在 R 中处理日期-时间（date-time data）数据并没有想象中简单，例如，请尝试回答下面几个简单问题：

* 每年都有365天么？
* 每天都有24小时么？
* 每分钟都有60秒么？

事实上，闰年有366天。而世界上许多地区使用夏令时（daylight savings time）制度，其中有些天只有23小时，有些天却有25小时。你或许不知道，由于地球自转在逐渐减速，每隔一段时间会增加闰秒（leap seconds），因此有时一分钟有61秒。

日期-时间数据的处理是困难的，出于协调两种物理现象（地球自转和绕太阳公转）的目的，我们需要处理月份、时区（time zones）和夏令时等一系列问题。

**lubridate** 包是 Garrett 和 Hadley 开发的用于高效处理日期-时间数据的 R 包，现在由 Vitalie Spinu 在维护。具体来说，**lubridate** 可以帮助用户完成：

* 解析日期-时间数据
* 提取和修改日期-时间数据中的成分（例如年、月、天、小时、分钟、秒）
* 对日期-时间和时间间隔（timespans）进行精确的计算
* 处理时区和夏令时（DST）

获取 **lubridate** 包，最简单的方法是直接安装整个 **tidyverse** 包：

```{r}
install.packages("tidyverse")
```

或者，仅仅只安装 **lubridate** 包：
```{r}
install.packages("lubridate")
```

如果要获取 Github 上的开发版：
```{r}
install.packages("devtools")
devtools::install_github("tidyverse/lubridate")
```

在介绍相关内容前，我们先加载 **lubridate** 包：
```{r}
library(lubridate)
```

***
## 解析日期-时间数据

让 R 来识别日期-时间数据本来是件棘手的事，但 **lubridate** 简化了这一过程。你只需要确定数据中年（year），月（month）,日（day）的出现顺序，然后以相同的顺序对 “y” ， “m” 和 “d” 进行排序，就可以得到解析该日期数据的 **lubridate** 函数。例如：
```{r}
mdy("05192019")
```
[1] "2019-05-19"
```{r}
dmy("19-05-2019")
```
[1] "2019-05-19"
```{r}
myd(c("05.2019.19", "04.2019.17"))
```
[1] "2019-05-19" "2019-04-17"

**lubridate** 的解析函数能自动识别各种日期格式和分隔符，这大大简化了对日期数据的解析工作。

如果你的数据中还包括时（hour），分（minute），秒（second）等时间信息，那么只需要在 **lubridate** 的日期解析函数中加入 “h” ， “m” ， “s” 即可，例如：
```{r}
mdy_hms("05.19.2019.12.30.00")
```
[1] "2019-05-19 12:30:00 UTC"
```{r}
ymd_h("2019/05/19 12")
```
[1] "2019-05-19 12:00:00 UTC"

`ymd_hms`可能是最常见的日期时间格式，如下：

```{r}
ymd_hms("2019-05-19 12:30:00")
```
[1] "2019-05-19 12:30:00 UTC"

***
## 提取和修改日期-时间数据中的成分

任何一个日期-时间数据都由不同成分组成，每一个成分都有其对应的值。例如，大多数日期-时间数据都包括年、月、日等成分及其对应数值。**lubridata** 提供了一系列便捷函数来提取日期-时间数据中的特定成分信息，包括 `date()` 、 `year()` 、 `month()` 、 `day()` 、 `hour()` 、 `minute()` 、 `second()` 、 `tz()` 和 `wday()` 等函数。例如，我们想保存当前的系统时间（下面为撰写该文本时的系统时间，请以实际系统时间为准）：

```{r}
date <- now()
date
```
[1] "2019-05-24 14:21:55 CST"


`now()` 函数可以提取当前系统时间，我们现在想提取系统时间中的各个成分：

```{r}
year(date)
```
[1] 2019
```{r}
month(date)
```
[1] 5
```{r}
day(date)
```
[1] 24
```{r}
hour(date)
```
[1] 14
```{r}
minute(date)
```
[1] 21
```{r}
second(date)
```
[1] 55.72506
```{r}
wday(date)
```
[1] 6

需要注意的是，在提取星期信息时，数字1代表周日，以此类推（例如上面系统时间的6代表周五）。我们还可以通过对参数 `label= ` 进行设置来决定是否输出文本信息，例如：
```{r}
wday(date, label = TRUE)
```
[1] 周五 

Levels: 周日 < 周一 < 周二 < 周三 < 周四 < 周五 < 周六

也可以使用这种方法提取月份的文本信息，例如：
```{r}
month(date, label = TRUE)
```
[1] 5月

Levels: 1月 < 2月 < 3月 < 4月 < 5月 < 6月 < 7月 < 8月 < 9月 < 10月 < 11月 < 12月

在提取日期-时间数据的具体成分后，我们还可以对其进行修改，例如：
```{r}
day(date) <- 5
date
```
[1] "2019-05-05 14:21:55 CST"

我们还可以进行一些稍微复杂的修改：
```{r}
dates <- ymd_hms("2019-01-01 01:00:00", "2019-05-19 01:30:00")
minute(dates) <- mean(minute(dates))
dates
```
[1] "2019-01-01 01:15:00 UTC" "2019-05-19 01:15:00 UTC"

如果我们设置的值超过了该成分的范围，其差值将会转入更高的成分，例如：
```{r}
day(date) <- 40
date
```
[1] "2019-06-09 14:21:55 CST"

如果想要一次性修改多个成分，或者想要创建一个修改后的副本：
```{r}
update(date, year = 2019, month = 7, day = 1)
```
[1] "2019-07-01 14:21:55 CST"

***
## 对日期-时间和时间间隔（timespans）进行精确的计算

**lubridate** 包中有四种与时间相关的对象类型，分别是即时（instants）、时间段（durations）、周期（periods）和区间（intervals）。其中时间段（durations）、周期（periods）和区间（intervals）都属于时间间隔（timespans）数据类型。

### 即时（instants）

instants 类型数据是一个时间点，例如2019年1月1日。每次使用 R 解析一个日期-时间数据，其实就是创建一个 instants 数据，我们可以使用 `is.instant()` 来判断对象是否为 instants 数据：
```{r}
is.instant(365)
```
[1] FALSE
```{r}
start_2019 <- ymd_hms("2019-01-01 12:00:00")
is.instant(start_2019)
```
[1] TRUE
```{r}
is.instant(today())
```
[1] TRUE

我们可以使用 `today()` 函数获取当天的日期数据。

### 时间段（durations）
当你使用两个 instants 相减时，你会得到一个 difftime 对象。例如你想知道一个1995年7月31日出生的人现在多大了，你可以：
```{r}
age <- today() - ymd(19950731)
age
```
Time difference of 8698 days

一个 difftime 对象记录了可能由秒、分钟、小时、日或周为单位的时间间隔，这使得 difftime 处理起来很费力，因此 **lubridate** 提供了一个只使用秒（second）作为单位的时间间隔对象，就是 duration。我们可以用 `as.duration()` 将 difftime 转化为 duration：
```{r}
as.duration(age)
```
[1] "751507200s (~23.81 years)"

duration 还附带一系列方便的构造函数：
```{r}
dseconds(15)
```
[1] "15s"
```{r}
dminutes(15)
```
[1] "900s (~15 minutes)"
```{r}
dhours(15)
```
[1] "54000s (~15 hours)"
```{r}
ddays(0:5)
```
[1] "0s"                "86400s (~1 days)"  "172800s (~2 days)" "259200s (~3 days)" "345600s (~4 days)"

[6] "432000s (~5 days)"
```{r}
dweeks(3)
```
[1] "1814400s (~3 weeks)"
```{r}
dyears(1)
```
[1] "31536000s (~52.14 weeks)"

需要注意的是，duration 基于固定的比率将不同时间单位转换为秒（一年365天，一天24小时，一小时60分钟，一分钟60秒）,因此 duration 并没有考虑闰年（366天）和夏令时（一天23小时）的情况，也无法对月（month）进行转换。

你可以对 duration 进行基本运算：
```{r}
2 * dyears(1)
```
[1] "63072000s (~2 years)"
```{r}
dyears(1) / 2
```
[1] "15768000s (~26.07 weeks)"
```{r}
dyears(1) + dweeks(12) - dhours(15)
```
[1] "38739600s (~1.23 years)"

你也可以在一个 instant 上使用 duration 进行加减运算：
```{r}
tomorrow <- today() + ddays(1)
last_year <- today() - dyears(1)
```

但是，由于 duration 没有考虑闰年和夏令时，有时结果会出人意料，例如：
```{r}
leapyear <- ymd(20161231)
leapyear - dyears(1)
```
[1] "2016-01-01"

从逻辑上，2016年12月31日的一年前应为2015年12月31日，但由于2016年为闰年（366天），因此在减去 `dyears(1)` (365天)后，输出日期为2016年1月1日。

我们可以使用 `leap_year()` 来判断日期-时间数据是否为闰年：
```{r}
leap_year(leapyear)
```
[1] TRUE

### 周期（periods）
虽然 duration 无法解决逻辑上的闰年和夏令时问题，但是 **lubridate** 中的 period 对象解决了这一问题。period 也是一个时间间隔对象，但是和 duration 不同的是，其不是由秒作为单位，而是使用人们更熟悉的时间概念（例如天、月）作为单位，因此其计算方式和结果更加直观。我们可以用 `as.period()` 将 difftime 转化为 period：
```{r}
as.period(age)
```
[1] "8698d 0H 0M 0S"

和 duration 一样，period 也有许多方便的构造函数：
```{r}
seconds(15)
```
[1] "15S"
```{r}
minutes(15)
```
[1] "15M 0S"
```{r}
hours(15)
```
[1] "15H 0M 0S"
```{r}
days(c(12, 24))
```
[1] "12d 0H 0M 0S" "24d 0H 0M 0S"
```{r}
weeks(1:3)
```
[1] "7d 0H 0M 0S"  "14d 0H 0M 0S" "21d 0H 0M 0S"
```{r}
months(13)
```
[1] "13m 0d 0H 0M 0S"
```{r}
years(1)
```
[1] "1y 0m 0d 0H 0M 0S"

和 duration 一样，你也可以对 period 进行基本运算：
```{r}
10 * (months(6) + days(40))
```
[1] "60m 400d 0H 0M 0S"

当然，你也可以在一个 instant 上使用 period 进行加减运算，并且得到更符合逻辑的结果，例如：
```{r}
leapyear - years(1)
```
[1] "2015-12-31"

而如果使用 duration，则：
```{r}
leapyear - dyears(1)
```
[1] "2016-01-01"

但有时使用 period 也会出现无效值，例如我们想要获得2016年12个月每个月最后一天的列表:
```{r}
ymd(20160131) + months(0:11)
```
[1] "2016-01-31" NA           "2016-03-31" NA           "2016-05-31" NA           "2016-07-31"

[8] "2016-08-31" NA           "2016-10-31" NA           "2016-12-31"

当遇到小月（没有31号）时，直接使用 period 计算会返回 `NA` 。这时我们可以使用 `%m+%` 这个特殊运算符，帮助我们追溯到每个月的最后一天，例如：
```{r}
ymd(20160131) %m+% months(0:11)
```
[1] "2016-01-31" "2016-02-29" "2016-03-31" "2016-04-30" "2016-05-31" "2016-06-30" "2016-07-31"

[8] "2016-08-31" "2016-09-30" "2016-10-31" "2016-11-30" "2016-12-31"

 `%m+%` 对应 `+` ，而 `%m-%` 对应 `-`，大家可以自己尝试。
 
 
### 区间（intervals）

intervals 数据是所有三种 timespans 数据类型中最简单的一种，是发生在两个特定 instants 之间的时间间隔。如果我们知道 interval 的起始时间和持续时间，就可以使用 `as.interval()` 函数将其转化为 interval:
```{r}
as.interval(age, ymd("1995-07-31"))
```

如果同时知道起止时间，你还可以通过两种方式直接创建 interval ：
```{r}
arrive <- ymd_hms("2016-01-01 12:00:00")
leave <- ymd_hms("2017-01-01 12:00:00")
span <- interval(arrive, leave)
span
```
[1] 2016-01-01 12:00:00 UTC--2017-01-01 12:00:00 UTC

或
```{r}
span <- arrive %--% leave
span
```
[1] 2016-01-01 12:00:00 UTC--2017-01-01 12:00:00 UTC

`int_start()` 函数可以帮我们获得一个 interval 的起始时间，而 `int_end()` 则输出结束时间：
```{r}
int_start(span)
```
[1] "2016-01-01 12:00:00 UTC"
```{r}
int_end(span)
```
[1] "2017-01-01 12:00:00 UTC"

`int_flip()` 函数可以反转 interval 的起止时间：
```{r}
int_flip(span)
```
[1] 2017-01-01 12:00:00 UTC--2016-01-01 12:00:00 UTC

`int_shift()` 函数可以将 interval 的起止时间同时向前或向后移动：
```{r}
int_shift(span, -days(1))
```
[1] 2015-12-31 12:00:00 UTC--2016-12-31 12:00:00 UTC

`int_overlaps()` 函数可以判断两个 intervals 之间是否有重叠：
```{r}
int_overlaps(span, ymd(20160901) %--% ymd(20170901))
```
[1] TRUE

如果起止时间存在逻辑矛盾（结束时间早于起始时间），`int_standardize()` 函数可以识别并反转矛盾的起止时间：
```{r}
leave %--% arrive
```
[1] 2017-01-01 12:00:00 UTC--2016-01-01 12:00:00 UTC

```{r}
int_standardize(leave %--% arrive)
```
[1] 2016-01-01 12:00:00 UTC--2017-01-01 12:00:00 UTC

对于正常的 interval，`int_standardize()` 会返回原值：
```{r}
int_standardize(span)
```
[1] 2016-01-01 12:00:00 UTC--2017-01-01 12:00:00 UTC

`int_aligns()` 函数可以判断两个 intervals 的起止时间是否有任意一个相同：
```{r}
int_aligns(span, "2016-01-01 12:00:00" %--% "2017-09-01 12:00:00")
```
[1] TRUE

如果有 n 个 instants，以相邻两个 instants 为起始时间， `int_diff()` 函数可以输出 n-1 个 intervals:
```{r}
dates <- now() + days(1:10)
int_diff(dates)
```

intervals 的长度永远不会是模糊的，因为我们知道何时开始。进一步，我们可以计算出任何一个 interval 中所包含的时间单位的精确长度，例如:
```{r}
span / days(1)
```
[1] 366

由于2016年为闰年，因此2016年有366天。我们已经知道 duration 中的一年是固定的365天，但是 period 中的一年呢？如下：
```{r}
years(1) / days(1)
```
estimate only: convert to intervals for accuracy
[1] 365.25

由于无法确定是否是闰年，period 中的一年是365.25天，且结果中会警告“仅估计:为精确起见，请转换为区间”。


### 日期的取整
和数字一样，日期-时间数据也会出现小数（例如36小时等于1.5天），**lubridate** 包提供了三种方法来对日期-时间数据进行取整，分别是`round_date()` 、`floor_date()` 和 `ceiling_date()`。

`round_date()` 函数采取四舍五入的取整策略：
```{r}
loveday <- ymd_hms("20190520 113329")
round_date(loveday, "day")
```
[1] "2019-05-20 UTC"

```{r}
round_date(loveday, "month")
```
[1] "2019-06-01 UTC"

`floor_date()` 函数采取向下取整策略（在时间上表现为向前取整）：
```{r}
floor_date(loveday, "month") 
```
[1] "2019-05-01 UTC"

`ceiling_date()` 函数采取向上取整策略（在时间上表现为向后取整）：
```{r}
ceiling_date(loveday, "month") 
```
[1] "2019-06-01 UTC"

借助 `ceiling_date()` 函数我们可以方便获取日期-时间数据所在月份的最后一天：
```{r}
ceiling_date(loveday, "month") - days(1)
```
[1] "2019-05-31 UTC"

还有一种经常碰到的情况，对日期-时间数据进行除法运算时的结果取整，例如我们需要在北京参加一个会议，会议日期如下：
```{r}
beijing <- "2019-05-01 08:30:00" %--% "2019-05-06 17:00:00"
beijing
```
[1] 2019-05-01 08:30:00 UTC--2019-05-06 17:00:00 UTC

我们想知道会议持续多少天（这里无论除以 `ddays(1)` 还是 `days(1)` 结果都一样）：
```{r}
beijing / days(1)
```
[1] 5.354167

使用 `%/%` 可以进行整除运算：
```{r}
beijing %/% days(1)
```
[1] 5

使用 `%%` 可以得到余数，但余数依然是一个 interval：
```{r}
beijing %% days(1)
```
[1] 2019-05-06 08:30:00 UTC--2019-05-06 17:00:00 UTC

***
## 处理时区和夏令时


### 时区（time zones）

为克服国际时间的混乱，1884年的国际经度会议规定，以英国格林尼治（Greenwich）天文台的经线为零度经线（即本初子午线），将全球划分为24个时区，每个时区都有自己的本地时间，同时提倡使用标准时间，即格林尼治标准时间，简称 GMT（Greenwich Mean Time）。理论上讲，格林尼治标准时间的正午是指当太阳横穿格林尼治子午线时的时间。但由于地球自转并不完全规则，故这一时间与实际的太阳时可能差到16分钟。为调整误差，自1924年2月5日开始，格林尼治天文台每隔一小时会向全世界发放调时信息。

为进一步协调时间误差，1972年1月1日，精度更高的世界协调时间（Universal Time Coordinated，简称 UTC）成为新的世界标准时间，广泛应用于国际无线电通信场合。但为了方便，在不需要精确到秒的情况下，通常也将 GMT 和 UTC 视为等同。通常所谓的 GMT 手表，仅指显示两个或两个以上时区时间的手表，并不涉及时间确定方式。

时区编码通常采用 `国家名（或大洲名）/城市名` 的方式列示，如 `America/Chicago` 、 `Asia/Tokyo`，不同城市可能属于相同时区。 或许你会好奇为何通常与国家和地区绑定的时区要用城市进行编码，这是因为城市名相比国家名在时间跨度上更加稳定，这样编码需要的后期调整更少，也可以更好反应历史。关于时区编码在历史上的变动，可以参考 [IANA 的原始时区数据库](http://www.iana.org/time-zones)。


使用 `Sys.timezone()` 函数，你可以查看 R 所设定的当前时区（如果 R 不知道，则返回 `NA` ）：
```{r}
Sys.timezone()
```

使用 `OlsonNames()` 函数可以查看完整的时区列表，这里只展示前6个：
```{r}
head(OlsonNames())
```
[1] "Africa/Abidjan"     "Africa/Accra"       "Africa/Addis_Ababa" "Africa/Algiers"    

[5] "Africa/Asmara"      "Africa/Asmera" 


中国采用北京所在地东八区的时间为全国统一使用时间，称为北京时间，或称中国标准时间（China Standard Time ，简称 CST）。北京时区是东八区，领先 UTC 八个小时，在电子邮件信头的 Date 域记为+0800。但在协调世界时的国际时区代码中，并不使用 `Asia/Beijing` 作为标记，而使用 `Asia/Shanghai`。

如果没有特别指定，**lubridate** 中默认使用世界协调时间（UTC）。 UTC 中没有夏令时（DST）的设定，便于计算:
```{r}
ymd_hms("2019-06-01 12:00:00")
```
[1] "2019-06-01 12:00:00 UTC"

 **lubridate** 包中的 `tz = ` 参数可以用来设定时区：
```{r}
x1 <- ymd_hms("2019-06-01 12:00:00", tz = "America/New_York")
x1
```
[1] "2019-06-01 12:00:00 EDT"
```{r}
x2 <- ymd_hms("2019-06-01 18:00:00", tz = "Europe/Copenhagen")
x2
```
[1] "2019-06-01 18:00:00 CEST"

虽然时间不同，但考虑到时区问题，两者其实完全一致:
```{r}
x1 == x2
```
[1] TRUE

如果我们使用 `c()` 函数将不同的日期-时间数据合并，那么原始数据中的时区信息将统一转换为第一个数据中的时区：
```{r}
c(x1, x2)
```
[1] "2019-06-01 12:00:00 EDT" "2019-06-01 12:00:00 EDT"

有两种改变现有数据时区的方式，第一种，使用 `with_tz()` 函数，只改变时间在不同时区的显示方式，并不改变客观时间：
```{r}
x1a <- with_tz(x1, tzone = "Australia/Lord_Howe")
x1a
```
[1] "2019-06-02 02:30:00 +1030"
```{r}
x1a == x1
```
[1] TRUE

第二种，使用 `force_tz()` 函数，只改变时区设置，这时客观时间也发生了改变：

```{r}
x1b <- force_tz(x1, tzone = "Australia/Lord_Howe")
x1b
```
[1] "2019-06-01 12:00:00 +1030"
```{r}
x1b == x1
```
[1] FALSE



### 夏令时（DST）

夏时令（Daylight Saving Time，DST），是一种为节约能源而人为规定地方时间的制度。一般在天亮早的夏季人为将时间调快一小时，可以使人早起早睡，减少照明量，以充分利用光照资源，从而节约照明用电。

各个采纳夏时制的国家具体规定不同。目前全世界有近110个国家每年要实行夏令时。1986年4月，中国中央有关部门发出“在全国范围内实行夏时制的通知”，具体作法是：每年从四月中旬第一个星期日的凌晨2时整（北京时间），将时钟拨快一小时，即将表针由2时拨至3时，夏令时开始；到九月中旬第一个星期日的凌晨2时整（北京夏令时），再将时钟拨回一小时，即将表针由2时拨至1时，夏令时结束。

中国自1992年起就暂停实行夏令时了，但世界上一些其他地区依旧存在夏令时制度，例如在伊利诺伊州芝加哥市，夏令时的变化曾经发生在2010年3月14日凌晨2点，"2010-03-14 01:59:59 CST" 是夏令时调整的前一秒，在一秒钟过后：
```{r}
DST_time <- ymd_hms("2010-03-14 01:59:59", tz = "America/Chicago") 
DST_time + dseconds(1)
```
[1] "2010-03-14 03:00:00 CDT"

在两小时过后：
```{r}
DST_time + dhours(2)
```
[1] "2010-03-14 04:59:59 CDT"

使用 durations 计算会返回真实时间（即一秒或两小时后，芝加哥市的当地时间），这可以防止由于时钟时间变化对时间间距精确测量的干扰，例如灯泡的使用寿命测量，虽然其结果有时难以理解。

如果使用 periods 则不受夏令时影响：
```{r}
DST_time + hours(2)
```
[1] "2010-03-14 03:59:59 CDT"

虽然由于夏令时的存在，一些时间只能返回 `NA`:
```{r}
DST_time + minutes(30)
```
[1] NA

当我们依赖时钟时间（例如纽约证券交易所需要在固定的开盘和收盘时间发出钟声）时，使用 periods 计算可以避免失误。

***
## 更深入了解 **lubridate**

上文主要参考了 Grolemund 和 Wickham (2011) 关于 **lubridate** 包的[原始论文](https://www.jstatsoft.org/article/view/v040i03)、 **lubridate** 自带的[说明文件](https://cran.r-project.org/web/packages/lubridate/vignettes/lubridate.html)以及 *R for Data Science* 一书中 [Date and times 一章内容](https://r4ds.had.co.nz/dates-and-times.html)，关于 **lubridate** 包的开发和更多细节可以到 github 上[相关开发页面](https://github.com/tidyverse/lubridate)查询。



