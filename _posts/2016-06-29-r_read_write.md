---
layout: post
title: R 语言常用包（1）
subtitle: 读取、输出与向量化操作
date:       2016-06-29
author:     "Scott"
header-img: ""
catalog:    true
tags:
    - R
---

## 数据读取与输出

#### 读取 read.table

读取普通数据可用 read.table，有6个参数比较重要：

- file：文件路径
- sep：文件的分隔符，缺省无
- skip：跳过开始的 skip 行开始读取
- header：是否将第一行读取为列名，缺省 FALSE
- nrows：读取的行数
- fill：将缺失数据定为 NA, 缺省并无指定

#### 高阶包 readr

也可用 readr 包读取表格型数据，不仅速度快，且能直接读取为 tbl 格式。参考 [readr 介绍](http://www.xueqing.tv/cms/article/102)。


```r
library(readr)
```

read_csv 和 read_tsv 分别读取分隔符为逗号和制表符的数据，read_csv2 则是读取分号分割的数据，read_dlim 读取自定义的。

函数定义：

```r
read_delim(file, delim, quote = "\"", escape_backslash = FALSE,
  escape_double = TRUE, col_names = TRUE, col_types = NULL,
  locale = default_locale(), na = c("", "NA"), comment = "", skip = 0,
  n_max = -1, progress = interactive())
```

write_csv 用于写入数据，不写入行名。

函数定义

```r
write_csv(x, path, na = "NA", append = FALSE, col_names = !append)
```

## 向量化操作之 apply 家族

- 参考1：[掌握R语言中的apply函数族](http://blog.fens.me/r-apply/)
- 参考2：[R Grouping functions: sapply vs. lapply vs. apply. vs. tapply vs. by vs. aggregate](http://stackoverflow.com/questions/3505701/r-grouping-functions-sapply-vs-lapply-vs-apply-vs-tapply-vs-by-vs-aggrega)
- 参考3：[R语言apply家族函数的用法及其比较](http://www.xueqing.tv/cms/article/158)

#### 循环迭代之 lapply/sapply

**sapply，lapply 的简化版**

第一参数是 X，官方解释说，可接收向量，list，或表达式对象，其他对象将被强制按 list，第二个参数则是函数，返回一个向量或矩阵。


```r
func <- function(x){
  if (x %% 2 == 0){
    ret <- 'even'
  }
  else {
    ret <- 'odd'
  }
  return(ret)
}
vec <- round(runif(4)*100)
func(vec)
#> Warning in if (x%%2 == 0) {: the condition has length > 1 and only the
#> first element will be used
#> [1] "odd"
vec
#> [1] 77  5 11 75
sapply(vec, func)
#> [1] "odd" "odd" "odd" "odd"
```

**lapply**

跟 sapply 的区别在于返回的是列表


```r
lapply(vec, func)
#> [[1]]
#> [1] "odd"
#> 
#> [[2]]
#> [1] "odd"
#> 
#> [[3]]
#> [1] "odd"
#> 
#> [[4]]
#> [1] "odd"
mylist <- as.list(iris[, 1:4]) # 不转list，也是强按
yourdata <- iris[, 1:4]

sapply(mylist, mean) # 还可以对列表进行计算
#> Sepal.Length  Sepal.Width Petal.Length  Petal.Width 
#>     5.843333     3.057333     3.758000     1.199333
lapply(mylist, mean) # return a list
#> $Sepal.Length
#> [1] 5.843333
#> 
#> $Sepal.Width
#> [1] 3.057333
#> 
#> $Petal.Length
#> [1] 3.758
#> 
#> $Petal.Width
#> [1] 1.199333

myfunc <- function(x) {
  ret <- c(mean(x),sd(x))
  return(ret)
}
result <- lapply(mylist, myfunc)
result
#> $Sepal.Length
#> [1] 5.8433333 0.8280661
#> 
#> $Sepal.Width
#> [1] 3.0573333 0.4358663
#> 
#> $Petal.Length
#> [1] 3.758000 1.765298
#> 
#> $Petal.Width
#> [1] 1.1993333 0.7622377

t(sapply(result, '[')) # list数据转 data.frame
#>                  [,1]      [,2]
#> Sepal.Length 5.843333 0.8280661
#> Sepal.Width  3.057333 0.4358663
#> Petal.Length 3.758000 1.7652982
#> Petal.Width  1.199333 0.7622377
```

#### 分组运算

**apply**

第一个参数接收数组或矩阵，MARGIN 参数按行或列计算


```r
set.seed(1)
vec <- round(runif(12)*100)
mat <- matrix(vec, nrow = 3, ncol = 4)
apply(mat, MARGIN = 1, sum) # 1为行，2为列
#> [1] 218 144 228
```

**tapply**

tapply用于分组的循环计算，通过INDEX参数可以把数据集X进行分组，相当于group by的操作。


```r
head(iris)
#>   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
#> 1          5.1         3.5          1.4         0.2  setosa
#> 2          4.9         3.0          1.4         0.2  setosa
#> 3          4.7         3.2          1.3         0.2  setosa
#> 4          4.6         3.1          1.5         0.2  setosa
#> 5          5.0         3.6          1.4         0.2  setosa
#> 6          5.4         3.9          1.7         0.4  setosa
with(iris, tapply(Sepal.Length, list(Species), mean))
#>     setosa versicolor  virginica 
#>      5.006      5.936      6.588
```

**aggregate**

比较友好，返回的是数据框


```r
aggregate(iris$Sepal.Length, list(iris$Species), mean)
#>      Group.1     x
#> 1     setosa 5.006
#> 2 versicolor 5.936
#> 3  virginica 6.588
```

#### 多参数运算

**mapply**

这个函数目前理解的还不是很透彻，待补。

```r
mapply(rep, times = 1:4, x = 4:1)
#> [[1]]
#> [1] 4
#> 
#> [[2]]
#> [1] 3 3
#> 
#> [[3]]
#> [1] 2 2 2
#> 
#> [[4]]
#> [1] 1 1 1 1
```

## 数据整理、转换
#### reshape2


```r
subdata <-iris[,4:5]
head(subdata)
#>   Petal.Width Species
#> 1         0.2  setosa
#> 2         0.2  setosa
#> 3         0.2  setosa
#> 4         0.2  setosa
#> 5         0.2  setosa
#> 6         0.4  setosa
data_w <- unstack(subdata)
colMeans(data_w)
#>     setosa versicolor  virginica 
#>      0.246      1.326      2.026
```

实践中这种单纯的长宽格式并不多见，更常见的是直接一步得到结果，所以就需要强大的 reshape2 包

参考[An Introduction to reshape2](http://seananderson.ca/2013/10/19/reshape.html)

加载包和改数据名

```r
library(reshape2)
names(airquality) <- tolower(names(airquality))
head(airquality)
#>   ozone solar.r wind temp month day
#> 1    41     190  7.4   67     5   1
#> 2    36     118  8.0   72     5   2
#> 3    12     149 12.6   74     5   3
#> 4    18     313 11.5   62     5   4
#> 5    NA      NA 14.3   56     5   5
#> 6    28      NA 14.9   66     5   6
```

**melt 熔化转长格式**

```r
aql <- melt(airquality)
#> No id variables; using all as measure variables
tail(aql)
#>     variable value
#> 913      day    25
#> 914      day    26
#> 915      day    27
#> 916      day    28
#> 917      day    29
#> 918      day    30
head(aql)
#>   variable value
#> 1    ozone    41
#> 2    ozone    36
#> 3    ozone    12
#> 4    ozone    18
#> 5    ozone    NA
#> 6    ozone    28
```

设定不需要变 value 的变量

```r
aql <- melt(airquality,id.vars = c('month', 'day'))
names(airquality)
#> [1] "ozone"   "solar.r" "wind"    "temp"    "month"   "day"
head(aql)
#>   month day variable value
#> 1     5   1    ozone    41
#> 2     5   2    ozone    36
#> 3     5   3    ozone    12
#> 4     5   4    ozone    18
#> 5     5   5    ozone    NA
#> 6     5   6    ozone    28
```

改名

```r
aql <- melt(airquality, id.vars = c("month", "day"),
            variable.name = "climate_variable",
            value.name = "climate_value")
head(aql)
#>   month day climate_variable climate_value
#> 1     5   1            ozone            41
#> 2     5   2            ozone            36
#> 3     5   3            ozone            12
#> 4     5   4            ozone            18
#> 5     5   5            ozone            NA
#> 6     5   6            ozone            28
head(airquality)
#>   ozone solar.r wind temp month day
#> 1    41     190  7.4   67     5   1
#> 2    36     118  8.0   72     5   2
#> 3    12     149 12.6   74     5   3
#> 4    18     313 11.5   62     5   4
#> 5    NA      NA 14.3   56     5   5
#> 6    28      NA 14.9   66     5   6
```

**dcast 重铸，长转宽**

```r
aql <- melt(airquality, id.vars = c("month", "day"))
aqw  <- dcast(aql, month + day ~ variable)
head(aql)
#>   month day variable value
#> 1     5   1    ozone    41
#> 2     5   2    ozone    36
#> 3     5   3    ozone    12
#> 4     5   4    ozone    18
#> 5     5   5    ozone    NA
#> 6     5   6    ozone    28
head(aqw)
#>   month day ozone solar.r wind temp
#> 1     5   1    41     190  7.4   67
#> 2     5   2    36     118  8.0   72
#> 3     5   3    12     149 12.6   74
#> 4     5   4    18     313 11.5   62
#> 5     5   5    NA      NA 14.3   56
#> 6     5   6    28      NA 14.9   66
```

不足的id.var 会出错，会把值的数量统计出来

```r
dcast(aql, month ~ variable)
#> Aggregation function missing: defaulting to length
#>   month ozone solar.r wind temp
#> 1     5    31      31   31   31
#> 2     6    30      30   30   30
#> 3     7    31      31   31   31
#> 4     8    31      31   31   31
#> 5     9    30      30   30   30
```

**fun,加上集合函数就能按正确方式集合了。**

```r
dcast(aql, month ~ variable, fun.aggregate = mean,
      na.rm = TRUE)
#>   month    ozone  solar.r      wind     temp
#> 1     5 23.61538 181.2963 11.622581 65.54839
#> 2     6 29.44444 190.1667 10.266667 79.10000
#> 3     7 59.11538 216.4839  8.941935 83.90323
#> 4     8 59.96154 171.8571  8.793548 83.96774
#> 5     9 31.44828 167.4333 10.180000 76.90000
```

#### tidyr
新建一列数据

```r
mtcars$car <- rownames(mtcars)
colnames(mtcars)
#>  [1] "mpg"  "cyl"  "disp" "hp"   "drat" "wt"   "qsec" "vs"   "am"   "gear"
#> [11] "carb" "car"
mtcars <- mtcars[, c(12, 1:11)]
head(mtcars)
#>                                 car  mpg cyl disp  hp drat    wt  qsec vs
#> Mazda RX4                 Mazda RX4 21.0   6  160 110 3.90 2.620 16.46  0
#> Mazda RX4 Wag         Mazda RX4 Wag 21.0   6  160 110 3.90 2.875 17.02  0
#> Datsun 710               Datsun 710 22.8   4  108  93 3.85 2.320 18.61  1
#> Hornet 4 Drive       Hornet 4 Drive 21.4   6  258 110 3.08 3.215 19.44  1
#> Hornet Sportabout Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0
#> Valiant                     Valiant 18.1   6  225 105 2.76 3.460 20.22  1
#>                   am gear carb
#> Mazda RX4          1    4    4
#> Mazda RX4 Wag      1    4    4
#> Datsun 710         1    4    1
#> Hornet 4 Drive     0    3    1
#> Hornet Sportabout  0    3    2
#> Valiant            0    3    1
```

**gather 聚合**

```r
library(tidyr)
#> Warning: package 'tidyr' was built under R version 3.2.5
#> 
#> Attaching package: 'tidyr'
#> The following object is masked from 'package:reshape2':
#> 
#>     smiths
mtcarsNew <- mtcars %>%
  gather(attribute, value, -car) #不仅聚合，且命名了。
head(mtcarsNew)
#>                 car attribute value
#> 1         Mazda RX4       mpg  21.0
#> 2     Mazda RX4 Wag       mpg  21.0
#> 3        Datsun 710       mpg  22.8
#> 4    Hornet 4 Drive       mpg  21.4
#> 5 Hornet Sportabout       mpg  18.7
#> 6           Valiant       mpg  18.1
tail(mtcarsNew)
#>                car attribute value
#> 347  Porsche 914-2      carb     2
#> 348   Lotus Europa      carb     2
#> 349 Ford Pantera L      carb     4
#> 350   Ferrari Dino      carb     6
#> 351  Maserati Bora      carb     8
#> 352     Volvo 142E      carb     2
```

gather mpg:gear 之间的列

```r
mtcarsNew <- mtcars %>%
  gather(attribute, value, mpg:gear)
```

**spread 铺开**

```r
mtcarsWide <- mtcarsNew %>%
  spread(attribute, value)
head(mtcarsWide)
#>                  car carb am cyl disp drat gear  hp  mpg  qsec vs    wt
#> 1        AMC Javelin    2  0   8  304 3.15    3 150 15.2 17.30  0 3.435
#> 2 Cadillac Fleetwood    4  0   8  472 2.93    3 205 10.4 17.98  0 5.250
#> 3         Camaro Z28    4  0   8  350 3.73    3 245 13.3 15.41  0 3.840
#> 4  Chrysler Imperial    4  0   8  440 3.23    3 230 14.7 17.42  0 5.345
#> 5         Datsun 710    1  1   4  108 3.85    4  93 22.8 18.61  1 2.320
#> 6   Dodge Challenger    2  0   8  318 2.76    3 150 15.5 16.87  0 3.520
```

**unite 合并**

```r
set.seed(1)
date <- as.Date('2016-06-01') + 0:14

hour <- sample(1:24, 15)
min <- sample(1:60, 15)
second <- sample(1:60, 15)
event <- sample(letters, 15)
df <- data.frame(date, hour, min, second, event)
head(df)
#>         date hour min second event
#> 1 2016-06-01    7  30     29     u
#> 2 2016-06-02    9  43     36     a
#> 3 2016-06-03   13  58     60     l
#> 4 2016-06-04   20  22     11     q
#> 5 2016-06-05    5  44     47     p
#> 6 2016-06-06   18  52     37     k

df1 <- df %>%
  unite(datehour, date, hour, sep = ' ') %>%
  unite(datetime, datehour, min, second, sep = ':')
df1
#>               datetime event
#> 1   2016-06-01 7:30:29     u
#> 2   2016-06-02 9:43:36     a
#> 3  2016-06-03 13:58:60     l
#> 4  2016-06-04 20:22:11     q
#> 5   2016-06-05 5:44:47     p
#> 6  2016-06-06 18:52:37     k
#> 7  2016-06-07 19:12:43     r
#> 8   2016-06-08 12:35:6     i
#> 9   2016-06-09 11:7:38     e
#> 10  2016-06-10 1:14:21     b
#> 11  2016-06-11 3:20:42     w
#> 12  2016-06-12 14:1:32     t
#> 13 2016-06-13 23:19:52     h
#> 14 2016-06-14 21:41:26     s
#> 15  2016-06-15 8:16:25     o
```

**separate 拆分**

```r
df2 <- df1 %>%
  separate(datetime, c('date', 'time'), sep = ' ') %>%
  separate(time, c('hour', 'min', 'second'), sep = ':')
df2
#>          date hour min second event
#> 1  2016-06-01    7  30     29     u
#> 2  2016-06-02    9  43     36     a
#> 3  2016-06-03   13  58     60     l
#> 4  2016-06-04   20  22     11     q
#> 5  2016-06-05    5  44     47     p
#> 6  2016-06-06   18  52     37     k
#> 7  2016-06-07   19  12     43     r
#> 8  2016-06-08   12  35      6     i
#> 9  2016-06-09   11   7     38     e
#> 10 2016-06-10    1  14     21     b
#> 11 2016-06-11    3  20     42     w
#> 12 2016-06-12   14   1     32     t
#> 13 2016-06-13   23  19     52     h
#> 14 2016-06-14   21  41     26     s
#> 15 2016-06-15    8  16     25     o
```

总的来说，tidyr 包比reshape语法更为简洁易懂， 更加方便。
