## 方差分析 R 操作

方差分析的目的在于分析某一或某些解释变量对数值响应变量的影响作用, 一般来说有如下几个步骤：

#### Step 1 检验假设条件是否满足

- 独立性

- 正态性
    * `shapiro.test()`
    * Q-Q 图

- 方差齐性：
    * Bartlett 检验: `bartlett.test()`
    * Fligner-Killeen 检验: `fligner.test()`
    * Levene 检验: `leveneTest()`

#### Step 2 方差分析

基本格式：
```
aov(y ~ A, data = ) # y —— 因变量, A —— 自变量(类型为因子)
```

符号  | 用法
----- | ----------
\+    | y ~ A + B + C, 自变量 A、B、C 对 因变量 y 的影响
:     | y ~ A + B + A : B, A、B 及 A 与 B 的交互项对 y 的影响
\*    | y ~ A * B , 表示 y ~ A + B + A : B

#### Step 3 计算效应值(effect size)

- η*2 = SSA / SST (平衡设计)

- **pwr** 包做功效分析

#### Step 4 多重比较

- Tukey-Kramer 法: R 自带的 `TukeyHSD()` 函数(要求各处理组中样本容量相同)

- DTK 法：**DTK** 包的`DTK.test()`函数

- **multcomp** 包的`glht()`函数

下面主要详细介绍单因素方差分析与双因素方差分析的 R 实现过程, 并对单因素方差分析的结果进行解释. 另外, 还会简单介绍如何用 R 进行单因素协方差分析、重复测量方差分析以及单因素多元方差分析.

## 单因素方差分析

适用于只有一个类型变量的实验研究或观测研究

- 例: 以 **multcomp** 包中的 cholesterol 数据集为例(取自Westf all、Tobia、Rom、Hochberg, 1999), 50 个患者均接受降低胆固醇药物治疗 (trt) 五种疗法中的一种疗法. 其中三种治疗条件使用药物相同, 分别是20 mg 一天一次 (1time) 、10 mg 一天两次 (2times) 和5 mg 一天四次 (4times). 剩下的两种方式 (drugD 和 drugE) 代表候选药物. 哪种药物疗法降低胆固醇最多?


```{r}
cholesterol <- multcomp::cholesterol
attach(cholesterol)
```

#### 样本数量

```{r}
table(trt) 
```

#### 均值与标准差

```{r}
library(dplyr)
descrip <- cholesterol %>% group_by(trt) %>% summarise(mean = round(mean(response), 2),
                                            sd = round(sd(response), 2))
descrip 
```

#### 正态性检验

- `shapiro.test()`

各处理组逐次检验：

```{r}
time1 <- filter(cholesterol, trt == "1time") 
shapiro.test(time1$response)
```

```{r}
time2 <- filter(cholesterol, trt == "2times") 
shapiro.test(time2$response)
```

```{r}
time4 <- filter(cholesterol, trt == "4times") 
shapiro.test(time4$response)
```

```{r}
drugD <- filter(cholesterol, trt == "drugD") 
shapiro.test(drugD$response)
```

```{r}
drugE <- filter(cholesterol, trt == "drugE") 
shapiro.test(drugE$response)
```

可使用`lapply()`函数一次性检验：

```{r}
x <- split(cholesterol[,2], cholesterol[,1])
lapply(x, shapiro.test)
```

每组的 P 值均大于 0.05, 故不能拒绝正态性假设.

-  Q-Q 图

```{r}
library(car)
qqPlot(lm(response ~ trt, data = cholesterol), simulate = TRUE, main = "Q-Q Plot")
```

数据落在95%的置信区间范围内, 说明满足正态性假设.

#### 方差齐性检验

- Bartlett 检验: `bartlett.test()`

```{r}
bartlett.test(response ~ trt)
```

- Fligner-Killeen 检验: `fligner.test()`

```{r}
fligner.test(response ~ trt)
```

- Levene 检验: `leveneTest()`

```{r}
library(car)
leveneTest(response ~ trt)
```

三个不同方法的检验结果, P 值均很大, 说明不能拒绝方差齐性的假设.

#### 方差分析

- 方差分析表

```{r}
model_one <- aov(response ~ trt, data = cholesterol)
summary(model_one)
```

- 可视化呈现——带有置信区间的组均值图

```{r}
library(gplots)
par(mar = c(4, 4, 2, 2))
plotmeans(response ~ trt, xlab = "Treatment", ylab = "Response", main = "Mean Plot with 95% CI")
```

#### 计算效应量

- η*2 = SSA / SST 

```{r}
1351.4 / (1351.4 + 468.8)
```

- **pwr** 包

```{r}
library(pwr)
pwr.anova.test(k = 5, n = 10, sig.level = 0.05, power = 0.9)
```

*f 为该分析要达到 0.8 的功效, 并选择 0.05 的显著性水平时的效应值*

#### 多重比较

- Tukey-Kramer 法: R 自带的 `TukeyHSD()` 函数

```{r}
TukeyHSD(model_one)
```

*结果中的 diff 表示两两之间的样本均值差, lwr(lower) 表示置信水平为 95% 的均值差区间下限, upr(upper) 表示置信水平为 95% 的均值差区间上限, p adj 表示相应的 p 值.*

结果可视化呈现：

```{r}
par(las = 2, mar = c(5, 10, 4, 2))
plot(TukeyHSD(model_one))
```

*图中的虚直线横坐标为 0, 实横线表示对应的两组均值差之间的指定显著性水平的置信区间. 若置信区间与虚线相交, 则说明两组之间的差异在统计上不显著; 若不相交,则两组之间的差异在统计上显著.*

- DTK 法：**DTK** 包的 `DTK.test()` 函数

```{r}
library(DTK)
DTK.test(response, trt, a = 0.05)
```

*Lower CI 和 Upper CI 分别是默认显著性水平下的置信区间下限和上限. 若置信区间未包含 0, 则意味着两组之间的均值差异显著, 反之则不显著. *

结果可视化呈现：

```{r}
par(las = 2, mar = c(2, 10, 2, 2), mgp = c(8, 1, 0))
DTK.plot(DTK.test(response, trt, a = 0.05))
```

*DTK.plot() 默认有显著差异的两组均值差的置信区间用红色显示, 无显著差异的组均值差的置信区间则用黑色显示*

```{r}
detach(cholesterol)
```


## 双因素方差分析

- 例: 以基础安装包中的 ToothGrowth 数据集为例, 随机分配 60 只豚鼠, 分别采用两种喂食方法(橙汁或维生素 c), 各喂食方法中抗坏血酸含量有三种水平(0.5mg、1mg 或 2mg), 每种处理方式组合都被分配10只豚鼠, 牙齿长度(len)为因变量.

```{r}
attach(ToothGrowth)
```

##### 样本数量

```{r}
table(supp, dose) 
```

#### 均值与标准差

```{r}
library(dplyr)
toothgrowth <- mutate(ToothGrowth, Group = case_when(
  supp == "VC" & dose == 0.5 ~ "A",
  supp == "VC" & dose == 1 ~ "B",
  supp == "VC" & dose == 2 ~ "C",
  supp == "OJ" & dose == 0.5 ~ "D",
  supp == "OJ" & dose == 1 ~ "E",
  supp == "OJ" & dose == 2 ~ "F"
))
detach(ToothGrowth)
toothgrowth$dose <- as.factor(toothgrowth$dose)
```

```{r}
descrip_two <- toothgrowth %>% group_by(Group) %>% summarise(
  mean = round(mean(len), 2),
  sd = round(sd(len), 2)
)
descrip_two 
```

#### 正态性检验

- `shapiro.test()`

```{r}
y <- split(toothgrowth$len, toothgrowth$Group)
lapply(y, shapiro.test)
```

- 可以通过绘图进行直观的了解

```{r}
stripchart(toothgrowth$len ~ toothgrowth$Group, vertical = TRUE,
method = "stack", pch = 16, cex = 0.8)
```

#### 方差齐性检验

```{r}
bartlett.test(toothgrowth$len ~ toothgrowth$Group)
```

#### 方差分析

- 方差分析表

```{r}
model_two <- aov(len ~ supp * dose, data = toothgrowth)
summary(model_two)
```

- 结果可视化呈现

可以使用 `interaction.plot()` 函数:

```{r}
attach(toothgrowth)
par(mfrow = c(2, 1), mar = c(3, 5, 2, 2))
interaction.plot(dose, supp, len, type="b", col = c("red","blue"), pch=c(16, 18), las = 1)
interaction.plot(supp, dose, len, type="b", col = c("red","blue", "black"), pch=c(16, 18), las = 1)
```

也可以使用**HH**包中的 `interaction2wt()` 函数：

```{r}
library(HH)
interaction2wt(len ~ supp * dose)
```

*不仅展示了交互效应的结果 (左上和右下两幅图), 而且可以对主效应的效果有直观的了解 (右上和左下两幅图)*

#### 计算效应值

- 偏η*2(A) = SSA / SST

- 偏η*2(B) = SSB / SST

- 偏η*2(AB) = SSAB / SST

#### 简单效应检验

另外, 当交互效应显著时, 需要做进一步地简单效应检验, 命令如下:

```{r}
library(phia)
mod <- lm(len ~ supp * dose, data = toothgrowth)
VC.vs.OJ <- list(supp = c(1, -1))
dose0.5.vs.dose1 <- list(dose = c(1, -1, 0))
dose0.5.vs.dose2 <- list(dose = c(1, 0, -1))
dose1.vs.dose2 <- list(dose = c(0, 1, -1))
testInteractions(mod, custom = VC.vs.OJ, across = "dose", adjustment = "none")
testInteractions(mod, custom = dose0.5.vs.dose1, across = "supp", adjustment = "none")
testInteractions(mod, custom = dose0.5.vs.dose2, across = "supp", adjustment = "none")
testInteractions(mod, custom = dose1.vs.dose2, across = "supp", adjustment = "none")
```

#### 多重比较

当简单效应显著时，可做多重比较, 命令如下:

```{r}
TukeyHSD(model_two)
detach(toothgrowth)
```

*这一命令会出现三部分的结果. 首先是对因子 supp 的不同水平之间做多重比较; 其次是对因子 dose 的不同水平之间做多重比较; 再次是对 6 个处理组合之间的两两均值差做比较.*

## 单因素协方差分析(ANCOVA)

适用于自变量中包含一个或多个定量的协变量的实验研究或观测研究

- 例：以 **multcomp** 包中的 litter 数据具为例. 怀孕小鼠被分为四个组, 每组接受不同剂量(0、5、50 或 500)的药物处理. 产下幼崽的体重均值为因变量, 怀孕时间为协变量.

```{r}
library(multcomp)
attach(litter)
```

#### 样本数量

```{r}
table(litter$dose) 
```

#### 均值与标准差

```{r}
library(dplyr)
litter %>% group_by(dose) %>% summarise(mean = round(mean(weight), 2),
                                        sd = round(sd(weight), 2)) 
```

#### 回归斜率同质检验

ANCOVA 与 ANOVA 一样, 都需要正态性和方差齐性假设, 假设检验步骤同上. 另外, ANCOVA 还需检验回归斜率相同假设, 即在本例中, 有四个处理组通过怀孕时间来预测出生体重的回归斜率都相同的假设, 可通过交互效应来检验回归斜率的同质性. 若交互效应不显著则支持回归斜率相同假设.

```{r}
model_ancova <- aov(weight ~ gesttime * dose, data = litter)
summary(model_ancova)
```

#### 方差分析

- 方差分析表

```{r}
model_one_anc <- aov(weight ~ gesttime + dose, data = litter)
summary(model_one_anc)
```

- 结果可视化

可使用 **HH** 包中的 `ancova()` 函数绘制因变量、协变量和自变量之间的关系图.

```{r}
library(HH)
ancova(weight ~ gesttime + dose, data = litter)
```

#### 多重比较

- 可使用 **multcomp** 包中的 `glht()` 函数

```{r}
tukey <- glht(model_one_anc, linfct = mcp(dose = "Tukey"))
summary(tukey)
```

- 结果可视化呈现

```{r}
par(mar=c(5,4,6,2))
plot(cld(tukey, level = 0.05), col = "lightgrey")
detach(litter)
```


*在此箱线图中, 有相同字母的组, 表示均值差异不显著; 无相同字母的组, 则表示均值差异显著.*


## 重复测量方差分析

主要介绍包含一个组间和一个组内因子的重复测量方差分析

- 例: 以基础安装包中的 CO2 数据集为例. 研究在某浓度二氧化碳的环境中，寒带植物与非寒带植物的光合作用率的比较. 因变量是二氧化碳吸收量(uptake), 单位为 ml/L, 自变量是植物类型 Type (魁北克 VS. 密西西比)和七种水平（95~1000 umol/m ^ 2 sec）的二氧化碳浓度(conc). 另外, Type 是组间因子, conc 是组内因子.

```{r}
library(dplyr)
co2 <- filter(CO2, Treatment == "chilled") # 只考虑寒带植物
co2$conc <- as.factor(co2$conc) 
```

重复测量方差分析仍需满足正态性与方差齐性假设, 可自行利用前述方法进行验证. 这里仅介绍重复测量方差分析需要满足的另一假定——球对称假定或球形性假定进行检验.

#### 球形性检验

可使用 `mauchly.test()` 函数进行检验, 其调用格式为:

```
mauchly.test(mlm, X = ~ 1)
```

```{r}
 arrange(co2, conc, Type) %>% 
 mutate(Group = rep(LETTERS[1:14], each = 3),
            id = case_when(
   Plant == "Qc1" | Plant == "Mc1" ~ "a",
   Plant == "Qc2" | Plant == "Mc2" ~ "b",
   Plant == "Qc3" | Plant == "Mc3" ~ "c")) -> x
```

```
x <- select(x, id, Group, uptake)
library(tidyr)
y <- spread(x, Group, uptake)
y1 <- y[, 2:15] 
mlm <- lm(as.matrix(y1) ~ 1)
mauchly.test(mlm, X = ~ 1)
```

#### 各组均值与标准差

```{r}
x %>% group_by(Group) %>% summarise(
  mean = round(mean(uptake), 2),
  sd = round(sd(uptake), 2))
```

#### 方差分析

- 方差分析表

```{r}
model_rep <- aov(uptake ~ conc * Type + Error(Plant/conc), data = co2)
summary(model_rep)
```

- 结果可视化呈现

```{r}
par(las=2)
with(co2, interaction.plot(conc, Type, uptake, type="b", col=c("red","blue"), pch=c(16,17), main="Interaction Plot for Plant Type and Concentration"))
```

## 单因素多元方差分析

当因变量个数大于一个时，可以用多元方差分析(MANOVA)来进行研究.

- 例: 以 **MASS** 包中的 UScereal 数据集为例(Venables, Ripley(1999)),我们将研究美国谷物中的卡路里(calories)、脂肪(fat)和糖含量(sugars)是否会因为储存架位置(shelf)的不同而发生变化; 其中 1 代表底层货架, 2 代表中层货架, 3 代表顶层货架. 卡路里、脂肪和糖含量是因变量, 货架是三水平(1、2、3)的自变量.

```{r}
library(MASS)
attach(UScereal)
shelf <- as.factor(shelf)
```

#### 各组均值

```{r}
y <- cbind(calories, fat, sugars) 
options(digits = 3)
aggregate(y, by = list(shelf), FUN = mean)
```

#### 方差分析

```{r}
model_mult <- manova(y ~ shelf)
summary(model_mult)
```

`anova()`函数能对组间差异进行多元检验. 上面 F 值显著, 说明三个组的营养成分测量值不同. 由于多元检验是显著的, 可以使用 `summary.aov()` 函数对每一个变量做单因素方差分析. 命令如下:

```{r}
summary.aov(model_mult)
detach(UScereal)
```

另外, 单因素多元方差分析需要满足两个假设: 多元正态性与方差——协方差矩阵同质性, 读者可自行查阅资料完成.

