---
layout: post
title: 让 CapsLock 键更实用
subtitle:   键盘重映射
date:       2016-01-01
author:     "Scott"
header-img: "img/post-bg-unix-linux.jpg"
tags:
    - 工具箱
    - OS
---
## 缘起

12年，我当时还在做销售工作，虽然从事那行已好几年，但总感觉自己的销售知识体系不够系统，于是向一个销售老大哥请教——如何提升自我的销售能力。他教了我一个很简单的办法——把自己跟客户的销售对话录下，然后用录音分析软件把每句话形成文字，其实就是给自己的话配上字幕。

办法虽笨，却极其有效，若你有很多口头禅想改却一直改不了，不妨也试试。很多时候自己说过什么话是意识不到的，而写作清晰与否，一看便知。

之后，老大哥给我一份长达两小时的「咨询录音」，让我敲出来，😓，他是语速超快的那种。于是，我便开始了「打字」受虐之旅。

我当时用的是:狼蛛牌，会发光哦!
![](http://7xjuve.com1.z0.glb.clouddn.com/image/160101-lanzhu.png?imageView2/2/w/600)

现在想想，当时的品味实在不咋地.

敲了 4 个白天，2 万多字，字敲完了，8 个手指也不行了，吃饭碗都拿不住，可以想象那个渣渣键盘有多难用。从那之后我很长一段话时间都不敢一次性写长文。

## 键盘

14 年初，我想学编程。考虑到要敲大量代码，且有了之前的惨痛教训，我得先把硬件换了。Google 上查看了大量的机械键盘知识后，锁定了 Cherry - 3000(红轴).放在家里用，于是家里很爽，公司很痛苦。

![](http://7xjuve.com1.z0.glb.clouddn.com/image/160101-cherry.png?imageView2/2/w/600)

经过几天的煎熬后，没办法，继续购了 Filco - 104(侧刻红轴)，从此以后，我便过上了与机械键盘在一起的幸福生活。

![](http://7xjuve.com1.z0.glb.clouddn.com/image/160101-filco104.png?imageView2/2/w/600)

办公室和家里都用机械之后，码字、敲代码的确很爽，但还有个问题一直困扰着我——Escape、方向键程太远，这导致我用 vimium 浏览网页，以及改文章都很痛苦，我可不想老离开中心键位。

## 软件
直到有一天，看到这几篇文章：[让 Caps lock 键更加实用](http://eamesliu.com/post/101419356939/caps-lock-key-to-hyper)、[A useful Caps Lock key](http://brettterpstra.com/2012/12/08/a-useful-caps-lock-key/)、[A Modern Space Cadet](http://stevelosh.com/blog/2012/10/a-modern-space-cadet/#modern-software) ，Steve 的文章里提到，键盘上面的按键分为三种：

> - 一种是按下去之后会改变其它按键的状态，但是只按下它们自己通常不会起任何作用，比如 Shift、Control。
- 第二种按键是你按下之后释放，它自己就会形成一定功能，但是你通常不会按住它来重复它的功能，比如 Esc。
- 第三种按键是你有时候只会按一下，有时候需要重复按（或按住），比如按住空格键会输出一连串空格，按住 Delete 键就会就会删除一连串文字。

Steve 提到，第三种按键方式比较正常，而第一种和第二种则是截然不同的按键方式，很多键盘设计的 Capslock 键则是个天大的笑话——占据了最核心的键位却只能用来切换大小写。当然，也有些例外，比如日本的 [HHKB pro2](https://www.zhihu.com/question/26598476)。于是，他把 Capslck 键改造成了一个超级键，并将其命名为 Hyper。

我借鉴了他们的思路，但也修改了一些东西。我的做法如下：

首先下载 [Seil](https://pqrs.org/osx/karabiner/seil.html.en)、[Karabiner](https://pqrs.org/osx/karabiner/index.html.en).

然后到系统偏好设置里把 Capslock 键设置为「无操作」。
![](http://7xjuve.com1.z0.glb.clouddn.com/image/160101-no_action.png?imageView2/2/w/720)

接着，打开 [Seil](https://pqrs.org/osx/karabiner/seil.html.en)，双击「Change the capslock key」的「key codeh」那行，把默认的 51 改为 80， 80 对应 F19 键，不会跟其他键造成冲突。
![](http://7xjuve.com1.z0.glb.clouddn.com/image/160101-seil.png?imageView2/2/w/720)

再打开 [Karabiner](https://pqrs.org/osx/karabiner/index.html.en) 的「Misc & uninstall」，
![](http://7xjuve.com1.z0.glb.clouddn.com/image/160101-karabiner01.png?imageView2/2/w/720)

点击 「open private.xml」，会跳出一个文件，把下方代码贴进去替换。如果没显示，请「越过城墙」。
<script src="https://gist.github.com/scottming/a9a756a9ed35467b18a4.js"></script>

回到「Change key」界面，点击「Reload XML」刷新，然后左边勾选即大功告成。
![](http://7xjuve.com1.z0.glb.clouddn.com/image/160101-karabiner02.png?imageView2/2/w/720)

我对 [文刀漢三](http://eamesliu.com/post/101419356939/caps-lock-key-to-hyper) 的代码做了一些修改：

- Hyper + H/J/K/L 为左、下、上、右，仿 Vim 模式。
- Hyper + N/; 分别是跳到行首、行尾。
- Hyper + I/M 分别是调到最上方、最下方。
- Hyper 单独按下时为 Escape，+ Space 则保留之前的切换大小写功能。

把键盘映射如上改完之后，我觉得至少有以下几个好处：

- 两手几乎不用离开中心键位，无论是码字、敲代码还是其他效率都会提升很多。
- 浏览网页，借助 Vimium 插件点击会更快，更爽。
- 设置 Hyper 键位，不会更传统的很多快键键造成冲突，可以凭空多出20个以上的自定义「键程短」的快捷键。比如我把欧路词典的「滑词翻译」设为 Hyper + E.

## 展望

### Keyboard maestro

OX 系统有个效率软件为 Keyboard maestro, 因为有了 Hyper 键，所以也可以多设置一些不冲突的 Hotkey.例如，Astrill 没有快捷控制 ON & OFF, 于是我把 Hyper + A 设为开关。
![](http://7xjuve.com1.z0.glb.clouddn.com/image/160101-keyboard_maestro.png?imageView2/2/w/720)

当然，Keyboard maestro 还有很多好玩的地方，希望大家多挖掘，多分享。

### Windows

Mac 有 karabiner，可 windows 上没有啊，公司还得用 win，虽然很痛苦，但是没办法。「改变我们能改变的，适应我们不能改变的」。于是 Google 了下 Karabiner 的替代产品，了解到了 AutoHotkey 这个脚本工具。到官网熟悉了下他的语法，参考其他人的类似脚本，写了下面这个脚本，跟 Mac 上的 karabiner 自定义差不多。

<script src="https://gist.github.com/scottming/5405b12eb2c69a4e0e54.js"></script>
如果你在 win 上也想改映射，下载 [AutoHotkey](https://autohotkey.com/) 把上方代码复制后，创建 .ank 后缀文件，右键以 AutoHotkey 运行即可。如果没显示，请「越过城墙」。
