## R 制作词云

步骤如下：
- 获取数据(以 *政府工作报告* 为例)
- 分词( **jiebaR** 分词包)
- 统计词频
- 画词云( **wordcolud2** 词云包)

需安装的 R 包如下：**jiebaRD**, **jiebaR**, **Rcpp**, **wordcloud2**。

### 分词

结巴分词( **jiebaR**)，是一款高效的 R 语言中文分词包，底层使用 C++ 语言，通过 Rcpp 调用。

载入分词包：
```
library(jiebaR)
```
#### 案例引入

- 调用分词器 `worker()` 并命名。
```
fc <- worker()
```

- 分词语法有如下三种，效果相同，以“R 语言是门出色的编程语言”为例：
```
fc["R语言是门出色的编程语言"]
fc <= "R语言是门出色的编程语言"
segment("R语言是门出色的编程语言", fc)
```

- 若需对两句话分词，并要求返回各自的分词结果，应调用 `bylines = TRUE` 参数，举例如下：
```
fc <- worker(bylines = TRUE)
fc[c("R语言是门出色的编程语言", "今天是2019年某月某日")]
```
- `worker()` 分词器还有如下参数，可按需要调用：
```
worker(type = "mix", dict = DICTPATH, hmm = HMMPATH, user = USERPATH, idf = IDFPATH, stop_word = STOPPATH, write = T, 
       qmax = 20, topn = 5, encoding = "UTF-8", detect = T, symbol = F, lines = 1e+05, output = NULL, bylines = F, user_weight = "max")
```
#### 配置词典

- 对于分词结果好坏的关键因素是词典，**jiebaR** 有默认的系统词典，可通过 `show_dictpath()` 函数查看其路径。
```
show_dictpath()
```
- 通过 `dir(show_dictpath())` 函数查看系统词典所在目录。
```
dir(show_dictpath())
```
- 该目录中应包含如下文件：

    * jieba.dict.utf8，系统词典文件，基于最大概率法

    * hmm_model.utf8，系统词典文件，基于隐式马尔科夫模型

    * user.dict.utf8，用户词典文件

    * stop_words.utf8，停止词文件

    * idf.utf8，IDF 语料库

*对于不同的行业或文字类型，应用专门的分词词典，可通过参数 `user` 自定义用户词典。*

#### 停止词过滤

停止词指不需要作为结果的词，比如 *的、地、得、我、你、他* 等。这些词出现频率高，但影响分词结果，应过滤掉。

```
fc <- worker(user = "C:\\Users\\john\\Documents\\R\\win-library\\3.5\\jiebaRD\\dict\\user.txt", 
            stop_word = 'C:\\Users\\john\\Documents\\R\\win-library\\3.5\\jiebaRD\\dict\\stop_word.txt',)
```
其中，参数 `user = "C:\\Users\\john\\Documents\\R\\win-library\\3.5\\jiebaRD\\dict\\user.txt"` 自定义用户词典，`stop_word = 'C:\\Users\\john\\Documents\\R\\win-library\\3.5\\jiebaRD\\dict\\stop_word.txt'` 自定义停止词词典(涉及路径均为编者自带，应按需要更改)。

#### 文件分词

- 用函数 `readLines` 读取文件
```
gwr <- readLines("gwr.csv") 
```
- 分词
```
fc <- worker() # 调用分词器

word0 <- fc[gwr] # 进行分词

fc["./gwr.csv"] # 将分词结果保存到相应工作路径下

fc <- worker(stop_word = "C:\\Users\\john\\Documents\\R\\win-library\\3.5\\jiebaRD\\dict\\stop_words.utf8") # 设置停止词词典，重新调用分词器

word <- fc[gwr] # 重新分词

fc["./gwr.csv"]
```

### 统计词频

用 **jiebaR** 中 `freq()` 函数统计词频。
```
library(dplyr)
count <- freq(word) %>%
  arrange(desc(freq)) # 降序排列

head(count, 30) # 显示词频较高的前30个词
```

### 绘制词云

- 载入 **wordcloud2** 词云包
```
library(wordcloud2)
```

- `wordcloud2` 函数参数如下：

```
wordcloud2(data, size = 1, minSize = 0, gridSize =  0, fontFamily = NULL, fontWeight = 'normal', 
           color = 'random-dark', backgroundColor = "white", minRotation = -pi/4, maxRotation = pi/4, 
           rotateRatio = 0.4, shape = 'circle', ellipticity = 0.65, widgetsize = NULL) 
```

    - data：词云生成数据，包含具体词语以及频率

    - size：字体大小，默认为 1，一般来说该值越小，生成的形状轮廓越明显

    - fontFamily：字体，如微软雅黑

    - fontWeight：字体粗细，包含 normal、bold 以及 600

    - color：字体颜色，可以选择 random-dark、random-light以及具体颜色

    - backgroundColor：背景颜色，支持 R 语言中的常用颜色，如 gray、blcak

    - minRontatin 与 maxRontatin：字体旋转角度范围的最小值以及最大值

    - rotationRation：字体旋转比例，如设定为 1，则全部词语都会发生旋转

    - shape：词云形状选择，默认 circle (圆形)，还可选择 cardioid (苹果形或心形)，star (星形)，diamond (钻石)，triangle-forward (三角形)，triangle (三角形)，pentagon (五边形)

- 制作词云：
```
wordcloud2(count, size = 0.1, shape = "star", fontFamily = "宋体", color = "random-light", backgroundColor = "black")
```
- 自定义图片
```
picture <- system.file("examples/picture.jpg", package = "wordcloud2") # 图片放在wordclou2的sample中, 如 C:\Users\john\Documents\R\win-library\3.5\wordcloud2\examples

wordcloud2(count, size = 0.5, figPath = picture, fontFamily = "楷体", color = "black")
```
