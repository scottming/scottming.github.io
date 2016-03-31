---
layout: post
title: ReadtheDocs 折腾记
subtitle:   如何快速搭建一个写书平台
date:       2016-03-27
author:     "Scott"
header-img: "img/home-bg-o.jpg"
tags:
    - Python
    - 网站搭建
---

参加的数据科学班今天结束了，但还有很多疑难点没搞清楚，很多收获也没整理。课程结束后想补写下笔记，做下查遗补漏，心想，与其写笔记，不如写成一本书的形式，更美观，也更系统。

现有的写电子书的方式，有以下几个解决方案，优劣势也很明显。

- 自有博客，跟散文堆在一起，不便索引。
- GitHub Wiki，适合做知识整理，但排版一般，不方便本地查看。
- GitBook，丑，慢。

经过一些比较和调查，最终锁定 Sphinx + GitHub + ReadtheDocs 作为文档写作工具。用 Sphinx 生成文档，GitHub 托管文档，再导入到 ReadtheDocs。

Sphinx 是一个基于 Python 的文档生成工具，最早只是用来生成 Python 官方文档，随着工具的完善，越来越多的知名的项目也用他来生成文档，甚至完全可以用他来写书，我最初被他所震撼，也是因为看到了这本书 [Linux 工具教程](https://linuxtools-rst.readthedocs.org/zh_CN/latest/)。

引用几点 Sphinx 的优势：

- 丰富的输出格式: 支持 HTML (包括 Windows 帮助文档), LaTeX (可以打印PDF版本), manual pages（man 文档）, 纯文本
- 完备的交叉引用: 语义化的标签,并可以自动化链接函数,类,引文,术语及相似的片段信息
- 明晰的分层结构: 可以轻松的定义文档树,并自动化链接同级/父级/下级文章
- 美观的自动索引: 可自动生成美观的模块索引
- 精确的语法高亮: 基于 Pygments 自动生成语法高亮 

## 1. 安装 Sphinx

Mac 系统下安装极简，一行代码搞定

``` shell
brew cask install sphinx
```

或

``` shell
pip install sphinx
```

我因为之前安装过 Anaconda，自带了 sphinx，便省略这步了。更多安装方法，请看此处：[Install Sphinx](http://www.sphinx-doc.org/en/stable/install.html)。

## 2. 创建工程

创建工程也就是创建文档。这步很简单，到创建工程的目录，比如我的是 `/Users/Scott/Documents/2.创造乐趣`, 创建一个名为 DataBook 的文件夹，你可以用别的你想用的名字。

### 创建：

``` shell
➜  2.创造乐趣 pwd
/Users/Scott/Documents/2.创造乐趣
➜  2.创造乐趣 mkdir BookData
➜  2.创造乐趣 ls
1.keynote 2.技术    BookData  映射
```

BookData 创建好了，进入该目录：

``` shell
➜  2.创造乐趣 cd BookData
```

输入`sphinx-quickstart`，接下来程序会提示你输入一些选项，基本上按 Return 就好了，有些地方看提示注意下，不懂的话可以参考这里：[建立sphinx工程](http://jwch.sdut.edu.cn/book/tool/UseSphinx.html#id5)。


完成之后，

``` shell
➜  BookData git:(master) ✗ ls
Makefile build    make.bat source
```

可以看到有4个文件：

- build 目录 运行make命令后，生成的文件都在这个目录里面
- source 目录 放置文档的源文件
- make.bat 批处理命令
- makefile

基本完成，使用 `make html` 命令就可以生成html形式的文档了。

### 配置（conf.py）
如果没有什么特殊需要，我觉得 Sphinx 没啥好配的，改个主题就好了，个人极其喜欢ReadtheDoc的主题。很简单，把 conf.py 里面的这句：

``` python
html_theme = 'alabaster'
```
换成

``` python 
html_theme = 'sphinx_rtd_theme'
```

### 将 Sphnix 生成的文档和配置 push 到远程 github

``` shell
➜  BookData git init
➜  BookData git add .
➜  BookData git commit -m "sphinx start"
➜  BookData git remote add origin https://github.com/[yourusename]/[yourrepository].git
➜  BookData git push origin master
```

注意：[yourusename]/[yourrepository] 换成你的 github 名和仓库名。

## 3. 导入到 ReadtheDocs

- GitHub 里选择仓库，然后依次点击 Setting => Webhooks & Service => Add service => ReadTheDocs,激活这个选项。

- 到 ReadtheDocs import 这个仓库，导入成功后，点击阅读文档，便可看到 Web 效果了。

## 4. 配置目录结构

到了这一步，基本上已经搭建了，但这个时候直接写文档是还不够的。目录结构需要配置下。

假如我要添加两个文档 example.rst 和 rest_eazy.rst 到索引 index.rst 里：

```
.. toctree::
   :maxdepth: 2

   example
   rest_eazy
```   

`make html`后，效果如下：

![](http://7xjuve.com1.z0.glb.clouddn.com/160327_rtd01.png?imageView2/2/w/720)

会发现明明是2个文档，左边却 有 3个标题，因为主索引是按一级标题的数量来索引的，所以这种方式不可取，标题一多会很乱，好的办法是创建二级索引：

因为我要建的板块是3个，分别为数据科学「入门篇」、「基础篇」、「工具篇」，所以先把主索引 index.rst 改为：

``` python
目录:
^^^^^

.. toctree::
    :maxdepth: 2
    :glob:
    
    beginning/index
    base/index
    tool/index
```
然后在 source 目录下创建 3个目录 ，每个目录下创建一个 index.rst 文件，也就是二级索引文件。

``` shell
mkdir beginning base tool
touch beginning/index.rst base/index.rst tool/inde.rst 
```
做完这步后，把书的大纲理一下，写到 BookData 目录底下的 README.md 文件里：

``` shell
# Python 和数据科学

### 全书目录：

入门篇：

- Linux
- ipython
- 数值计算（Numpy）
- 数据绘图（Matplotlib）
- 数据绘图（Seabornd)

```
参照目录创建文件，如 入门篇，则在 beginning 目录下创建如下文件：

``` shell
touch 01_linux.rst 02_ipython.rst 03_numpy.rst 04_matplotlib.rst 05_seaborn.rst
```

每个文件里写上 一级标题，然后检查下：

``` shell
➜  BookData git:(master) ✗ ls source/beginning
01_linux.rst      03_numpy.rst      05_seaborn.rst
02_ipython.rst    04_matplotlib.rst index.rst
➜  BookData git:(master) ✗ cat source/beginning/*
Linux 基础
=========================

Jupyter 基础
=========================

数值计算（Numpy）
=========================

数据绘图（Matplotlib）
=========================

数据绘图（Seaborn)
=========================
```

然后把文件名添加到二级索引 beginning/index 里

```
入门篇
==========

这一部分主要介绍数据科学的入门内容;\
包含数据科学的基础工具，如：Jupyter、Linux，以及 Python 基本的数据科学包 Numpy，画图包 Matplotlib;


.. toctree::
    :maxdepth: 2
    :numbered: 2

    01_linux
    02_ipython
    03_numpy
    04_matplotlib
    05_seaborn
```

同理于 base 和 tool 目录，都完成之后会是下图的效果：
![](http://7xjuve.com1.z0.glb.clouddn.com/160327_rtd02.png?imageView2/2/w/720)

链接：[BookData](http://bookdata.readthedocs.org/en/latest/)， 有三个索引，下一个，上一个都非常顺畅。

## 其他

reStructureText 语法很简单，不建议刻意去学，如果习惯用 Markdown，建议用 [pandoc](http://pandoc.org/try/?text=%23+%E4%B8%80%E7%BA%A7%E6%A0%87%E9%A2%98%0A%23%23+%E4%BA%8C%E7%BA%A7%E6%A0%87%E9%A2%98%0A%23%23+%E4%B8%89%E7%BA%A7%E6%A0%87%E9%A2%98%0A%0A%60%60%60%0A+%E4%BB%A3%E7%A0%81%E5%9D%97%0A%60%60%60&from=markdown&to=rst) 一键转化即可.

![](http://7xjuve.com1.z0.glb.clouddn.com/160327-pandoc.png?imageView2/2/w/720)


