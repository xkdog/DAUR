# DAUR

DAUR: *Data Analysis Using R for Social and Behavioral Sciences*

This repo was built for my ongoing project of publishing an introductory Chinese textbook titled *Data Analysis Using R for Social and Behavioral Sciences*. I'm not familiar with the [Bookdown](https://bookdown.org/) style yet. Once I figure it out, I will put all the materials into a bookdown repo.

本文档中的文档为[本人](http://zfxy.nankai.edu.cn/xk)正在撰写的《R语言数据分析入门》的部分内容。版权归本人所有。

如想参与本系列文档的撰写，请阅读以下内容。

## 写作风格

只有一条原则：简洁。遵循所谓的奥卡姆剃刀原则：如无必要，勿增实体。翻译一下：不要多写一个字。

比如：

- 原文
    - “在不知道任何 R 命令的情况下，你也可将 R 作为一个高级的科学计算器使用。”
- 修改为（删去“你”字，完全不影响内容；如此，能删则删）
    - “在不知道任何 R 命令的情况下，~~你~~也可将 R 作为一个高级的科学计算器使用。”

再如：

- 原文
    - 如果是 Mac 系统，可使用 [Sublime](https://www.sublimetext.com/) 软件或其他开源的文本编辑器打开。
- 修改为（比原文少了两个汉字）
    - 若~~如果~~是 Mac 系统，可使用 [Sublime](https://www.sublimetext.com/) 软件或其他~~的~~开源文本编辑器打开。


## 撰写语法

- 中文语句中出现英文单词，该英文单词两侧需要有英文状态下的空格。例如，“如何利用 R 的向量化运算特征来实现目的”。
- 所有的 R 包名称，应当加粗，即使用一对\*号包裹。例如，Hadley 开发的**dplyr**包。
- 所有的 R 命令，行内的应放在\`\`中，如`?mtcars`；行间的应放在\`\`\` 和 \`\`\`中。
- 所有图片的命名，应当以`pic-`开头，并附以能够代表图片实质内容的英文缩写，然后保存为`png`格式。
- 撰写者应充分熟悉[**tidyverse**风格](http://style.tidyverse.org/)。
- 撰写者必须使用 Markdown 语法进行写作。
- 未尽事宜，请联系xkdog@126.com。


## 章节预想

1. Chap01: 软件基础知识
    - R 简介
    - RStudio 简介
    - R Markdown 简介
    - **tidyverse** 简介
1. Chap02: R 基础操作
    - 数据结构
    - 数据导入
    - 基础命令
    - 数据管理
1. Chap03: **tidyverse** 数据处理 R 包
    - **dplyr**
    - **tidyr**
    - **lubridate**
    - **stringr**
    - **rvest**
1. Chap04: R 绘图
    - R 基础绘图命令
    - **ggplot2** 风格绘图
    - 科研论文中的统计绘图
1. Chap05: 统计分析基础
    - 描述统计
    - 假设检验与置信区间
    - 线性回归
    - 方差分析
    - 广义线性模型


2019 学员名单：


| name | id | department | 负责章节 
|:---:|:---:|:---:|:---:|
|唐诗文|2120182142|社会学 | chap01-intro01-R
|张涵|2120182145| 社会学 | chap02-RBasics01-datastructure.md
|杨旋|2120182158 | 应用心理学|
|罗寓|2120182160 | 应用心理学| chap03-tidyverse01-dplyr
|丛琪珈|2120182155| 应用心理学|chap01-intro04-tidyverse
|曹雪|2120182154| 应用心理学| chap03-tidyverse03-stringr
|倪楠|2120182161| 应用心理学| chap01-intro02-RStudio
|王姣|2120182156| 应用心理学| chap02-RBasics02-data_import
|王晖|2120182162|应用心理学|
|张卉|2120182163|应用心理学| chap03-tidyverse04-lubridate.md
|王晓文|2120182157|应用心理学| chap02-RBasics03-base_function
|洪斌|2120182159|应用心理学|

吕小康

南开大学周恩来政府管理学院
