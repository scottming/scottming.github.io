---
layout: post
title: 数据分析中的命令行工具（1）
subtitle:   Linux Shell 内置命令
date:       2017-01-06
author:     "Scott"
header-img: "img/160403-debian.png"
catalog:    true
tags:
    - Linux
---

自 16 年 9 月开始转岗「数据分析师」，已近 4 月，数据分析的前提是数据清洗和整理，在诸多重复性工作中，命令行无疑帮了大忙，多数任务用 Python 等编程语言得写小会代码，而命令行通常一两行就能解决，这篇文章是我最常用的系统内置命令介绍。

## head & tail 查看数据

```
head -n Number filename
tail -n Number filename
```

上面 head 和 tail 的基本用法相信很多人知道，但如果你想去除某些行，打印其他呢？

```
$ seq 20 | head -n -5
```

这条命令将去除最后5行，打印 1-15 行，同理：

```
$ seq 20 | tail -n +6
``` 
将打印后15行，去除了前 5 行，记得这里数字 +1 了。

跟 body 配合一起用非常方便，如看看 coca 1000-2000 的词频

```
$ coca20k head -n 2000 coca20k_source.csv | body tail -n +1001 | head -n 5
rank,words,speech,frequency,dispersion
1001,method,n,42667,0.88 A
1002,sign,v,39418,0.95
1003,somebody,p,41639,o.90 S
1004,magazine,n,39197,0.96
```

body 的用处在于保存头部处理数据。


## sort & uniq 排序和唯一

在我之前的文章有提过，`sort` 排序有这些参数

- `-n` 按数字进行排序
- `-d` 按字典序进行排序
- `-r` 逆序排序
- `-k` N 指定按第 N 列排
- `-t` 指定分割符号，跟 `body` 配合使用时会出错，不建议用
    - 纯粹的 `'\t'` 或 `' '` 分割，可以直接用sort，无须指定
    - 而两者混合的情况，则需 `-t$'\t'` 做 tab 分割 排序，空格则无须用 `$` 特别强调。

为了看清一个数据的近似分布，可能会想画个直方图，若嫌开启 Python、R 太麻烦，其实命令行也可以搞定基本的。

```
$ cat sample.txt
January
January
February
October
January
March
September
September
February
```

```
$ cat sample.txt | sort | uniq -c | sort -rk1
      3 January
      2 September
      2 February
      1 October
      1 March
```

uniq 一定得和 sort 搭配用，如果没有第一个 sort，频数统计便会出错，第二个 sort 的 `-r` 表示反向，`-k` 表示列，`-1`表示频数排序。


## tr 转换

```
$ tr -s [set1]  # 压缩
$ tr -d [set1]  # 删除特定字符
$ tr [set1] [set2]  # 替换
```


## grep 文本内搜索

查看不包含

```
$ grep -v scott /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
......
```

查看不包含的行数

```
$ grep -cv scott /etc/passwd/
43
```

查找时忽略大小写

```
$ grep -i scott /etc/passwd
```

在一个路径下查找匹配模式下的文件, 这个很常用，比如忘记自己的代码在哪个文件了。

```
grep -rnw path -e 'patterns'  # 模式
grep -rnw /home/scott/AnalyzedNotes -e 'FontProperties'  # 实例
```

## find 查找

`find` 无疑是 Unix/Linux 命令行中最棒的工具之一，但大多数人却无法发挥它的最大功效。

find 基本的参数有：

1. 基于名称或正则
    - `-name`
    - `-iname` 名称忽略大小写
    - `-name -o -name ` 同时匹配两者，如 `find . \( -name "*.txt" -o -name "*.pdf" \) -print`
    - `-path` 通配符匹配路径，如 `find /home/users -path "*/slynux/*" -print
    - `-iregex` 忽略大小写的正则，如 `find . -regex ".*\(\.py\|\.sh\)$" `
2. 否定参数
    - `!` 
3. 基于目录深度
    - `-maxdepth` 最大深度
    - `-mindepth` 最小深度
4. 基于文件类型
    - `-type f` 文件
    - `-type d` 目录
    - `-type l` 符号链接
5. 基于文件时间的搜索
    - `atime` 用户最近一次访问文件的时间
    - `mtime` 文件内容最后一次修改的时间
    - `ctime` 文件元数据（如权限或所有权）最后一次改变的时间
    - 上述三者接数字，数字有 `-`、`+` 的区分，单位为天，`time` 换成 `min` 即分钟
        - `-7` 表 7 之内，如 7 天之内
        - `7` 表恰好 7
        - `+7` 表访问时间超过 7 
6. 基于文件大小的搜索
    - `size +2k` 大于 2k
    - `size  2k` 等于 2k
    - `size -2k` 小于 2k
    - 其他 size 类型
        - `M`
        - `G`
7. 删除匹配文件
    - `-delete` 可删除文件，目录需用其他方法。
8. 基于所有权或权限
    - `-perm`  如 `-perm 644`

上面 8 点 是 `find` 的常用且常见的参数，要使 `find` 更加强大，还需跟其他命令结合使用，如 `xargs`，而一些常用的命令执行也能用 `-exec` 参数搞定，如

比如把目录下所有 Python 文件 移动到 PythonScrips 这个目录

```
find . -name '*py` -exec cp {} PythonScrips \;
```

把 `cp` 换成其他命令，自然就能对单文件执行其他操作了。

## 玩转 xargs

`xargs` 主要用途在于读取 stdin，然后格式化参数传递给命令，`xargs` 和 `find` 是一对好基友，两者结合可以让任务变得更轻松，但千万别用错了，特别是 `rm -rf` 等命令的结合使用。

常用参数：

- `-n` 打印
- `-I` 替换字符串

寻找当前目录下所有 txt 结尾的文件并删除

```
find . -type f -name '*.txt' -print0 | xargs -0 rm -f 
```
寻找当前目录下所有 txt 结尾的文件并打包成 tst.tar.gz 文件

```
find . -type f -name '*.txt' -print0 | xargs -0 tar -czf tst.tar.gz
```

## paste 按列拼接

按列拼接文件是极为常用数据处理任务，而 paste 可以轻松搞定。

如下两个文件

```
$ seq 5 > file1.txt
$ cat file2.txt 
slynux
gnu
bash
back
```

拼接后

```
$ paste file1.txt file2.txt 
1   slynux
2   gnu
3   bash
4   back
5   
```

也可以用 `-d` 指定定界符，例如

```
$ paste file1.txt file2.txt -d, 
1,slynux
2,gnu
3,bash
4,back
5,
```


## 用 sed 做文本替换

sed、Awk、Perl 、Python 分别什么时候用，争论很多，我觉得这个 [stackoverflow 的回答](http://stackoverflow.com/questions/366980/what-are-the-differences-between-perl-python-awk-and-sed) 很棒。


> - sed - when you need to do simple text transforms on files.
> - awk - when you only need simple formatting and summarization or transformation of data.
> - perl - for almost any task, but especially when the task needs complex regular expressions.
> - python - for the same tasks that you could use Perl for.

sed 的基本语法是：

`sed [options] {sed-commands} {input-file}`

sed 每次从 input-file 中读取一行记录，并在该记录上执行 sed-commands
sed 首先从 input-file 中读取第一行，然后执行所有的 sed-commands；再读取第二行，执行
所有 sed-commands，重复这个过程，直到 input-file 结束，而 option 则是一些可选项，如常用的打印 `-n`，替换原文件 `-i`(谨慎使用，建议 `ibak`)

**而他最常用的功能是**

- 筛选打印
- 替换
- 删除、追加等

首先创建两个文件，待会会用到：

```
$ cat sample.txt
January January
January
February
October
January
March
September
September
February

$ cat employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
```

### 1. 打印

打印含有两者的行，p 一定要和 n 一起出现，不然会打印两次

```
$ sed -n '{
/^root/ p
/^nobody/ p
}' /etc/passwd
```

打印含有两者的行

```
$ cat employee.txt 
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
```

打印指定行 `,`

```
$ sed -n '1,3 p' employee.txt 
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
```

打印指定行后面的行数 `+`

```
$ sed -n '1,+3 p' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
```

跳过的行数 `~`

```
$ sed -n '1~2 p' employee.txt
101,John Doe,CEO
103,Raj Reddy,Sysadmin
105,Jane Miller,Sales Manager
```

打印第一次匹配 Jason 的行至第 4 行的内容:

```
$ sed -n '/Jason/,4 p' employee.txt
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
```

### 2. 删除行

删除用法其实就是把 `-n` 去掉，`p` 换成 `d` 即可

### 3. 替换

基本语法

`sed '[address-range|pattern-range] s/original-string/replacement-string/[substitute-flags]'
input file`

上面提到的语法为：

- address-range 或 pattern-range(即地址范围和模式范围)是可选的。 如果没有指定，那么 sed 将在所有行上进行替换。
- s 即执行替换命令 substitute
- original-string 是被 sed 搜索然后被替换的字符串，它可以是一个正则表达式
- replacement-string 替换后的字符串
- substitute-flags 是可选的，下面会具体解释

对比全局替换和非全局替换

```
$ sed '/January/s/January/1/g' sample.txt
1 1
1
February
October
1
March
September
September
February

$ sed '/January/s/January/1/' sample.txt        
1 January
1
February
October
1
March
September
September
February
```

**数字标志**，第二次出现的 January 替换为1

```
$ sed '/January/s/January/1/2' sample.txt
January 1
January
February
October
January
March
September
September
February
```

**打印标志**

打印替换的行

```
$ sed -n '/January/s/January/1/2p' sample.txt
January 1
```

**忽略大小写**

```
$sed -n '/January/s/january/1/2ip' sample.txt
January 1
```

**& 作用**

```
$ sed 's/^[0-9][0-9][0-9]/[&]/g' employee.txt
[101],John Doe,CEO
[102],Jason Smith,IT Manager
[103],Raj Reddy,Sysadmin
[104],Anand Ram,Developer
[105],Jane Miller,Sales Manager
```

**替换备份**

`-i` option 可以修改原文件
`-ibak` 则是修改之前备份源文件 


### 4. 正则表达式

`sed` 里使用正则跟其他地方使用正则类似，关键是得明白哪些需要转义，哪些不需要


**需要转义**

- `{}` -> `\{`
- `|` -> `\|`

**不需要转义**

- `[]`

### 5. 附加命令

**插入与追加命令**

语法：

`$ sed '[address] i the-line-to-insert' input-file`

试比较下面两者的区别

```
$ sed '1 a Month' sample.txt
January January
Month
January
February
October
January
March
September
September
February
$ ^a^i
$ sed '1 i Month' sample.txt
Month
January January
January
February
October
January
March
September
September
February
```


**修改命令**

命令 a、 i 和 c 可以组合使用。下面的例子将完成三个操作：

- a 在”Jason”后面追加”Jack Johnson”
- i 在”Jason”前面插入”Mark Smith”
- c 用”Joe Mason”替代”Jason”

```
$ sed '/Jason/ {
> a\
> 204,Jack Johnson,Engineer
> i\
> 202,Mark Smith,Sales Engineer
> c\
> 203,Joe Mason,Sysadmin> }' employee.txt
101,Johnny Doe,CEO
202,Mark Smith,Sales Engineer
203,Joe Mason,Sysadmin
204,Jack Johnson,Engineer
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manag
```

**打印不可见的字符**

```
$ sed -n 'l' tablfile.txt 
fname\tFirst Name$
lname\tLast Name$
mname\tMiddle Name$
```

## 用 Awk 做高级文本处理


> 计算机用户经常把大量的时间花费在简单, 机械化的数据处理工作中 — 改变数据格式, 验证数据的 有效性, 搜索特定的数据项, 求和, 打印报表等. 这些工作完全可以自动化地完成, 但是如果每碰到一个这 样的任务, 就用一门标准的编程语言 (比如 C 或 Pascal) 写一个专用的程序来解决它, 未免也太麻烦了.
> 
> Awk 是一门特殊的编程语言, 它可以非常方便处理上面提到的任务, 经常只需要一两行便可搞定. 一个 awk 程序由一系列的模式和动作组成, 这些模式与动作说明了在输入中搜索哪些数据, 以及当符合 条件的数据被找到时, 应该执行什么操作. Awk 在输入文件集合中搜索与模式相匹配的输入行, 当找到 一个匹配行时, 便会执行对应的动作. 通过字符串, 数值, 字段, 变量, 和数组元素的比较操作, 再加上正 则表达式, 利用这些组合, 一个模式可以用来选择输入行, 而动作可以对选中的行作任意的处理. 描述动 作的语言看起来和 C 非常像, 但是它不需要声明, 并且字符串和数值都是内建的数据类型.
>
> ——Awk 程序设计

### 1. 基础

首先创建一个文件,叫作 emp.data, 这个文件包含有名 字, 每小时工资 (以美元为单位), 工作时长, 每一行代表一个雇员的记录, 就像这样

```
Beth   4.00    0
Dan    3.75    0
Kathy  4.00    10
Mark   5.00    20
Mary   5.50    22
Susie  4.25    18
```

假设要打印每位雇员的名字以及他们的报酬 (每小时工资乘以工作时长)，而雇员的工作时长必须大于零，可以这样做

```
$ awk '$3 > 0 {print $1,$2*$3}' emp.data
Kathy 40
Mark 100
Mary 121
Susie 76.5
```

pattern + action, 上面的代码中，`$3 > 0` 即 模式，`{print $1,$2*$3}` 即动作，两者都是可选的，但不能同时省略。


也可以配合 `printf` 打印的更加的漂亮

```
$ awk '$3 > 0 { printf("total pay for %s is $%.2f\n", $1, $2*$3) }' emp.data
total pay for Kathy is $40.00
total pay for Mark is $100.00
total pay for Mary is $121.00
total pay for Susie is $76.50
```

### 2. 选择

模式可以使用括号和逻辑运算符进行组合, 逻辑运算符包括 &&, ||, 和 !, 分别表示 AND, OR, 和 NOT. 程序

如选择大于0小于20小时的人和时间

```
$ awk ' $3 < 20 && $3 > 0 {print $1,$3}' emp.data
Kathy 10
Susie 18
```

打印的行不满足这样的条件: $2 小于 4, 并且 $3 也小于 20

```
$ awk '!($2 < 4 && $3 < 20)' emp.data
Beth   4.00    0
Kathy  4.00    10
Mark   5.00    20
Mary   5.50    22
Susie  4.25    18
```

还可以用正则符号做筛选

- `~` 模糊匹配
- `!~` 不匹配

```
$ awk -F, '$2~"Tennis"' items.txt
104,Tennis Racket,Sports,190,20
```

### 3. 计算

```
$ awk '{pay = pay + $2 * $3}
    END {print NR, "employees"
    print "total pay is", pay
    print "average pay is", pay/NR}' emp.data 
6 employees
total pay is 337.5
average pay is 56.25
```

### 4. 内置变量和内建函数


`OFS` 输出字段分隔符

```
$ awk -F, 'BEGIN{OFS=":"}{print $2,$3}' employee.txt 
John Doe:CEO
Jason Smith:IT Manager
Raj Reddy:Sysadmin
Anand Ram:Developer
Jane Miller:Sales Manager
```

`NR` 打印行号

```
$ awk -F, '{print "Emp id of record number",NR,$1}' employee.txt
Emp id of record number 1 101
Emp id of record number 2 102
Emp id of record number 3 103
Emp id of record number 4 104
Emp id of record number 5 105
```

length 判断长度

```
$ awk 'length($1) <= 4 {print $1}' emp.data
Beth
Dan
Mark
Mary
```

### 5. 查重和去重

去重，默认保留 first

```
awk '!seen[$0]++' sample.txt
```

以第一列为准去重，同理，去除 `!` 即可查重（非first）


```
awk '!seen[$1]++' sample.txt
```

去重，不去空行

```
awk '!NF || !seen[$0]++' sample.txt
```

### Awk 其他实用一行命令

1. 输入行的总行数

    `END { print NR }`

2. 打印第 10 行

    `NR == 10`

3. 打印每一个输入行的最后一个字段

    `{ print $NF }`

4. 打印最后一行的最后一个字段

    `{ field = $NF } END { print field }`

5. 打印字段数多于 4 个的输入行

    `NF > 4`

6. 打印最后一个字段值大于 4 的输入行

    `$NF > 4`

7. 打印所有输入行的字段数的总和

    `{ nf = nf + NF } END { print nf }`

8. 打印包含 Beth 的行的数量

    `/Beth/ { nlines = nlines + 1 } END { print nlines }`

9. 打印具有最大值的第一个字段, 以及包含它的行 (假设 $1 总是 正的)

    `$1 > max { max = $1; maxline = $0 } END { print max, maxline }`

10. 打印至少包含一个字段的行

    `NF > 0`

11. 打印长度超过 80 个字符的行

    `length($0) > 80`

12. 在每一行的前面加上它的字段数

    `{ print NF, $0 }`

13. 打印每一行的第 1 与第 2 个字段, 但顺序相反

    `{ print $2, $1 }`

14. 交换每一行的第 1 与第 2 个字段, 并打印该行

    `{ temp = $1; $1 = $2; $2 = temp; print }`

15. 将每一行的第一个字段用行号代替

    `{ $1 = NR; print }`

16. 打印删除了第 2 个字段后的行

    `{ $2 = ""; print }`

17. 将每一行的字段按逆序打印

    `{ for (i = NF; i > 0; i = i - 1) printf("%s ", $i) printf("\n") }`

18. 打印每一行的所有字段值之和

    `{ sum = 0 for (i = 1; i <= NF; i = i + 1) sum = sum + $i print sum }`

19. 将所有行的所有字段值累加起来

    `{ for (i = 1; i <= NF; i = i + 1) sum = sum + $i } END { print sum }`

20. 将每一行的每一个字段用它的绝对值替换

    `{ for (i = 1; i <= NF; i = i + 1) if ($i < 0) $i = -$i print }`


## Refrences 

* [Linux Shell 脚本攻略 (豆瓣)](https://book.douban.com/subject/6889456/) 印度人写的一本 Shell 好书，适合 Shell 进阶读，本文 find 部分的整理来源；
* [The AWK Programming Language (豆瓣)](https://book.douban.com/subject/1876898/) AWK 作者写的 AWK 介绍书，本文 AWK 部分参考此书第一章；
* [Learn Sed and Awk to Enhance Your UNIX / Linux Life](http://www.thegeekstuff.com/sed-awk-101-hacks-ebook/) Sed 和 Awk 的妙招