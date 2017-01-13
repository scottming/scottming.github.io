---
layout: post
title: 数据分析中的命令行工具（3）
subtitle:   成为创造者--快速创建命令行工具
date:       2017-01-13
author:     "Scott"
header-img: "img/160403-debian.png"
catalog:    false
tags:
    - Linux
    - Python
---

## 前言

无论是数据分析还是其他，总有一行命令解决不了的事，这时，便需要把工作流程写成脚本，Shell 脚本太复杂，人生苦短，掌握一些 Python 命令行脚本技巧，你会感叹世界如此美好。入职几个月，针对日常高频工作写了几十个 Python 脚本，其中有些脚本几乎没有参数，如执行一段流程，类似一键导出数据库等，有些带有两三个参数，还有些脚本拥有复杂嵌套参数。作为一个命令行钟爱者，这些脚本借助的命令行包自然是不一样的，我的命令行工具创建原则如下：

- 无参数: 用 Python 自带的 sys.argv[1] 表示 `argument` 即可
- 3 个参数以内：用 docopt，Python 里面最火的命令行包
- 3 个参数以上或有嵌套参数：用 click


```
# -*- coding: utf-8 -*-
import sys
from zkMySQL import zkquery


def cli():
    try:
        df = zkquery(sys.argv[1])
        return df.to_csv(sys.stdout, index=False)
    except (BrokenPipeError, IOError):
        pass
    sys.stderr.close()


if __name__ == '__main__':
    cli()
```

比如上方这个脚本，短短 10 行不到的代码，用起来却极方便，后面跟 SQL 语句即可

```
$ zkquery 'select id,price,bonus from goods_product limit 5' | csvlook      
|-------+-------+--------|
|  id   | price | bonus  |
|-------+-------+--------|
|  6834 | 12.8  | 1.2    |
|  6835 | 26.5  | 2.7    |
|  6836 | 38.7  | 3.0    |
|  6837 | 99.0  | 3.8    |
|  5491 | 55.0  | 2.8    |
|-------+-------+--------|
```

## Docopt 

下面这个脚本，我用了 docopt

```
# -*- coding: utf-8 -*-
from docopt import docopt
from pandas import read_csv
from zkMySQL import df2zkdb

def cli():
    cli_doc ="""df2zkdb
A script to push DataFrame to zokoodb.

Usage:
    df2zkdb <input> -t <table>

Options:
    -h --help   Show this screen.
    -t --table  to table.

"""
    arguments = docopt(cli_doc)
    df = read_csv(arguments['<input>'])
    df2zkdb(df, arguments['<table>'])


if __name__ == '__main__':
    cli()
```

这个脚本有两个参数，`<input>` 为 argument, `-t/--table` 为 option 参数。更多 docopt 的更多妙用建议看 kennethreitz 大神的一些小包，如 [kennethreitz/records: SQL for Humans™](https://github.com/kennethreitz/records)、[kennethreitz/em: the cli emoji keyboard™](https://github.com/kennethreitz/em) 等

## Click

接下来说说 click，为何要用 click 呢，官网对于这点讲的很透彻 [Why Click? — Click Documentation (5.0)](http://click.pocoo.org/5/why/)。比如 docopt 的组合性差这点，展示下我之前的烂代码：


```
$ ./advanced_freeq.py -h
advanced_freeq

Usage:
    ./advanced_freeq -i <bookname>  [-o <output>] [-c] [--mas=<mastered> --mas=<mastered>]

Examples:
    ./advanced_freeq -i txtname.txt -o bookfreeq.csv
    ./advanced_freeq -i txtname.pdf -o bookfreeq.csv --mas mastered.csv

Options:
    -h --help           Show this screen
    -v --version        Show version
    -i --input          Input file of bookname
    -o --output         Output frequency file
    -c --coca           CoCa Vocabulary
    --mas=<masterted>   Mastered vocabularies file
                        [default: /mastered.csv /COCA_top5000.csv]
```

这是一个统计英文词频的脚本，@王超兄写了词频统计的部分，在他的基础上，我增加了剔除已掌握词频、自动查字典、导出词云图等功能。而上面的脚本仅仅添加了一个功能，用 docopt 实现就如此复杂了。当时推荐给做英语教学的朋友试用，对方直接懵了。为增加功能，而不复杂，果断换成 click 实现，现在脚本运行起来是这样的，比上面功能多，却更简洁，


```
Usage: freeq [OPTIONS] COMMAND [ARGS]...

  freeq is a script to generate word frequency report of English
  text/pdf/epub...

  Examples:

      freeq -i tsttxt.pdf fordict
      freeq -i tstpdf.txt forread

      freeq -i tsttxt.txt fordict -o out.txt
      freeq -i tstpdf.pdf forread -o out.csv

      freeq -i tsttxt.txt fordict -n 2 -o out.txt
      freeq -i tstpdf.pdf forread -n 2 -o out.txt -p

Options:
  -i, --input TEXT  Set filename of the book.
  -h, --help        Show this message and exit.

Commands:
  fordict   Get frequent words for dictionary to import,...
  forread   Get a file of frequent words for reading.
  outimage  Get a wordcloud image.
```

例子循序渐进，Commands 分为 3 个，每个 Command 有各自的 Option，比如你只是想导个词频出来看看，可以这样查看帮助，

```
freeq -i test/tst_book.txt forread -h
Usage: freeq forread [OPTIONS]

  Get a file of frequent words for reading.

Options:
  -o, --output TEXT          Set output filename.
  -m, --mastered-words TEXT  Set filename of words list that you have
                             mastered.
  -n, --over-number INTEGER  Set over frequent number.
  -p, --phonetic
  -h, --help                 Show this message and exit.
```

Command 和 Option 的组合使得命令简洁而强大，而这些是如何实现的呢？

```
300 @click.group(context_settings=CONTEXT_SETTINGS)
301 @click.option('-i', '--input',
302     help='Set filename of the book.')
303 @click.pass_context
```

首先在 [Group](https://github.com/scottming/freeq/blob/master/freeq.py#L300) 的位置设定了 CONTEXT_SETTINGS，而这个设定是为了参数能传递到各个 Command，毕竟每个 Command 都要用到书籍文件。

```
324 @cli.command('forread')
325 @click.option('-o', '--output', help='Set output filename.')
326 @click.option('-m', '--mastered-words',
327     default=dirpath + '/mastered.csv',
328     help='Set filename of words list that you have mastered.')
329 @click.option('-n', '--over-number', default=3,
330     help='Set over frequent number.')
331 @click.option('-p', '--phonetic', is_flag=True)
332 @click.pass_obj
```

第 324 行是第一个 Command, 里面的 `forread` 则是 Command 名，仔细看几行代码，会发现 click 这种用装饰器的方式使得添加 Option 或 Argument 参数变得异常简单。如，第 331 行，我添加了一个可选项 `--phonetic`，方便查看音标和词频排序用， `is_flag` 实现了 bool 效果。输入命令时，有 `-p/--phonetic` 即为 `True`，打印 phonetic 和 rank，无则不打印此项。

```
$ freeq -i tst.txt forread -p | p.df -o table
rank    Word     phonetic coca_speech       WB_speech  Freq
1077  dinner      /ˈdınɚ/           n           noun    176
1237     sun       /ˈsʌn/           n   noun \n verb     76
1400  female  /ˈfiːˌmeıl/           j    adj \n noun     87
1535    male      /ˈmeıl/           j    adj \n noun    157
1591   lunch      /ˈlʌnʧ/           n   noun \n verb     68
```

总而言之，Click 是个结构优雅，上手简单，组合性极强的包，如果想用好他，建议仔细查看 [官方文档](http://click.pocoo.org/6/) 的 Options、Arguments 等节，以及 GitHub 的例子。


## Refrences

* [Comparing Python Command-Line Parsing Libraries - Argparse, Docopt, and Click - Real Python](https://realpython.com/blog/python/comparing-python-command-line-parsing-libraries-argparse-docopt-click/)
* [Why Click? — Click Documentation (5.0)](http://click.pocoo.org/5/why/)
* [docopt/docopt: Pythonic command line arguments parser, that will make you smile](https://github.com/docopt/docopt)