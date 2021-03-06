---
layout: post
title: Git 常用命令
subtitle:
date:       2016-08-06
author:     "Scott"
header-img: ""
catalog:    false
tags:
    - Git
---

**目录：**

[TOC]

## 配置

```
git config --global color.ui true
git config --global alias.co checkout
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.br branch
git config --global core.editor "subl -w"    # 设置 Editor 使用 sublime
git config -l  # 列举所有配置
```

## 时光机

### 对比

工作区、缓存区、最新 commit 之间的对比？

```
git diff # 工作区与缓存区的差异
git diff -–cached # 缓存区与最新 commit 之间的差异
git diff HEAD # 工作区与最新 commit 之间的差异
git diff @ # 同上
```

如何对比当前版本和上一个版本？

```
# 以下三者皆可
git diff @~..@
git diff HEAD^ HEAD
git show @
```

如何对比当前版本和任意版本？

```
git diff commit_id HEAD
git diff commit_id @
```

其他的一些对比，参考 TLCL 的文本操作

```
diff -c file1 file2 # 比较两个文件，上下文模式
diff -u file1 file2 # 比较两个文件，统一模式
```

### 撤销与回退

如何撤销工作区编辑？

```
git config --global alias.co checkout # git checkout 比较常用，设个别名
git co .
git co -- filename
```

如何撤销缓存区？

```
git reset HEAD filename # add 之后，恢复后回到编辑状态。
git reset @ filename # 同上
```

提交到版本库之后如何回退？

```
# 会把当前历史删除的做法
git log --pretty=oneline # 查看最近的日志
git reset --hard @~ # 直接回退到上一个版本
git reset --hard HEAD^ # 同上 

# 比较安全的做法
git revert <$id>    # 恢复某次提交的状态，恢复动作本身也创建了一次提交对象，需重写 commit
git revert HEAD     # 恢复最后一次提交的状态

# 感觉自己这次写错了
get reset @~ # 回到工作区的编辑状态，跟上方的 hard 还是有区别的。
```

改变 git log 至标准输出

```
git config pager.log false
```

## 查看命令历史

```
git log -1 @ # 简单查看最后的一次提交历史
git config --global alias.last 'log -1 HEAD' # 设置别名
git last # 同 1
```

```
git log --pretty=oneline # 一行显示
git log --pretty=format:"%h - %an, %ar : %s" -5 # 定制式最近的 5 条记录
```

```
git log -p -2 # 查看最近两次提交的差异
git log --stat -2 # 查看最近两次提交的统计信息
```

## 分支管理

搭建 git 服务器

```
git clone --bare my_project my_project.git # 服务器新建空仓库
sudo chown git:git -R # 改变仓库所有者
git clone git@server:/home/scott/.gitrepo/sample.git # 克隆服务器仓库
```

Fetch 分支、对比

```
git fetch origin master 
git diff ..FETCH_HEAD # 对比当前版本库和 Fetch 的内容
git diff @..FETCH_HEAD # 同上
```

Clone 所有分支

```
git branch -a
git co -b newbranch origin/newbranch
```

单独 Checkoout 某分支的某个文件

```
git co branch file
git commit -m "say something"
```


推送两个 remote

```
git remote set-url --add --push origin git://original/repo.git
git remote set-url --add --push origin git://another/repo.git
```

其他待补......

## References

- [常用命令参考](http://robbinfan.com/blog/34/git-common-command)
- [廖雪峰的 Git 教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013743862006503a1c5bf5a783434581661a3cc2084efa000)
- [Git Book](https://git-scm.com/book/zh/v2)
