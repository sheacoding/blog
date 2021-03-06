---
title: screen命令基本用法详解
id: 1
categories:
  - Linux
date: 2016-01-19 20:02:48
tags:
  - Linux
---

在服务器上搭建一些服务的时候，经常要用到screen命令。这是因为一般情况下我们只会开一个连接服务器的控制台，但是某些服务（比如minecraft服务器）开启的时候需要占据一整个控制台，如果关闭了就会导致进程终止。这就成了类似单进程的效果。而screen命令就是为了能够在服务器上开启多个控制面板(screen)，用以容纳不同的任务。

## 安装

ubuntu软件库里就有，直接下载：
```
$sudo apt-get intstall screen
```
## 基本概念

话说我看了网上很多的教程，发现他们大多是把命令啊参数啊什么的列一遍，却并没有告诉我他这里不同窗口的关系，导致被screen耍的云里雾里的。在蛋疼了好久之后，我终于大概晓得了他实质的运作机制（估计是我开始想偏了）。。。

首先我们要明确三个窗口的概念（话说这三个窗口我是摸索了好就才理清楚的～～～汗。。）

### 默认窗口

所谓默认窗口就是我们没有用screen命令时的那个干干净净的窗口，不能使用等会介绍的screen命令下的任何快捷键。（这个不是废话）

### screen界面

screen界面就是当我们进入screen空间下的，占用了一个进程pid的一个界面。这个界面有他自己的名字，有他自己内部交互的快捷键，能够拥有很多不同的子终端的界面。也可以理解成独立于默认窗口之外的窗口。注意，是每一个screen界面都能拥有很多的子窗口，而我们的电脑能同时拥有很多不同的screen界面。

### 子窗口

每一个子窗口都是附属与一个screen界面下的，他们互不影响，能够分别执行不同的命令，这是整个screen命令的精华所在。工作时，我们需要做的就是选定某一个screen，并且选定一个子窗口。这样如果我们需要换一个界面的话，我们就只需要更换另一个子窗口就行了。

可能不太清楚，但是只要晓得有这么三个层次就好了（不要当成只有两个层次！！）

## 基本命令

既然有了三个层次，那么我们是不是要考虑三种不同的窗口下的命令呢？其实不是，因为每一个screen都得有至少一个的子窗口，所以我们只要考虑两个层次下的命令就可以了，一个是面对不同screen界面的切换，另一个是面对同一个screen界面下不同终端之间的切换。

### 面向screen级别的命令

**１、新建一个screen :**

可以直接输入screen 来打开一个带有默认名字的screen界面，也可以加上 -S 参数来亲自制定一个好听的名字。然后系统就会切进当前的screen了。

**２、查看当期存在的screen:**

输入`screen -ls` 可以查看当前存在的screen 界面，比如：
```
myths@Business:~$ screen -ls
There are screens on:
        4545.s2 (2016年01月19日 19时40分28秒)   (Attached)
        4506.s1 (2016年01月19日 19时40分22秒)   (Detached)
        4487.pts-8.Business     (2016年01月19日 19时40分15秒)   (Attached)
3 Sockets in /var/run/screen/S-myths.
```
他会显示当前的每一个screen界面，对于每一个界面，他的显示格式是：<span class="lang:default decode:true crayon-inline ">pid.name (time) (status)</span> ，前面的不解释，最后的那个状态有(Attached) 和(Detached) ，表示是否已经连接进来（？其实不太懂这个意思，没有连接进来那就连接进来呗～有啥必要非得分开看呢。。。不过有一个明显的区别就是，当你断开主界面然后重新登录的时候，所有的screen都是显示Detached的。而如果你想进入某一个screen，你首先得把他的状态变成Attached才行）。

**３、关闭和选择screen:**

想要关闭一个screen 就要先选择并且进入这个screen。

首先用screen -ls查看你想要选的screen:

如果他的状态是Detached的，我们需要输入`screen -r <screen的pid>` ，来进入这个screen ;

如果他的状态是Attached的，我们就要输入　`screen -d <screen的pid>` ，来使他"Dettached"，然后再输入`screen -r <screen的pid> `，来进入这个screen。(必须先detached，否则是进入去的)。

进入了你想要关闭的screen之后，就可以直接输入exit，或者Ctrl+d，来终止这个界面了。成功终止后，如果有其他处于Attached状态的screen界面，他就会跳到那个界面中，如果没有，他就会跳到默认界面上。

**４、清除损坏的screen :**

如果由于某些情况导致一个screen的状态处于Dead（很可能是程序出错或者强行删除进程锁之类的。。），我们无法attached进去，那么我们只需要输入`screen --wipe` ，就可以删除这些进程了。

### 面向子窗口切换的快捷键：

在一个screen下，我们可以方便的创建多个子窗口，这些窗口之间转换的操作都是通过Ctrl+a再加参数的组合快捷键实现的。

**１、新建子窗口：**

可以通过`Ctrl+a+c` 组合快捷键在当前的screen下创建一个新的子窗口，并跳转到上面去。

**２、查询当前的子界面状态：**

可以通过Ctrl+a+w组合快捷键，在屏幕的左下角就会出现类似：`0-$ bash 1*$ bash `之类的样式。这个表示当前screen下有两个子窗口，当前在的是１号子窗口。当然，这个子窗口的数目可以有很多。

**３、切换子窗口：**

方法很多，可以通过`Ctrl+a+a`组合快捷键在最近的两个窗口中切换，也可以用`Ctrl+a+n` 切换到下一个窗口，或是`Ctrl+a+p` 切换到上一个窗口，或是`Ctrl+a+num` 切换到第num个窗口。。。

**４、锁定当前窗口：**

可以用`Ctrl+a+x` 来锁住当前的界面，此时所有的快捷键都无法使用，直到输入了密码。。

**５、关闭当前窗口：**

这个可以直接用`Ctrl+d`  或者`exit` 来关闭，当这个窗口是现有screen的最后一个窗口时，就会触发上面讲的关闭当前screen的事件了。

**６、暂离当前screen:**

这个也是screen命令的精髓，用组合键`Ctrl+a+d` 就能detached当前的screen，回到默认界面。


大概就是这些操作，其实还挺绕人的，有个小注意点，就是我们可以通过`Ctrl+a+w` 命令来测试下当前窗口是不是处于screen状态下，如果不是的话，贸然使用`Ctrl+a+d` 可是会直接退出登陆的额。。