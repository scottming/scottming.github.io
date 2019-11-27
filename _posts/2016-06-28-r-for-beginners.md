---
layout: post
title: R 语言数据结构
subtitle:
date:       2016-06-28
author:     "Scott"
header-img: ""
catalog:    true
tags:
    - R
---

## 向量

R 里面的向量看起来像 Python 的 list，但又不是 list，更像是Python 里一维的数组。

**向量入门**

基础

```r
vec <- c(1, 2, 3, 6, 5, 4)
vec[c(1, 2, 4)]
#> [1] 1 2 6
class(vec)
#> [1] "numeric"
vec[1:2] # 末尾包含
#> [1] 1 2
```

Python 代码,除了用列表推导式之外还可以直接用 Numpy 实现

```python
import numpy as np
a= np.array([1, 2, 3, 6, 5, 4])
print a[[0, 1, 3]]
print type(a)
```

```
## [1 2 6]
## <type 'numpy.ndarray'>
```

创建向量 类 Python range

```r
a <- seq(10)
a
#>  [1]  1  2  3  4  5  6  7  8  9 10
b <- seq(10, 13)
b
#> [1] 10 11 12 13
temp <- c(1, 2, 4, 0)
temp * b
#> [1] 10 22 48  0
```

生成有规律的向量， 类 np.linspace

```r
vec <- seq(1, 100, length.out = 10) # 还有个long参数也非常有用。
vec
#>  [1]   1  12  23  34  45  56  67  78  89 100
vec[-4] #这点跟 Python 很不一样
#> [1]   1  12  23  45  56  67  78  89 100

seq(from=2, to=1000, length.out = 10)
#>  [1]    2.0000  112.8889  223.7778  334.6667  445.5556  556.4444  667.3333
#>  [8]  778.2222  889.1111 1000.0000
seq(from=2, to=1000, length=10)
#>  [1]    2.0000  112.8889  223.7778  334.6667  445.5556  556.4444  667.3333
#>  [8]  778.2222  889.1111 1000.0000

group1 <- rep(1:3, times = c(8, 10, 9))
group2 <- factor(group1) # 转换成因子
group1
#>  [1] 1 1 1 1 1 1 1 1 2 2 2 2 2 2 2 2 2 2 3 3 3 3 3 3 3 3 3
class(group1)
#> [1] "integer"
class(group2)
#> [1] "factor"
length(group1) # as Py len
#> [1] 27
```

随机向量

```r
vec_random1 <- runif(5) # 0-1
vec_random1
#> [1] 0.97060734 0.08917549 0.67931865 0.55040742 0.01648558
vec_random2 <- sample(c('A', 'B'), size = 10, replace = TRUE) # 随机字符向量
vec_random2
#>  [1] "B" "A" "A" "A" "A" "A" "B" "B" "B" "A"
vector1 <- numeric(10)
vector1 # empty vector
#>  [1] 0 0 0 0 0 0 0 0 0 0
```

逻辑判断

```r
temp[1:3]
#> [1] 1 2 4
temp[c(TRUE, TRUE, FALSE, FALSE)]
#> [1] 1 2
temp[temp > 1]
#> [1] 2 4
```


## 矩阵
矩阵是二维的容器，可包含数值、逻辑、字符等数据

生成矩阵

```r
vector <- 1:12
class(vector)
#> [1] "integer"
my_matrix <- matrix(vector, nrow = 3, ncol = 4, byrow = FALSE)
dim(my_matrix)
#> [1] 3 4
vector
#>  [1]  1  2  3  4  5  6  7  8  9 10 11 12
my_matrix
#>      [,1] [,2] [,3] [,4]
#> [1,]    1    4    7   10
#> [2,]    2    5    8   11
#> [3,]    3    6    9   12

vector1 <- vector2 <- vector3 <- runif(3)
my_matrix <- cbind(vector1, vector2, vector3) # 一致的向量类型进行合并
my_matrix
#>        vector1   vector2   vector3
#> [1,] 0.7883996 0.7883996 0.7883996
#> [2,] 0.3369505 0.3369505 0.3369505
#> [3,] 0.3207653 0.3207653 0.3207653
round(my_matrix*10, digits = 2) # 取2位
#>      vector1 vector2 vector3
#> [1,]    7.88    7.88    7.88
#> [2,]    3.37    3.37    3.37
#> [3,]    3.21    3.21    3.21
```

取子集和计算

```r
my_mat <- matrix(c(8, 3, 4, 1, 5, 9, 6, 7, 2), ncol = 3)
print(my_mat)
#>      [,1] [,2] [,3]
#> [1,]    8    1    6
#> [2,]    3    5    7
#> [3,]    4    9    2

my_mat[1,1] + my_mat[1, 2] + my_mat[1,3]
#> [1] 15
sum(my_mat[1,])
#> [1] 15
rowSums(my_mat)
#> [1] 15 15 15
colSums(my_mat)
#> [1] 15 15 15
sum(diag(my_mat))
#> [1] 15

class(my_mat[1,]) # 退化成向量了
#> [1] "numeric"
my_mat[1, , drop=FALSE] # 保留矩阵属性
#>      [,1] [,2] [,3]
#> [1,]    8    1    6
my_mat[my_mat <= 5] <- 0 # 改变赋值
my_mat
#>      [,1] [,2] [,3]
#> [1,]    8    0    6
#> [2,]    0    0    7
#> [3,]    0    9    0

# ifelse 函数
my_mat <- matrix(c(8, 3, 4, 1, 5, 9, 6, 7, 2), ncol = 3)
ifelse(my_mat > 0.5, 1, 0)
#>      [,1] [,2] [,3]
#> [1,]    1    1    1
#> [2,]    1    1    1
#> [3,]    1    1    1
```

## 数据框

优点在于不同向量的数据类型可以不一样，但各列的长度必须一致


```r
city <- c('A', 'B', 'C', 'D')
temp <- c(27, 29, 23, 14)
data <- data.frame(city, temp) # 对于 list，Py 不能直接这么导，会把整数向量变为索引，得用 dir 的方式
data
#>   city temp
#> 1    A   27
#> 2    B   29
#> 3    C   23
#> 4    D   14
data[, 1] # Py data.ix[:,0] | data.iloc[:,0]
#> [1] A B C D
#> Levels: A B C D
data$temp
#> [1] 27 29 23 14
class(data$city) # 本是字符，自动转了因子，若不想转，课用 stringAsFactors = FALSE 设定
#> [1] "factor"
```

组合索引

```r
data[data$temp > mean(data$temp), ]
#>   city temp
#> 1    A   27
#> 2    B   29
# data['temp', ] # empty，这是提取行的语法
data[, 'city'] # 提取列
#> [1] A B C D
#> Levels: A B C D
data$temp > mean(data$temp)
#> [1]  TRUE  TRUE FALSE FALSE
with(data, data[temp > mean(temp), ]) # 直接操作列名,省略 $
#>   city temp
#> 1    A   27
#> 2    B   29
with(data, data[temp > mean(temp), 'city']) 
#> [1] A B
#> Levels: A B C D
```

熟悉数据框最快的办法

```r
summary(data) # as Py describe
#>  city       temp      
#>  A:1   Min.   :14.00  
#>  B:1   1st Qu.:20.75  
#>  C:1   Median :25.00  
#>  D:1   Mean   :23.25  
#>        3rd Qu.:27.50  
#>        Max.   :29.00
dim(data) # as Py data.shape
#> [1] 4 2
head(data) # 看前6行
#>   city temp
#> 1    A   27
#> 2    B   29
#> 3    C   23
#> 4    D   14
#data.head(1) # 不能这么干
head(data, n = 1L)
#>   city temp
#> 1    A   27
str(data) # 返回数据结构
#> 'data.frame':	4 obs. of  2 variables:
#>  $ city: Factor w/ 4 levels "A","B","C","D": 1 2 3 4
#>  $ temp: num  27 29 23 14
```

数据框的排序

```r
order(data$temp) # 返回数据的索引号
#> [1] 4 3 1 2
data[order(data$temp), ]
#>   city temp
#> 4    D   14
#> 3    C   23
#> 1    A   27
#> 2    B   29
data[order(data$temp, decreasing = T), ][1:2, ] # 反序
#>   city temp
#> 2    B   29
#> 1    A   27
```

## 列表

```r
data_list <- list(temp = temp, city = city)
print(data_list)
#> $temp
#> [1] 27 29 23 14
#> 
#> $city
#> [1] "A" "B" "C" "D"

data_list$mat <- my_mat
data_list$data <- data
names(data_list)
#> [1] "temp" "city" "mat"  "data"
class(data_list[3])
#> [1] "list"
data_list[3]
#> $mat
#>      [,1] [,2] [,3]
#> [1,]    8    1    6
#> [2,]    3    5    7
#> [3,]    4    9    2
data_list[[3]] # 没有 name
#>      [,1] [,2] [,3]
#> [1,]    8    1    6
#> [2,]    3    5    7
#> [3,]    4    9    2
class(data_list[[3]])
#> [1] "matrix"
```

## 特殊对象

缺失值和空值

```r
temp <- c(27, 29, 23, 14, NA)
mean(temp)
#> [1] NA
mean(temp, na.rm = TRUE)
#> [1] 23.25
temp <- c(27, 29, 23, 14, NULL)
data_list <- NULL # 快速删除一列
mean(temp) # TRUE
#> [1] 23.25
```

连接

```r
textcon = textConnection('output', 'w')
sink(textcon) # 开
x <- runif(10)
summary(x)
#>    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
#>  0.1157  0.2999  0.4472  0.4598  0.5630  0.9581
print('这话不显示了，写入了 output 对象了')
#> [1] "这话不显示了，写入了 output 对象了"
sink() # 关闭控制台转换
print(output)
#> character(0)

showConnections()
#>   description class            mode text   isopen   can read can write
#> 4 "output"    "textConnection" "wr" "text" "opened" "no"     "yes"    
#> 5 "output"    "textConnection" "w"  "text" "opened" "no"     "yes"
class(textcon)
#> [1] "textConnection" "connection"
close(textcon)
```

公式

```r
n <- 1:50
xvar <- paste0('x', n )
right <- paste(xvar, collapse = ' + ')
left <- 'y~'
my_formula <- paste(left, right)
my_formula <- as.formula(my_formula)
class(my_formula)
#> [1] "formula"

left
#> [1] "y~"
```

表达式

```r
ex <- expression(x <- seq(1, 10, 2))
print(ex)
#> expression(x <- seq(1, 10, 2))
class(ex)
#> [1] "expression"
eval(ex)
print(x)
#> [1] 1 3 5 7 9

tex <- c('z<-seq(1, 10, 2)', 'print(z)' )
eval(parse(text = tex))
#> [1] 1 3 5 7 9
```

环境变量

```r
ls()
#>  [1] "a"           "args"        "b"           "city"        "data"       
#>  [6] "data_list"   "dir"         "ex"          "filename"    "group1"     
#> [11] "group2"      "left"        "my_formula"  "my_mat"      "my_matrix"  
#> [16] "n"           "output"      "right"       "temp"        "tex"        
#> [21] "textcon"     "vec"         "vec_random1" "vec_random2" "vector"     
#> [26] "vector1"     "vector2"     "vector3"     "x"           "xvar"       
#> [31] "z"
env1 <- new.env()

assign("x1", 1:5, envir = env1)
ls(envir = env1)
#> [1] "x1"
get('x1', envir = env1) # 为啥没法直接用 x1 取呢？
#> [1] 1 2 3 4 5

exists('x1', envir = env1)
#> [1] TRUE
rm('x1', envir = env1)
```

函数

```r
exp(1)
#> [1] 2.718282

myfunc <- function(r){
  area <- pi*r^2
  return(area)
} # 内部可以调用 global value
print(myfunc(4)) 
#> [1] 50.26548

myfunc
#> function(r){
#>   area <- pi*r^2
#>   return(area)
#> }
```


