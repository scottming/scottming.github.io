---
layout: post
title: 我的 Mac 工具清单
subtitle:   让你更高效的一些工具
date:       2015-12-18
author:     "Scott"
header-img: "img/post-bg-2015.jpg"
tags:
    - 工具箱
    - OS
---
<!-- 160424 重新排版 -->

作为一个喜欢技术且不怕折腾的宅男，用 Mac 一年多，积累了不少好用的工具，他们让我的工作和生活变得快乐和高效，下面分享出来，希望也能对你有所帮助。

**筛选原则:**

- 简单易用，复杂的工具往往学习成本太高，用了反而耗费更多时间，得不偿失。
- 高效专注，一款好的工具往往是简洁明快，功能清晰。
- 美观，美，是很重要的。
- 不贵。

## 加速类

> 「懒惰、傲慢、缺乏耐性 」是程序员的三大美德。
>
> -- Larry Wall

### [Alfred](https://www.alfredapp.com/)

![](http://7xjuve.com1.z0.glb.clouddn.com/160424-Alfred.png?imageView2/2/w/720)

---

Alfred 是一款快速定位与文件查看工具。在《卓有成效的程序员》里面提到「键盘输入总比导航快」，人的工作记忆是很有限的，你比较舒服的记忆层级最多 5 个，所以子目录一旦大于 4 个，你就很难记住了。而 Alfred 的快速搜索却很方便，按下 Option + Space，输入程序首字母，按下 Return，正常打开一个程序不会超过 3 秒钟；若搜索文件则需在字幕前加`<空格>`

### [Karabiner](https://pqrs.org/osx/karabiner/seil.html.en)/[Seil](https://pqrs.org/osx/karabiner/seil.html.en)

![](http://7xjuve.com1.z0.glb.clouddn.com/160424-Karabiner01.png?imageView2/2/w/720)

---

「永远不要将你的双手从字符键上移开」是加速的重要原则，Vim 和 Emacs 的设计都符合这点，但日常办公，若用系统默认映射，想要做到这点是极难的。所幸我们有 Seil 和 Karabiner，这是一个很简单的修改键盘映射的小工具，具体操作方法可参考我这篇文章[让你的 Capslock 键更实用](http://scottming.com/2016/01/01/remap_keyboard/)，参照修改过后，你基本可以摆脱鼠标。

### [Keyboard Maestro](https://www.keyboardmaestro.com/documentation/7/screenshots.html)

![](http://7xjuve.com1.z0.glb.clouddn.com/160424-KeyboardMaestro.png?imageView2/2/w/720)

---

用电脑难免有一些工作是单调重复的，比如:复制一行文本、一个单词到文件；边看 Kindle 边写代码，两个窗口频繁切换；等等各种重复类流程性工作。而 keyboard Maestro 无愧他「键盘大师」的称号，有了他，几乎所有重复类工作皆课一键完成。比如我常用的「Alt + 程序首字母」切换程序，比 Command + Tab 切换可快多了，还有做 Keynote 时屏幕缩放，也是非常有用的，点 [这里](http://ww1.sinaimg.cn/mw690/60dc9a70gw1f0ar0znggvg20hs0b4qv5.gif) 看效果。

### [aText](http://www.trankynam.com/atext/)

![](http://7xjuve.com1.z0.glb.clouddn.com/160424-aText.png?imageView2/2/w/720)

---

无论你写代码还是写文章，肯定有很多重复的文本或 Code，用命令行就更不用多讲，而 aText 则是一个强大的别名工具，输入一个小小的别名，复杂的输入自动填充，无疑可以节省大量时间。比如：

- 进入自己的七牛上传文件夹，在终端输入 `;cg` 即跳出 `cd /Users/Scott/Documents/5.DataBank/qiniu`, 也就进入这个文件夹了。
- 连接局域网 Linux 的 Jupyter，`;jn` ==> `https://192.168.31.199:8889`
- 写 reStructuredText 时的大标题， `;==` ==> `=========================`

### [A Better Finder Rename](http://www.publicspace.net/ABetterFinderRename/)

![](http://7xjuve.com1.z0.glb.clouddn.com/160424-ABetterFinderRename.png?imageView2/2/w/720)

---

> 文件命名看似简单，但其实这体现了你对你的对象在当前体系中的关系/作用的理解深度，往大了说，命名还直接反映了你对整个资源结构的理解.
>
> 统一的命名规约，对团队，尤其对团队知识管理来说，非常重要。否则创建一文件，队友很可能难以识别所含内容。
>
> -- 闪闪

好的命名规约，不仅对团队知识管理重要，对个人今后的搜索、复用都是极为重要的，比如我个人的博客配图，就喜欢用 年月日开头，这样当我看到博文日期，我就知道那天的图有哪些。而 A Better Finder Rename 则是一款非常强大的批量命名工具，如图所示的文件名首批量添加字符，甚至自定义 Text 文件导入批量命名等复杂需要都能用他实现。

### [Paste](http://pasteapp.me/)

![](http://7xjuve.com1.z0.glb.clouddn.com/160424-Paste.png?imageView2/2/w/720)

---

批量复制总比单个快，而 Paste，一次性复制多个后，便可轻松点选了。

## 写作类

我的写作流程大致是这样的，供参考：

- Atom 写初稿. 理由： 多行编辑、替换等非常方便. 快捷键： Command + Shift + L 多行编辑，修改标题、列表等可以用; Command + L 选中当行; Command + F 查找， Alt + Enter 在查找后统一替换。
- Dropzone + 七牛插件 + aText， 一键拖拽， 一键贴入. Dropzone + 七牛插件设置好后，便可一键拖拽，并生成一个复制好的图片链接，所以可以先在 aText 编辑一个 类似这样的 "!\[]({#clipboard -paste})" 快捷，比如把快捷名设为 ";qi"， 等你切换回 Atom， 输入 别名 `qi` 即可。
- Textmate 查看组块排版. 快捷键 Command + Option + 1， 显示所有标题。

### [Atom](https://atom.io/)

![](http://7xjuve.com1.z0.glb.clouddn.com/160424-Atom.png?imageView2/2/w/720)

---

GitHub 亲娘生的，是我见过的对 Markdown 文档的渲染效果最好的，写作用他就对了。

### [Dropzone](https://aptonic.com/)

![](http://7xjuve.com1.z0.glb.clouddn.com/160424-Dropzone.png?imageView2/2/w/720)

---

配合七牛一键盘传图，具体配置看这里 [Mac OS 图床运用优化模式](http://azeril.me/blog/How-To-Use-Image-Hosting-Quickly.html)

### [Textmate](https://macromates.com/)

主要是排版用。输入框不跟随光标这个问题一直没解决。

## 插件类

### [Pushbullet]()

![](http://7xjuve.com1.z0.glb.clouddn.com/160424-Pushbullet.png?imageView2/2/w/720)

---

你还在用微信、qq 给自己传图吗？ Pushbullet 更方便。

### [Vimium](https://chrome.google.com/webstore/detail/vimium/dbepggeogbaibhgnhhndojpepiihcmeb)/[Vimfx](https://addons.mozilla.org/en-US/firefox/addon/vimfx/)

![](http://7xjuve.com1.z0.glb.clouddn.com/160424-Vimum.png?imageView2/2/w/720)

---

刚刚说到尽量不用鼠标，浏览器这种有大量链接的地方看似不现实，所幸在 Chrome 有 Vimium, Firefox 有 Vimfx。

### 其他插件
[Image Picker](https://addons.mozilla.org/en-US/firefox/addon/image-picker/) 是Firefox 下的批量下图神器；[Adblock](https://chrome.google.com/webstore/detail/adblock/gighmmpiobklfepjocnamgkkbiglidom?hl=zh-CN) 让广告已成往事；[Ginger](http://www.gingersoftware.com/zh) 语法检查工具。

## Haiker 类

### [iTerm 2](https://www.iterm2.com/)

![](http://7xjuve.com1.z0.glb.clouddn.com/160424-iTerm2.png?imageView2/2/w/720)

---

这是一个比 Mac 自带终端 Terminal 好用的多的终端仿真器。

### [Oh My ZSH](http://ohmyz.sh/)

![](http://7xjuve.com1.z0.glb.clouddn.com/160424-OhMyZSH.png?imageView2/2/w/720)

---

如图所言，我的终端从来没有这么好用过。

### [Homebrew](http://brew.sh/)

![](http://7xjuve.com1.z0.glb.clouddn.com/160424-Homebrew.png?imageView2/2/w/720)

---

用过 Linux 的人都知道，包管理工具 APT 极为好用，下载工具、程序通常一行代码就搞定了，而 Homebrew 也可以做到，让你彻底摆脱 Win 的「无数个下一步操作」思维。

### [Fugu](https://sourceforge.net/projects/fugussh/files/)

虽说可以在 Mac 的终端可以直接用 FTP 或 SFTP 直连服务器，但 Fugu 这种客户端工具在有些时候也是极为有用的。

## 其他

### [欧陆词典](http://www.eudic.net/eudic/mac_dictionary.aspx)

![](http://7xjuve.com1.z0.glb.clouddn.com/160424-eudic.png?imageView2/2/w/720)

---

欧路词典无疑是 Mac 下最好用的词典之一，导入生词本，词库等都很方便。

### [Sip](http://theolabrothers.com/sip/pro/)

![](http://7xjuve.com1.z0.glb.clouddn.com/160424-Sip.png?imageView2/2/w/720)

---

对于一个美控来说，偶尔还是要取取色，做做设计的， Sip 则可以让你方便取色、生成色板等，可以极好的配合 Ai 使用。

### [Workflowy](https://workflowy.com/)

![](http://7xjuve.com1.z0.glb.clouddn.com/160424-Workflowy.png?imageView2/2/w/720)

---

之前一直用 Evernote 列清单，但 Evernote 太重，不适合用来写笔记、记录生活点滴想法。所以现在换成了 Workflowy ，足够简洁，写读书笔记时，用来整理知识点也非常不错。

### [Bartender 2](https://www.macbartender.com/)

![](http://7xjuve.com1.z0.glb.clouddn.com/160424-Bartender.png?imageView2/2/w/720)

---

强迫症隐藏小图标用的。

### [Flux](https://justgetflux.com/)

低蓝光护眼神器。

## 小结：

关注本质，而非形式。
