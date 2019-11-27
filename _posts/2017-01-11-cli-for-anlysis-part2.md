---
layout: post
title: 数据分析中的命令行工具（2）
subtitle:   做一个优雅的调包侠--擅用第三方包
date:       2017-01-11
author:     "Scott"
header-img: "img/160403-debian.png"
catalog:    true
tags:
    - Linux
    - Python
---

本文不以具体工具为组块，而按数据处理流程介绍各类命令行工具的用法，看本文前，建议提前下载以下工具。

* CSV 系列工具
    + [dilshod/xlsx2csv: Convert xslx to csv, it is fast, and works for huge xlsx files](https://github.com/dilshod/xlsx2csv)
    + [getsheets](https://gist.github.com/scottming/99c09685360376d4cac2de7c891e8050)
    + [csvkit 1.0.1 — csvkit 1.0.1 documentation](https://csvkit.readthedocs.io/en/1.0.1/)
    + [csv2xlsx](https://gist.github.com/konrad/4154786)
* [robdmc/pandashells: Bringing the python data stack to the shell prompt](https://github.com/robdmc/pandashells)
* [《命令行中的数据科学》系列工具](https://github.com/jeroenjanssens/data-science-at-the-command-line/tree/master/tools)
* [文中所用数据集](https://github.com/jeroenjanssens/data-science-at-the-command-line/tree/master/book/.data)

## 1. 格式转换

各类数据格式中，最让人喜欢的无疑是 csv 格式，转换方便，可读性强，第三方处理包多。但你的老板和同事给你的不一定是 csv 格式，有可能是 Excel，SQL，Json 等等，这时有些转换工具就能派上用场了。

#### 转换 Excel 文件

xlsx2csv 把 xlsx 快速转为 csv 并在终端打印

```
$ xlsx2csv iris_mult_sheet.xlsx | head | csvlook
|---------------------+-------------+--------------+-------------+--------------|
|  sepal_length       | sepal_width | petal_length | petal_width | species      |
|---------------------+-------------+--------------+-------------+--------------|
|  5.0999999999999996 | 3.5         | 1.4          | 0.2         | Iris-setosa  |
|  4.9000000000000004 | 3           | 1.4          | 0.2         | Iris-setosa  |
|  4.7                | 3.2         | 1.3          | 0.2         | Iris-setosa  |
|  4.5999999999999996 | 3.1         | 1.5          | 0.2         | Iris-setosa  |
|  5                  | 3.6         | 1.4          | 0.2         | Iris-setosa  |
|  5.4                | 3.9         | 1.7          | 0.4         | Iris-setosa  |
|  4.5999999999999996 | 3.4         | 1.4          | 0.3         | Iris-setosa  |
|  5                  | 3.4         | 1.5          | 0.2         | Iris-setosa  |
|  4.4000000000000004 | 2.9         | 1.4          | 0.2         | Iris-setosa  |
|---------------------+-------------+--------------+-------------+--------------|
```

有时可能会遇到所需的数据不再 Excel 的第一个 Sheet，这时可用我写的 getsheets 小脚本查看 Excel 的 sheet-names 或 直接把所有 sheet 提取出来。

```
$ python getsheets.py -n iris_mult_sheet.xlsx
  1: Sheet1
  2: Sheet2
  3: Sheet3
  4: Sheet4
  5: Sheet5
  6: Sheet6
  7: Sheet7
  8: Sheet8
  9: Sheet9
 10: Sheet10
 11: Sheet11

# 获取所有 Sheet
$ python getsheets.py iris_mult_sheet.xlsx
Sheet1.xlsx Done!
Sheet2.xlsx Done!
Sheet3.xlsx Done!
Sheet4.xlsx Done!
Sheet5.xlsx Done!
Sheet6.xlsx Done!
Sheet7.xlsx Done!
Sheet8.xlsx Done!
Sheet9.xlsx Done!
Sheet10.xlsx Done!
Sheet11.xlsx Done!

All Done!
```

获取 Sheet 名称或id后，再用 xlsx2csv 转换

```
$ xlsx2csv -s 2 iris_mult_sheet.xlsx
a,b
1,2
```
这便是第二个 Sheet 的内容。


另外，也有可能你获取的 CSV 文件有编码问题，这时，可用 [chardet/chardet: Python 2/3 compatible character encoding detector.](https://github.com/chardet/chardet) 检验编码，防止非 UTF-8/ASCII 编码无法处理。

```
$ chardetect tips.csv 
tips.csv: ascii with confidence 1.0
```

ASCII 格式一般是不需要继续转换的，如果是其他格式，可用 iconv 转换

```
$ iconv -f iSO-8859-2 -t UTF-8 otherfile
```

#### 其他格式转换

有时同事需要 Excel 文件，同理 csv2xlsx 一键转换即可，

```
csv2xlsx iris.csv
```

[csvkit](https://github.com/wireservice/csvkit/tree/1.0.1/csvkit/utilities) 工具包里面还有 sql2csv 等工具。


## 2. 查看数据

在 Python 里面，拿到一个数据，肯定先看看整体情况，info 无疑会在导入数据后第一时间使用，其实使用 Pandashells 也能如此

```
$ < tips.csv | p.df 'df.info()'
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 244 entries, 0 to 243
Data columns (total 7 columns):
bill      244 non-null float64
tip       244 non-null float64
sex       244 non-null object
smoker    244 non-null object
day       244 non-null object
time      244 non-null object
size      244 non-null int64
dtypes: float64(2), int64(1), object(4)
memory usage: 13.4+ KB

$ head tips.csv | csvlook
|--------+------+--------+--------+-----+--------+-------|
|  bill  | tip  | sex    | smoker | day | time   | size  |
|--------+------+--------+--------+-----+--------+-------|
|  16.99 | 1.01 | Female | No     | Sun | Dinner | 2     |
|  10.34 | 1.66 | Male   | No     | Sun | Dinner | 3     |
|  21.01 | 3.5  | Male   | No     | Sun | Dinner | 3     |
|  23.68 | 3.31 | Male   | No     | Sun | Dinner | 2     |
|  24.59 | 3.61 | Female | No     | Sun | Dinner | 4     |
|  25.29 | 4.71 | Male   | No     | Sun | Dinner | 4     |
|  8.77  | 2.0  | Male   | No     | Sun | Dinner | 2     |
|  26.88 | 3.12 | Male   | No     | Sun | Dinner | 4     |
|  15.04 | 1.96 | Male   | No     | Sun | Dinner | 2     |
|--------+------+--------+--------+-----+--------+-------|

```

借助 `head` `tail` `less` `sed` 等内置命令，能对数据整体有个基本了解了。如果需要单独针对一些列，方便接下来的数据清洗，可用 `csvcut` 的 `-n` 参数，打印出来的列号、列名都是极有用的。

```
$ < tips.csv | csvcut -n
  1: bill
  2: tip
  3: sex
  4: smoker
  5: day
  6: time
  7: size
```

## 3. 数据整理与清洗

#### 行过滤

简单的过滤，如：位置过滤、简单模式过滤，[命令行中的数据科学 (豆瓣)](https://book.douban.com/subject/26387975/) 这本书讲的很清楚，用上篇文章提到的 head、tail、grep、sed、awk 等内置命令便可。

略复杂点的模式过滤，可用 csvkit 工具包里面的 csvgrep，它有两个重要参数 `-c`、`-r`，后面分别跟列名/号和正则匹配，如提取在晚上用餐的男性所给小费数据，

```
$ < tips.csv | csvgrep -c 3 -r Male | csvgrep -c time -r Dinner | head | csvlook
|--------+------+------+--------+-----+--------+-------|
|  bill  | tip  | sex  | smoker | day | time   | size  |
|--------+------+------+--------+-----+--------+-------|
|  10.34 | 1.66 | Male | No     | Sun | Dinner | 3     |
|  21.01 | 3.5  | Male | No     | Sun | Dinner | 3     |
|  23.68 | 3.31 | Male | No     | Sun | Dinner | 2     |
|  25.29 | 4.71 | Male | No     | Sun | Dinner | 4     |
|  8.77  | 2.0  | Male | No     | Sun | Dinner | 2     |
|  26.88 | 3.12 | Male | No     | Sun | Dinner | 4     |
|  15.04 | 1.96 | Male | No     | Sun | Dinner | 2     |
|  14.78 | 3.23 | Male | No     | Sun | Dinner | 2     |
|  10.27 | 1.71 | Male | No     | Sun | Dinner | 2     |
|--------+------+------+--------+-----+--------+-------|
```

**随机过滤**

随机过滤经常用于数据验证，对于 csv 数据，我们即可结合 body 只处理主体，也可直接用 pandashells，如提取 1% 的随机数据

```
$ < tips.csv | body sample -r 1%  | csvlook
|--------+------+--------+--------+------+--------+-------|
|  bill  | tip  | sex    | smoker | day  | time   | size  |
|--------+------+--------+--------+------+--------+-------|
|  24.06 | 3.6  | Male   | No     | Sat  | Dinner | 3     |
|  3.07  | 1.0  | Female | Yes    | Sat  | Dinner | 1     |
|  17.29 | 2.71 | Male   | No     | Thur | Lunch  | 2     |
|--------+------+--------+--------+------+--------+-------|
```

pandas 的做法

```
$ < tips.csv | p.df 'df.sample(frac=0.01)' | csvlook
|--------+------+--------+--------+-----+--------+-------|
|  bill  | tip  | sex    | smoker | day | time   | size  |
|--------+------+--------+--------+-----+--------+-------|
|  22.42 | 3.48 | Female | Yes    | Sat | Dinner | 2     |
|  10.59 | 1.61 | Female | Yes    | Sat | Dinner | 2     |
|--------+------+--------+--------+-----+--------+-------|
```

**更复杂的过滤**

这时用 pandashells 无疑最快，比如我要提取 coca 两万词频中单词含有 ain 且发音为 ɛn 的单词

```
<  coca20k_WB_speech.csv | p.df 'df[df.phonetic.notnull() & df.words.notnull()] ' | p.df 'df[df.words.str.contains("ain")]' | p.df 'df[df.phonetic.str.contains("ɛn")]' | head -n 3 | csvlook
|-------+---------+-----------+-------------+------------|
|  rank | words   | phonetic  | coca_speech | WB_speech  |
|-------+---------+-----------+-------------+------------|
|  180  | against | /əˈgɛnst/ | i           |  prep      |
|  184  | again   | /əˈgɛn/   | r           |  adv       |
|-------+---------+-----------+-------------+------------|
```

当然也可以借助 Rio 使用 R 的 sqldf 包或 dplyr 包。当然，写起来略复杂了，一般这种情况我都用 pandashells，毕竟 Python 是我的主力语言。

```
< iris.csv | Rio -se 'sqldf("select * from df where df.sepal_width > 3")' | head | csvlook 
|---------------+-------------+--------------+-------------+--------------|
|  sepal_length | sepal_width | petal_length | petal_width | species      |
|---------------+-------------+--------------+-------------+--------------|
|  5.1          | 3.5         | 1.4          | 0.2         | Iris-setosa  |
|  4.7          | 3.2         | 1.3          | 0.2         | Iris-setosa  |
|  4.6          | 3.1         | 1.5          | 0.2         | Iris-setosa  |
|  5            | 3.6         | 1.4          | 0.2         | Iris-setosa  |
|  5.4          | 3.9         | 1.7          | 0.4         | Iris-setosa  |
|  4.6          | 3.4         | 1.4          | 0.3         | Iris-setosa  |
|  5            | 3.4         | 1.5          | 0.2         | Iris-setosa  |
|  4.9          | 3.1         | 1.5          | 0.1         | Iris-setosa  |
|  5.4          | 3.7         | 1.5          | 0.2         | Iris-setosa  |
|---------------+-------------+--------------+-------------+--------------|
```

#### 列提取

同理于行过滤，列提取方面，简单的可用 csvcut，先用 `-n` 参数看列名，`-c` 做提取

```
$ < tips.csv | csvcut -n                    
  1: bill
  2: tip
  3: sex
  4: smoker
  5: day
  6: time
  7: size
$ < tips.csv | csvcut -c 3: | head | csvlook
|---------+--------+-----+--------+-------|
|  sex    | smoker | day | time   | size  |
|---------+--------+-----+--------+-------|
|  Female | No     | Sun | Dinner | 2     |
|  Male   | No     | Sun | Dinner | 3     |
|  Male   | No     | Sun | Dinner | 3     |
|  Male   | No     | Sun | Dinner | 2     |
|  Female | No     | Sun | Dinner | 4     |
|  Male   | No     | Sun | Dinner | 4     |
|  Male   | No     | Sun | Dinner | 2     |
|  Male   | No     | Sun | Dinner | 4     |
|  Male   | No     | Sun | Dinner | 2     |
|---------+--------+-----+--------+-------|
```

复杂点的用 pandashells，跟写 pandas 一样了

```
$ < tips.csv | p.df 'df.iloc[1:3, 3:5]'| csvlook
|---------+------|
|  smoker | day  |
|---------+------|
|  No     | Sun  |
|  No     | Sun  |
|---------+------|
```


#### 列合并

列合并，内置命令有 `join`, csvkit 工具包有 `csvjoin`，但这些，除非是不依靠任何共同列的合并用 `paste`，其他合并一律不如 pandas 的 merge 好用，幸运的是，pandashells 里有 `p.merge` 这个 merge 命令

```
p.merge <(p.example_data -d election) <(p.example_data -d electoral_college) --how left --on state | p.df -o table | head 
days state  obama  mccain                           poll            name  electors  population
-252    AK     43      48                      SurveyUSA          Alaska         3      710000
-213    AK     43      48                      Rasmussen          Alaska         3      710000
-176    AK     41      50                      Rasmussen          Alaska         3      710000
-143    AK     41      45                      Rasmussen          Alaska         3      710000
-112    AK     40      45                      Rasmussen          Alaska         3      710000
 -99    AK     39      44                      Rasmussen          Alaska         3      710000
 -65    AK     35      54            Ivan Moore Research          Alaska         3      710000
 -58    AK     33      64                      Rasmussen          Alaska         3      710000
 -56    AK     39      55                            ARG          Alaska         3      710000
```

比如上方这个官方例子，依靠的是 `state` 这个共同列，且 `--how left` 以 `<(p.example_data -d election)` 这个数据为主.


## 4. 数据探索与可视化

#### 检查数据

开篇的 [查看数据](#查看数据) 已经提到了一些内置命令检查数据的办法，而如果想查看更加详尽的信息，

可用 pandashells 调用 pandas 的 `describe`，注意，`p.df` 处理过的数据默认是没有 index 的，若要显示，需额外添加 `index` 参数。

```
$ < tips.csv | p.df 'df.describe()' -o table index
             bill         tip        size
count  244.000000  244.000000  244.000000
mean    19.785943    2.998279    2.569672
std      8.902412    1.383638    0.951100
min      3.070000    1.000000    1.000000
25%     13.347500    2.000000    2.000000
50%     17.795000    2.900000    2.000000
75%     24.127500    3.562500    3.000000
max     50.810000   10.000000    6.000000
```

当然，还可做个 groupby

```
$ < tips.csv | p.df 'df.groupby(by=["sex","day"]).tip.mean()' -o table index 
                  tip
sex    day           
Female Fri   2.781111
       Sat   2.801786
       Sun   3.367222
       Thur  2.575625
Male   Fri   2.693000
       Sat   3.083898
       Sun   3.220345
       Thur  2.980333
```

也可以用 `csvstat` 查看更加详细的信息，直接使用 csvstat 操控数据，打印结果是冗长的，而用好一些参数会很方便


```
--max                 Only output max.
--min                 Only output min.
--sum                 Only output sum.
--mean                Only output mean.
--median              Only output median.
--stdev               Only output standard deviation.
--nulls               Only output whether column contains nulls.
--unique              Only output unique values.
--freq                Only output frequent values.
--len                 Only output max value length.
--count               Only output row count
```

比如检查下 null 值，或行数


```
$ csvstat tips.csv --null
  1. bill: False
  2. tip: False
  3. sex: False
  4. smoker: False
  5. day: False
  6. time: False
  7. size: False
$ csvstat tips.csv --count
Row count: 244
```

#### 数据可视化

静态可视化，ggplot 无疑是最擅长的，值得庆幸的是，`Rio` 有 ggplot 的相关参数，比如画个直方图，看分布情况，用管道连接 display 可在终端直接把图形打印出来

```
$ < tips.csv Rio -ge 'g+geom_histogram(aes(total_bill))' | display
```

![](https://scottming-1253938325.cos.ap-chengdu.myqcloud.com/blog/170111-tips.png?imageView2/2/w/600)


用重定向也可保存图形

```
$ < iris.csv Rio -ge 'g+geom_point(aes(x=SepalLength,y=SepalWidth,colour=Name))' > iris.png
```

pandashells 也有图形选项，个人比较喜欢 ggplot，用好命令行，完全可以把 Python 和 R各自的优势结合起来使用。

## Refrences

* R
    + [eddelbuettel/littler: A scripting and command-line front-end for GNU R](https://github.com/eddelbuettel/littler)
    + [littler examples: how to use a scripting front-end for GNU R](http://dirk.eddelbuettel.com/code/littler.examples.html)
    + [Diving into Genetics and Genomics: bring the power of R to command line](http://crazyhottommy.blogspot.jp/2016/08/bring-power-of-r-to-command-line.html)
    + [Diving into Genetics and Genomics: csvkit to manipulate csv at command line, Rio to interact with R at command line](http://crazyhottommy.blogspot.jp/2014/11/csvkit-to-manipulate-csv-at-command.html)
* [jeroenjanssens/data-science-at-the-command-line: Data Science at the Command Line](https://github.com/jeroenjanssens/data-science-at-the-command-line)
* [robdmc/pandashells: Bringing the python data stack to the shell prompt](https://github.com/robdmc/pandashells)