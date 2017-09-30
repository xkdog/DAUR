---
title: "DAUR-0intro"
author: "xkdog@126.com"
date: '`r Sys.Date()`'
output:
  word_document: default
  html_notebook: default
  html_document: default
---


# 软件准备

本节主要介绍R与RStudio的基础知识、安装方式与相关资源。这里所有的示例均以Windowns 10系统下的R 3.3.3版本为例进行，适用于任何R 3.0.0以上版本。RStudio说明适用于以RStudio 1.0以上版本。

## R安装与设置


### R简介

R是一个免费自由且跨平台通用的统计计算与绘图软件，它有Windows、Mac、Linux等版本，均可免费下载使用。R项目(The R Project for Statistical Computing)最早由新西兰奥克兰大学（Auckland University）的Robert Gentleman（1959-） 和 Ross Ihaka（1954-） 开发，故软件取两人名字的首字母命名为R。该项目始于1993年，2000年发布了首个官方版本R 1.0.0，后期维护由R核心团队[（R Core Team）](https://www.r-project.org/contributors.html)负责。截止2017年5月，已发布到 3.4.0版本。凭借其开源、免费、自由等开放式理念，R迅速获得了流行，目前已成为学术研究和商业应用领域最为常用的数据分析软件之一。

从[R主页](https://www.r-project.org/)中选择[download R](https://cran.r-project.org/mirrors.html)链接可下载到对应操作系统的R安装程序。打开链接后的网页会提示选择相应的[CRAN](https://cran.r-project.org/mirrors.html) ^[CRAN是Comprehensive R Archive Network（R综合典藏网）的简称，它提代R核心开发者提供的主程序、源代码和说明文件，也收录其他用户撰写的软件包。]镜像站 ^[镜像站(mirror sites)是网站的复制版本，将网站中的部分网页按原来的结构复制出来，即所谓“镜像”；再将这些镜像放置于具有独立网址的服务器中，以便缓解主站服务器的流量负荷，从而提升访问速率或作为备选网站在主站服务器出现意外时提供正常访问功能。]。目前全球有超过一百个CRAN镜像站 ，用户可选择就近下载。Windows平台下也可直接点击此链接 <http://cran.r-project.org/bin/windows/base/release.htm> 直接下载最新版的R进行安装。


### R的安装与初步尝试

程序下载完毕后，双击安装程序、选择对应的32位或64位程序（若不明白这其中的区别，可选择同时安装）即可安装。初学者可按默认设置安装在系统盘，并选择一切默认设定完成安装程序。高级用户可参考[谢益辉](https://yihui.name/)等人的[安装经验](https://bookdown.org/yihui/r-ninja/setup.html#r)进行相关设置，如安装时去掉版本号以便于日后更新R包。初学者可先略过某些技术细节，将注意力集中于数据分析本身。


安装完毕后，打开R，可看到R的操作界面，称为R控制台（R console）。类似其他以编程语言为主要工作方式的软件，R的界面简洁而朴素，类似一个空白的写字板。但在这一朴素的外表下，是丰富而复杂的运算功能。

在R命令提示符`>`后输入相关命令，并摁回车键即可展示相关结果。在不知道任何R命令的情况下，你也可将R作为一个高级的科学计算器使用。

通过一些简单的R命令，可更好地了解R的风格。例如
```r
data()
```
这一命令可展示R自带的所有数据集，R数据的后缀名为`.RData`。注意命令中的`()`是不可缺少的，是R命令的有机组成部分。可以发现其中有一个`mtcars`数据集。欲了解这一数据集的内容，可输入如下命令
```r
?mtcars
```

`?`表示求助。此时会在默认浏览器 ^[建议使用Chrome浏览器。]中打开一个新的网页，介绍此数据的来源及各变量的具体定义与测量单位。查阅文档可知，`mtcars`数据是从1974年美国汽车趋势（Motor Trend）杂志中抽取了32辆汽车的基本性能数据，并对各变量的含义与单位进行了详细说明。以后会利用该数据进行基本展示，读者应自行花一分钟的时间去了解该数据中各变量的具体含义。

直接键入`mtcars`会在R中直接展示整个数据。若数据太长，则可能占据太多空间或消耗大量时间。如只想直观了解数据的基本形式，使用`head()`命令即可展示某一数据的前几行（默认6行），也可通过以下方式展示指定数据的前若干行
```{r}
head(mtcars, 5)
```
如此即可展示`mtcars`数据前5行。相信你很快就能猜出`tail(mtcars, 5)`是何功能。


## R包安装与加载

R的初始安装程序只包含少数几个基础模块和若干基础安装包（base packages），使用它们虽已能完成诸多统计分析与可视化呈现的工作，但往往需要安装并加装其他开放性的软件包来实现更多功能或简化相关的操作流程。这些附加包通常通过CRAN镜像站下载安装，并在加载后可调用相关函数执行计算或绘图功能。一般而言，安装R包的方式有三种。

### 在线安装


在线安装R包命令为`install.packages("")`，`""`中填入软件包的名称 ^[遵从R开发者的书写惯例，在描述R命令的名称时也应带上小括号`()`，以表示这是一个R命令。]。例如，在R命令窗口（即所谓的R控制台）中的`>`符号后输入如下命令：
```r
install.packages("dplyr")
```
确保电脑联网，选择镜像后点击确定即可在线安装软件包`dplyr`。其中的双引号`""`也可使用单引号`''`替换。安装成功后应出现如下提示：
```r
package ‘dplyr’ successfully unpacked and MD5 sums checked
```

在`install.packages()`命令中不能省略双引号或单引号，否则会出现如下错误提示：

```r
Error in install.packages : object 'dplyr' not found
```

若想一次性安装多个包，可使用如下方式：
```r
install.packages(c("Package A", "Package B"))
```
即使用`c()`将不同的包加以联接，中间加上逗号。字母`c`的含义其实正是联结（concatenate，或理解为combine更容易记忆）。

如此，可使用如下方式安装常用的数据分析包和RStudio文档写作与展示相关包：
```r
install.packages(c("ggplot2", "dplyr", "tidyr", "stringr", "lubridate", "readr", "readxl", "haven", "httr", "rvest", "xml2", "devtools", "tidyverse"))
```

读者不妨将此命名拷走并运行。由于一次性安装的包比较多，可能需要几分钟左右的时间才能安装完毕上述所有包。

### 离线安装

由于网络问题，在线安装有时可能出错，此时可选择离线安装。

离线安装首先要求有相关R包的压缩包。如已确定所想安装的包名，在CRAN网站上选定镜像站后，点击左侧的Packages一栏，可看到所有在该网站上储存的R包。点击R包名称进入相关页面，找到Windows Binaries一行对应的`.zip`文件，下载到本地电脑（Mac系统选择`.tgz`文件）。该文件无需解压缩，打开R后，遵循以下路径安装该压缩包`Packages-->Install package(s) from local files`，点击后选择安装包即可完成安装。

离线安装的问题在于，有些 R 包的功能依赖于另外一些包，因此需要同时安装其所依赖的其他它。采用离线安装时无法加载这些包。

### GitHub安装

存放于CRAN上的包通常是较为成熟、某种程度上讲也是相对滞后的包。包在维护和更新过程中会增加一些新的功能，或者总会有一些新的试验性的包出现，以满足用户的功能。这些旧包的更新版、或者是未曾公开推出的新包，通常会以开发版（development version）的形式储存于类似[GitHub](https://github.com)这样的代码托管平台 ^[GitHub 是一个面向开源及私有软件项目的托管平台，于 2008 年 4 月 10 日正式上线，是目前全球规模最大的社会化编程及代码托管网站。]，而并未提交到CRAN。甚至有开发者本人并无意向将自身开发的R包提交至CRAN镜像。此时，前述两种安装方式就不再有效。

若想直接从GitHub安装相关包，建议通过Hadley开发的`devtools`包完成安装。以下是基本步骤：

1. 安装`devtools`包（请复习并实践第一种安装法）。
2. 加载该包，即输入`library(devtools)`。
3. 使用其中的`install_github()`函数完成安装。

以下是示例。
```r
install.packages("devtools")
library(devtools)
install_github("hadlley/dplyr")
```
`install_github()`命令要求先给出开发者名字再给出包名。这对于只知道包名而不知道开发者名字的用户是不利的。好在使用这一安装方式的通常为中高级用户，他们自可从GitHub页面找到相关包并阅读其安装说明后安装。


### R包加载

使用R基础安装包中的函数进行数据分析时，直接调用函数即可，无需先加载这些包。


若要引入附加包中的函数进行数据分析，首先要加载这些包。这有两种方式。

一是使用加载命令`library()`，此时包的名称不需要加引号。例如：
```r
library(dplyr)
```
此时会出现如下显示：
```r
载入程辑包：‘dplyr’

The following objects are masked from ‘package:stats’:

    filter, lag

The following objects are masked from ‘package:base’:

    intersect, setdiff, setequal, union
```

此中内容先不加过多解释，其基本要点是：加载此包之后，即可使用此包中的`filter()`、`lag()`等函数，而原基础安装包中的同名函数则会被最近一次引入的包中的函数所覆盖（即失效）。例如



二是使用双冒号`::`的形式调用某一函数，其用法为`package_ name::function_name`，即先写包名，双冒号后写入函数名称，即可调用该包中的这一函数。
```{r}
dplyr::sample_n(mtcars, 2)
```
上述命令表示，使用`dplyr`包中的`sample_n()`函数，从`mtcars`数据中任取两行。

退出R时无需先“退出”包再退出R，保存数据对象后直接关闭R即可。

### 工作目录设置

R 默认读入和写出的数据对象都存储在当前工作目录（working directiory）中。若要读入其他目录中对象，则需要指定工作路径。一般而言，开始某个数据分析项目时，即可新建一个目录并将其设定为当前工作目录。此后所有的数据对象均储存其中。

对使用中文操作系统的分析者而言，为避免因汉字编码问题而在读入文件和分析数据时发生莫名的错误，首先需要明确一条**基本命名规则**：R 中的目录名和文件名不能有中文，也不要出现除中划线 `-` 和下划线 `_` 之外的特殊字符，而只使用英文字母、数字以及 `-` 和 `_` 的组合。

对普通用户而言，建议在非系统盘（如D盘、E盘等）的根目录下建立数据分析目录。创建新目录可直接在操作系统中进行，也可在R 中使用命令`dir.creat(" ")`建立新目录，`" "`中输入路径名和目录名，例如：

```r
dir.create("D:/R2017")
```

此时即可在D盘根目录下找到名为 R2017 的目录。

**注意**：R 中的路径分隔符为正斜杠（forward slash）`/`，而不是反斜杠（backward slash）`\`。正反斜杠的译法多少有些令人费解，不妨取名为撇斜杠（/）和捺斜杠（\\），更适合中国人的理解方式。

如目录忆创建，可通过命令`setwd()`将该目录设为当前工作路径，例如：

```r
setwd("D:/R2017")
```
其中`wd`即working directory的首字母缩写。


设置完毕后，可通过`getwd()`命令查看当前工作路径，此时括号中不需要填入任何内容。



### 语言选项设置

也许你已注意到前面提示中出现了“程辑包”而不是“程序包”这种古怪的翻译。这是因为 R 的相关中文提示最早并非由中国大陆人士翻译所致。实际上，按照其默认的中文提示（如错误提示）进行网络搜索，通常不能找到很好的资源。这一方面是由于翻译质量与翻译习惯问题，另一方面是由于国内的中文R社区在活跃度和技术水平上与国外的成熟社区还有较大距离。因此，安装完R后的第一项设置，可考虑修改默认语言选项，将其设为英文。

若想使修改后的语言选项仅对此次打开的R有效，可使用如下命令：
```r
Sys.setenv(LANG = "en")
```
`Sys.setenv()`是修改环境变量函数，`Lang`表示语言（language），`en`表示英语（English）。这样在将来出现错误提示时，可使用搜索引擎检索到相关解答资源。注意应当使用英文搜索引擎来搜索英文资源，以提高效率。

这种做法的劣势在于关闭R而重新打开后，其默认语言选项仍是中文。严格来说，这并不是R本身的设置，而是它默认采用Windows系统的默认语言。对于大陆人士而言，其所使用的Windows操作系统通常默认语言为中文，因此R会使用中文提示。为一劳永逸地改变此版本下的R的默认语言，可采用如下方式：

1. 找到安装路径下`etc`文件夹中的`Rconsole`文件。如选择默认安装方式，R通常安装在C盘的Program Files目录下。以本人写作此文档时的R版本为例，其路径为：`C:\Program Files\R\R-3.3.3\etc`。
2. 用文本编辑器打开`Rconsole`文件。建议安装[Notepad++软件](https://notepad-plus-plus.org/)而不是使用Windows自带的记事本软件打开^[Notepad++也是一个免费的自由开源软件。之所以推荐使用它而不是记事本，原因比较复杂，涉及计算机的字符编码问题，暂不必深究。仅了解一点即好：如有可能，Windows平台下尽可能使用Notepad++而不是其自带的记事本软件创建或修改文本文件。]。找到以下文字：
```r
## Language for messages
language = 
```
3. 通常来说，`=`号后面默认是空白，以便调用Windows的默认语言。在 `=`后填入指定的语言缩写，保存修改后关闭该文档，即可永久性修改默认语言设置。例如：
```r
## Language for messages
language = en
```

其中`en`就表示English。保存后关闭（Windows系统可能会提醒需要管理员权限方可修改，点击确定），重新打开R，应当可以看到所
有提示文字已变为英文:

```r
R version 3.4.0 (2017-04-21) -- "You Stupid Darkness"
Copyright (C) 2017 The R Foundation for Statistical Computing
Platform: x86_64-w64-mingw32/x64 (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under certain conditions.
Type 'license()' or 'licence()' for distribution details.

  Natural language support but running in an English locale

R is a collaborative project with many contributors.
Type 'contributors()' for more information and
'citation()' on how to cite R or R packages in publications.

Type 'demo()' for some demos, 'help()' for on-line help, or
'help.start()' for an HTML browser interface to help.
Type 'q()' to quit R.

[Previously saved workspace restored]
```

使用R，即意味着基本告别中文操作与提示。请尽快转向并熟练英文环境下的相关操作，这对提升R的使用效率至关重要。



## RStudio安装与设置

### RStudio简介

R虽然是个强大的统计分析软件，但仍欠缺完成数据分析的整体流程所需要的衍生功能。例如，如何满足普通用户对友好操作界面的需求，如何生成可重复、交互性的报告（Word格式、HTML格式或其他格式）并与他人共享，如何快速导入其他类型的数据（如Excel、SPSS、Stata、SAS等常用数据管理与分析软件格式的数据），等等。这就需要一个更具整合性的操作平台，以更有效率和对普通用户更友好的方式完成数据分析、报告撰写、成果发布等工作。

RStudio就是一个优秀的R集成开发环境 ^[集成开发环境（Integrated Development Environment，IDE）软件是用于程序开发的应用程序，一般包括代码编辑器、编译器、调试器和图形用户界面工具，集成了代码编写、分析、编译、调试、建模等功能的一体化开发软件套。]。它集成了R、带语法高亮和命令补全的代码编辑器、画图工具、代码调试工具等工作环境，同样提供Windows、Mac和Linux版本，同时具有免费的开源版本和付费的商业版本供用户选择。个人用户或普通用户选择免费版本即可，具有更高要求的企业用户或高级用户可选择商业版本。RStudio的开发始于2010年，2011年2月发布测试版，2016年发布1.0.0版本。此后介绍均以RStudio 1.0.0之后的版本为基础进行演示。

RStudio的[核心团队](https://www.rstudio.com/about/)包括以首席科学家[Hadley Wickham](http://hadley.nz/)为代表的其他数据科学家和软件工程师，他们是驱动R与数据科学进一步发展和推广的活跃力量，其所开发的诸多R包已成为数据分析的最常用工具。

RStudio可从其[官网](https://www.rstudio.com/products/RStudio/)选择对应系统的版本下载安装。安装选择默认选项即可，注意一般应在安装完R后再安装RStudio。

对初级用户而言，RStudio的最初调试只涉及Tools菜单下的Global Options子菜单。打开后，在General选项中可选择与RStudio相关联的R版本（如果只安装了一个版本的R，此步骤可忽略），还可设定当前工作目录（working directory）。当前工作目录的设置非常重要，稍后继续说明。在Appearance选项中可选择字体、字号和背景颜色，可自行尝试调整到个人觉得舒适的配置。

### RStudio功能简介

为确保能实现RStudio的诸多拓展功能，请确保已执行以下命令安装各相关包。

```r
install.packages(c("knitr", "rmarkdown", "bookdown", "xaringan", "shiny"))
```

其中，`knitr`包和`rmarkdown`包可用来将Rmarkdown文档转为HTML网页、Word文件或PDF文件；`bookdown`包可用来撰写Markdown格式的长文档（书稿）并转为HTML网页、Word文件或PDF文件；`xaringan`可用来制作HTML5格式的网页幻灯片；`shiny`包可用来制作动态网页APP。

安装完毕这些包后，打开RStudio，点击左上角的新建空白文档图标的向下箭头，可以看到可供选择的新建文档格式包括R script、R Notebook、R Markdown等。一般可选择R Markdown为基本文档格式。以下如无特殊说明，均以此格式为准进行演示。

如想观察数据，可键入如下命令：
```r
View(mtcars)
```

此时左上方窗口会出现数据结构示意，并可执行数据排序（点击变量名称中的上下箭头按钮）、筛选（点沙漏形状的Fitler按钮）等简单功能。

![](rstudio_viewmtcars.png)





### RStudio中的常用快捷键




## Markdown语法简介


## LaTeX语法简介


## 相关资源索引


GitHub: <http://github.com>

Stack Overflow: <https://stackoverflow.com>

# R中的数据类型

数据类型是指一个变量内元素取值的类型。对“数据”的理解有不同角度和层次，由此也可产生不同的数据类型分类。

## 

主要包括：数值型、字符型、逻辑型、复数型。

对象类型：R语言组织和管理内部元素的不同方式。主要包括：向量、矩阵、数组、列表、数据框、因子、时间序列。

但是要注意有两种情况不能用上述4种数据类型来描述，分别是数据的缺失（NA）和数据的未知状态（NULL）。
NA：占据数据空间，参与运算
NULL：不占据任何数据空间
数据类型我们简单介绍一下。


数值型：取值是实数，在R语言环境中常用数字来表示。
复数型：取值为复数。
逻辑型：取值为TRUE（T）或者FLASE（F）。
字符型：取值是字符串。


# tidyverse 数据处理

## tidyverse 简介

tidyverse 是一系列基于相同理念的数据处理包的合集。此词中的`tidy`意为整洁，`verse`意为诗篇、诗行，合起来意指数据如诗行般整洁易读，即成为“整洁数据”（tidy data）。tidyverse 集合了当下最为流行的数据处理包，是简化数据操纵、便利统计操作、美化结果呈现的高效工具。

### 基本理念

整洁数据是Hadley等人极力提倡的一个数据处理理念。若要执行统计计算，统计软件对数据格式有一定要求。但通常外部导入的数据并不一定能达到软件处理的要求，而需要进行一定的预处理，此过程通常也称为数据清洗（data cleaing）。实际上，这种前期处理的工作往往占据比狭义的统计分析更多的时间。为此，需要将无序数据（messy data）整理成可供计算机程序识别与处理的、具备特定格式的数据，即整洁数据，其基本特征有三：

1. 每列为一个变量（Each variable is in a column）；
2. 每行为一个观测为一行（Each variable is in a column）；
3. 每个单元格为一个取值（Each value is a cell）。

  
这些特征在后面的例子中会逐一呈现，这里暂不展开。分析者获得的数据有些天然即能达到要求，但很多时候并非如此。使用 tidyverse 包，可高效地将无序数据转为整洁序列，以便软件分析。


### 安装与加载

安装 tidyverse 包，即可一次性安装多个系列包。具体如下：

```r
install.packages("tidyverse")
```

此命令将安装如下包：

- 最常用数据分析包：
    - ggplot2，用于数据可视化
    - dplyr，用于数据操纵
    - tidyr，用于数据整洁
    - readr，用于读入 R 格式数据
    - purrr，用于编程
    - tibble，用于形成便于数据处理的数据框

- 数据操纵类：
    - stringr，用于处理字符串数据
    - lubridate, 用于处理日期和时间数据
    - forcats，用于处理因子数据

- 数据导入类：
    - DBI，用于联接数据库
    - haven，用于读入SPSS、SAS、Stata数据
    - httr，用于联接网页API
    - jsonlite，用于读入JSON数据
    - readxl，用于读入Excel文档
    - rvest，用于网络爬虫
    - xml2，用于读入xml数据

- 数据建模类：
    - modelr，用于使用管道函数建模
    - broom，用于统计模型结果的整洁

安装成功后，可通过常规方式加载 tidyverse 包，结果如下：
```{r, warning=FALSE}
library(tidyverse)
```
此时加载的是最常用的数据分析包，如 ggplot2、dplyr、tidyr等。若需其他 tidyverse 包，可再单独导入。

若要查看此中的相关包是否为最新版本，可通过如下命令：

```r
tidyverse_update()
```

此时若出现相关更新提示，可遵照执行。

```r
The following packages are out of date:
 * dplyr  (0.5.0 -> 0.7.0)
 * purrr  (0.2.2 -> 0.2.2.2)
 * tibble (1.2 -> 1.3.3)
Update now?

1: Yes
2: No

Selection: 
```

在`Selection`后键入`1`即可更新相关包。

开始数据分析时，不论是否用到，可先加载 tidyverse 系列包，以便后续工作。

## dplyr 包

dplyr 是用于数据操纵的最流行R包之一，使用它并结合 R 的基础函数，已可完成大部分的统计描述工作。

### dplyr 中的函数类型

dplyr 的基础函数只有5个，其名称和功能如下：

- `mutate()`：生成变量
- `select()`：选择变量
- `filter()`：筛选观测
- `arrange()`：数据排序
- `summarise()`：描述统计

这些函数的功能基本可以“望文生义”。这也是 tidyverse 系列包的命名风格：“人”（此处意指函数的功能）如其名。

这5个基础函数，加上用于分组的函数`group_by()`以及管道函数`%>%`，构成了 dplyr 的最常用函数。此外，dplyr 还提供用于合并数据框、随机选取观测、计算变量频次与取值序位等功能的若干函数。


以下均通过实例说明各函数的用法。请确保已通过以下命令加载 dplyr 包。

```r
library(dplyr)
```

当然，也可通过`library(tidyverse)`调用此包。

### 生成变量：`mutate()`

出于分析的需要，研究者通常需要基于已有变量创建新变量，并保存至原数据框中，然后再进行后续分析。此时即可使用`mutate()`函数。

`mutate()`函数的基本用法为：

```r
mutate(dataframe,
       newvar1 = expression1,
       newvar2 = epxression2,
       ...)
```

其中，

- dataframe 表示待操纵的数据框
- newvar1 表示想生成的第1个新变量
- newvar2 表示想生成的第2个新变量
- expression1 表示用以生成 newvar1 的命令
- expression2 表示用以生成 newvar2 的命令
- 生成更多变量可依次类推



R 自带数据`women`给出了美国30~39周岁之间女性在不同平均身高下对应的平均体重，但其单位分别为英寸（in）和磅（lb）。将其转换为国际单位制会更容易为其他国家的分析者理解，转换公式如下：

- 1 in = 2.54 cm
- 1 lb = 0.45 kg

首先观察原始数据。
```{r}
women
```

现拟生成新变量 `height_cm` 和 `weight_kg`，即以 cm 和 kg 为单位的身高和体重。命令如下：

```{r}
mutate(women, 
       height_cm = height * 2.54, 
       weight_kg = weight * 0.45
       )
```

从实用角度而言，对已用 cm 和 kg 衡量的平均身高和平均体重之类，无需保留小数。为此可在对上述命令进行优化：

```{r}
mutate(women, 
       height_cm = round(height * 2.54), 
       weight_kg = round(weight * 0.45)
       )
```

BMI指数（身体质量指数，英文为Body Mass Index，简称BMI），是用体重（单位：kg）除以身高（单位：m）的平方得出的数字，即：
$$
BMI = \frac{体重（kg）}{{身高}^2(m^2)}    
$$

BMI指数是目前国际上常用的衡量人体胖瘦程度的一个参考标准。下面生成各平均身高和平均体重下对应的BMI指数，变量命名为`bmi`，并保留至小数点后1位。


```{r}
mutate(women, 
       height_cm = round(height * 2.54), 
       weight_kg = round(weight * 0.45),
       bmi = round((weight_kg / (height_cm/100)^2), 1)
       )
```

如果只想保留新生成的变量、而不保留原有的所有变量，可使用`transmute()`函数。试比较：

```{r}
transmute(
  women,
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

按世界卫生组织（WHO）的分类，成人BMI指数与肥胖程度之间的分类如下：

BMI | 肥胖程度
----|---------
< 18.5 | 偏瘦（Underweight）
18.5~24.9 | 正常（Normal）
25.0~29.9 | 偏胖（Overweight）
>= 30.0   | 肥胖（Obesity）


依据此表，对上前述 BMI 值进行分类，该变量命名为`class`。

此时使用R的自有函数`within()`来修改数据框可能更为方便。

```{r}
women_bmi <- within(women_bmi,
                    {
                    class = NA
                    class[bmi < 18] = "Underweight"
                    class[bmi >= 18 & bmi <= 24.9] = "Normal"
                    class[bmi >= 25 & bmi <= 29.9] =  "Overweight"
                    class[bmi >= 30] =  "Obesity"
                    })
head(women_bmi)
```

显然所有`class`的取值均为正常（normal）。基于均值计算的BMI指数通常并无实际意义，只有对独立的个体进行体重是否超标的判定才有实际价值。此例只作为对变量的操纵示例。

结合R自有函数`cut()`，仍可在`mutate()`框架下完成同样的工作。

```{r}
women_bmi2 <- mutate(
  women_bmi,
  class = cut(bmi,
  breaks = c(0, 18.5, 25, 30, 100),
  labels = c("Underweight", "Normal", "Overweight", "Obesity"),
  right = FALSE)
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

当一个数据框包含太多并不一定在一次分析中使用到的变量、即存在太多无关的列时，选择部分变量（列）另存为某一数据对象，对针对这一新对象进行数据操纵是更为方便的。这尤其适用于对大型社会经济调查的问卷分析中。研究者通常只需要选取其中的若干个变量进行分析，而无需使用其中的全部变量。此时即可使用`select()`函数。

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

使用`var1: var2`的形式能够选择能够选择指定两个变量之间的所有变量（包括这两个变量本身）。试比较以下两个命令的结果：

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


`select()`的功能不仅如此。在此函数中通过适当的参数设置，可更效率地选择变量，相关参数如下：

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

实际上，在CGSS2013问卷中，c2a代表着一个小规模的态度量表，询问了被调查者关于医疗卫生公共服务10个方面的满意程度。`cgss2013_c2a`数据已提取此量表的所有回答。请自行思考如下命令有何功能：

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

`all_equal(x, y)`函数来自于 dplyr 包，它相当于R的自带函数`all.equal(x, y)`。

请自行观察以下命令的结果：
```r
x <- filter(mtcars, am == 1 & cyl == 4)
y <- filter(mtcars, am == 1 | cyl == 4)
all_equal(x, y)
```

### 变量排序：`arrange()`

排序是最常见的数据操纵方式之一。dplyr 中可使用 `arrange()`实现这一功能，该函数及相关函数比 R 自带的`sort()`、`order()`、`rank()`等涉及排序的函数更为直观且不易出错。

`arrage()`函数的用法如下：

```r
arrange(dataframe, var1, var2, ...)
```
其中，

- `dataframe` 表示待排序的数据框
- `var1` 表示作为首选排序标准的变量
- `var2` 表示作为次选排序标准的变量
- 默认升序（从小大到）排序，除非用`desc(var1)`的方式降序（从大到小）排序

下面仍以mtcars数据进行示例。

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
- `FUN1` 表示以以生成var1的统计函数
- 不同统计量可在逗号后依次写出，但只能返回单一统计量，即不能在一个变量名中给出多个统计量值，如**不能**写出如下形式：`quartiles = fivenum(var)`，因为`fivenum()`一次性给出5个统计量的值。

```{r}
result <- summarise(mtcars, 
          mpg_mean = mean(mpg),
          mpg_sd = sd(mpg),
          mpg_median = median(mpg))
result
class(result)
```
注意`result`的类型实为一个数据框。这见`summarise()`函数实际上将描述统计的结果储存为一个数据框，这可为后续的进一步处理提供方便。

多数 R 自带的描述统计函数都能与`summarise()`组合使用，如`mean()`、`sd()`、`max()`、`min()`、`median()`、`quantile()`、`range()`、`sum()`等。同时还可与 dplyr 包中的相应函数相配合使用：

- `n()`：计数，相当于length()
- `nth()`：给出指定顺序位置上的数值
- `first()`：给出向量中的首位元素
- `last()`：给出向量中的末位元素
- `n_distinct()`：给出向量中不同取值的个数
 


单独的`summarise()`函数本身而言并不比 R 自带的描述统计方式更为便捷，只有在与分组函数`group_by()`组合在一起时，才能显示其便利性。为更好地展示分组描述统计结果，需要了解一个重要的函数：`%>%`。


### 管道操作：`%>%`

所谓管道操作（pipe operation），是将管道操作符（pipe operator）左边的对象传递给右边的函数并成为后者的作用对象。dplyr 包引入了其他包中的管道操作符`%>%`，通常也称为管道函数。

前面曾有这样的操作：

```r
mtcars <- select(mtcars, am, mpg, everything())
head(mtcars)
mtcars_arranged <- arrange(mtcars, am, mpg)
head(mtcars_arranged)
```
其逻辑是先选择、后排序，在实际分析中，后续还可能进行分组统计。如此一来，就会出现一些像`mtcars_arranged`这样的中间对象。如果中间对象多，而分析者又无意对之进行保存、但出于运算需要又不得不对之进行命名，就会使分析过程变得繁杂，代码也会显得拖沓。此时，引入管道函数就显得十分必要。试看以下代码：

```{r}
mtcars %>% select(am, mpg) %>%
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

1. 选取 `mtcars` 数据中的 `am` 和 `mpg` 变量；
2. 按先 `am` 后 `mpg` 的方式升序排序；
3. 按 `am` 对数据进行分组；
4. 分组（即分手动档和自动档车型）分别统计车辆频次、`mpg`的均值、标准差和中位值；
5. 将描述统计结果保留至小数点两位。

如果不使用管道操作，就需要使用命令嵌套的方式编写代码，此时代码就会变得层次复杂、难以理解。

有时上一步命令的结果并不一定能作为下一步函数的第一个参数，此时可使用点号`.`来表示所传递的数据对象。


```{r}
library(ggplot2)
mtcars %>%  qplot(wt, mpg, color = am, data = .)
```

上述命令使用了 ggplot2 包中的`qplot()`函数，使用不同颜色绘制手动档和自动档车的车重（`wt`）与油耗（`mpg`）之间关系的散点图。此函数的细节暂可略过，此处请仅注意`data = .`的用法。


### 随机取样：`sample_n()`与`sample_frac()`

分析者有时需要从数据随机抽取若干个观测（行），或随机抽取特定百分比的观测。这可分别通过`sample_n()`与`sample_frac()`实现，其中`frac`是英文 fraction的缩写，意为“分数”（与整数相对应），即部分的意思。


`sample_n()`与`sample_frac()`的用法如下：

```r
sample_n(dataframe, size = , replace = FALSE)
sample_frac(dataframe, size = , replace = FALSE)
```
其中，

- `dataframe` 表示待抽取的数据框
- `sample_n()` 中的`size` 取正整数，表示待抽取的观测数（行数）
- `sample_frac()`中的`size` 通常取[0, 1]之间的小数，表示比例。
- 默认`replace = FALSE` 表示无放回抽样，若设置 `replace = TRUE` 表示有放回抽样 

试观察以下结果：

```{r}
sample_n(mtcars, 5)
sample_frac(mtcars, 0.2)
mtcars %>% 
  group_by(am) %>% 
  sample_n(5)
```

由于未设定随机数种子数，每次执行上述命令的结果自然有所不同。


