---
title: 「教程」从零开始一步步搭建我的世界基岩版服务器 (Linux) 0~1
date: 2023-01-25 23:20:54
tags: 
    - Minecraft Bedrock Edition
    - 我的世界基岩版 
    - Linux
    - 云服务器
    - BDS
category: 教程
cover: https://i.postimg.cc/33cr98pt/1674660038130.png?dl=1
---
### [教程]从零开始一步步搭建我的世界基岩版服务器 (Linux) 0~1

此教程将一步步在Linux平台上搭建我的世界基岩版服务器(BDS, 即 Bedrock Dedicated Server )，**不包括**LL加载器的内容，但是会尽可能提到过程中出现的种种问题以及它们的解决方案，也包括修改行为包等一系列扩展内容。**本教程仅为个人经验，若有问题请以官方文档或实际情况为准。**

##### 0.准备服务器

在搭建BDS之前，我们首先需要拥有一台服务器。什么是服务器呢？一台电脑+公网就可以当作一个服务器。

**（当然这里要看需求，仅作好友联机的话也有人会选择手机+内网穿透来开服，本教程并不考虑这种情况，而是专注于一般使用Linux服务器来开服的情况）**

但是绝大多数人都没有公网，而且要做到电脑24h不间断开机也不容易。所以咱们更多会选择购买云服务器来搭建BDS。

售卖云服务器的平台有很多，有专业的云计算服务平台，比如腾讯云、阿里云等。也有专门提供MC专用服务器的平台，这里就不举例的，去MCBBS或者MineBBS里面就能看到它们的广告。

由于大平台会提供新用户免费试用的活动（通常都是1个月），所以我们可以先在这些平台免费领取一个服务器来试试手。

但是这些平台的服务器CPU主频通常都是2.4 GHz，总所周知，MC十分依赖单核的性能，尤其是现在高版本的服务器，对CPU主频的要求很高。而专门提供MC专用服务器的平台会在这个方面进行考虑，就是提供了更多高主频的选择。至于到底这么选择，还是要根据实际情况来考虑。

总结一下，准备服务器这块可以领取一个大平台免费试用的服务器来先尝试一下，其他的事情以后再考虑。

##### 1.配置环境和安装BDS

###### 1.1 安装系统和连接到服务器

拿到领到或者买到服务器之后，对于我们这个教程来说，需要安装Ubuntu系统，如果不知道选这个版本号的话，选最大的就可以了。下文给出的命令我都是在 **Ubuntu 20.04.2 LTS** 上测试执行的

系统也准备好之后，就可以使用ssh来连接到终端了。在这里连接的途径有很多，如果手头上有ssh工具那就用，如果没有的话，Windows 10 及以上的系统都自带了ssh。只需要在 Windows终端 里输入 ssh 就能看到命令的提示。比如 ssh root@1.2.3.4 就是以root用户连接到IP地址为 1.2.3.4 的服务器。

如果不会用命令行ssh的话，可以选择一些软件，这里推荐一些我用过比较好的ssh工具:

- FinalShell
  
- MobaXterm:
  
- WindTerm: [kingToolbox/WindTerm: A professional cross-platform SSH/Sftp/Shell/Telnet/Serial terminal. (github.com)](https://github.com/kingToolbox/WindTerm)
  

当然最推荐的还是第三个 WindTerm，前两个不开源，第二个还是付费的。而且FinalShell貌似还有安全问题...

如果这些软件也不会用，那就装个面板吧。可以是专用于MC的面板，比如 MCSM: [MCSManager (github.com)](https://github.com/mcsmanager)。也可以是通用的服务器面板，比如 BT [宝塔面板 - 简单好用的Linux/Windows服务器运维管理面板 (bt.cn)](https://www.bt.cn/new/index.html)

总之，只要能通过ssh连接上服务器，那么这一小节的任务就完成啦！

*连上服务器之后可以用这条命令打印出好看的服务器信息哦*

```shell
sudo apt update && sudo apt install neofetch && neofetch
```

###### 1.2 下载并解压BDS文件

我们连接到服务器之后，在安装东西之前，先更新一下软件源吧。

```shell
sudo apt update
```

然后创建一个目录用于存放我们的BDS文件

```shell
mkdir bds
```

再进入这个目录

```shell
cd bds
```

之后就是从网上下载BDS文件，这里可以使用wget或者curl。我倾向于wget。

先在自己电脑上打开这个网站: [基岩版服务器下载 | Minecraft](https://www.minecraft.net/zh-hans/download/server/bedrock)

![](https://i.postimg.cc/0xJXSRws/1674656945707.png?dl=1)

找到 **适用于 UBUNTU (LINUX) 平台的 MINECRAFT 专属服务端软件。**

在下面的下载按钮上右键单击，然后选择复制链接。

然后先在服务器终端里输入

```shell
wget
```

再打个空格，然后粘贴你复制的链接（注意，在Linux上面 Ctrl+C 相当于 Alt+F4，是终止程序，复制粘贴的快捷键是 Ctrl + Shift + C 和 Ctrl + Shift + V），最终的命令应该是这样的：

```shell
wget 你复制的链接
```

执行成功之后，可以使用ls来查看当前目录下的文件。

```shell
ls
```

如果一切正常，那么你就能看到你下载的文件了。例如:

```shell
bedrock-server-x.xx.xx.xx.zip
```

现在你需要做的就是解压，我相信云服务器应该都默认安装了unzip软件包，如果你输入unzip提示没有这个命令的话，可以输入下面的命令来安装它。

```shell
sudo apt install unzip
```

然后再解压这个文件，先输入

```shell
unzip
```

再打个空格，然后输入你刚才通过ls看到的文件名，例如：

```shell
unzip bedrock-server-x.xx.xx.xx.zip
```

你可以先输入 bedrock , 然后按一下 Tab 就能自动补全文件名。

如果你成功解压，再次输入ls之后多出了很多文件的话，那么恭喜你完成了这一小节。

###### 1.3 配置启动脚本以及安装一些需要的软件包

解压完成之后，我们可以简要浏览一下目录的结构

- behavior_packs 存放行为包的目录
  
- resource_packs 存放资源包的目录
  
- config 用于Script的配置目录，详情请见ScriptAPI
  
- definitions 一个目录，不清楚其作用
  
- worlds 存放世界的目录，运行一次服务器才会出现
  
- allowlist.json 白名单配置文件
  
- bedrock_server_how_to.html 官方的开服教程，是英文
  
- bedrock_server_symbols.debug 调试符号文件，这里不涉及
  
- permissions.json 玩家权限配置文件
  
- release-notes.txt 版本更新日志
  
- server.properties 服务器配置文件
  

记得之前下载BDS的网站吗？

![](https://i.postimg.cc/Wjm8gQVF/1674658708581.png?dl=1)

这里就是我们开服所需的命令了，但是每次都要输入这么长串的命令很麻烦，使用我们可以创建一个启动脚本。

为了减少学习成本，我们可以使用nano来编辑文件。

首先安装nano软件包

```shell
sudo apt install nano
```

然后创建一个名为 run.sh 的脚本文件并编辑

```shell
nano run.sh
```

在里面粘贴上文提到的开服命令

```shell
LD_LIBRARY_PATH=. ./bedrock_server
```

然后按 Ctrl+X 保存并退出

这时候我们就一键创建好启动脚本了，但是运行的时候会出现权限错误，所以我们还需要给这个文件设置运行权限。（在Linux中输入 ./文件名 可以运行文件）

```shell
sudo chmod +x run.sh
```

设置完权限，我们就可以运行服务器了。

```shell
./run.sh
```

如果一切正常，我们能看到类似如下的输出：

```log
NO LOG FILE! - setting up server logging...
[2023-01-25 23:05:46:379 INFO] Starting Server
[2023-01-25 23:05:46:380 INFO] Version 1.19.41.01
[2023-01-25 23:05:46:381 INFO] Session ID 8d66dede-0992-4868-a59f-c2cfc1d9b709
[2023-01-25 23:05:46:382 INFO] Level Name: Bedrock level
[2023-01-25 23:05:46:419 INFO] Game mode: 0 Survival
[2023-01-25 23:05:46:420 INFO] Difficulty: 1 EASY
[2023-01-25 23:05:46:490 INFO] opening worlds/Bedrock level/db
[2023-01-25 23:05:47:352 INFO] IPv4 supported, port: 19132
[2023-01-25 23:05:47:354 INFO] IPv6 supported, port: 19133
[2023-01-25 23:05:47:659 INFO] Server started.
[2023-01-25 23:05:47:689 INFO] Please note that LAN discovery will not function for this server.
[2023-01-25 23:05:47:690 INFO] Server IP must be specified in Servers tab in game. 
[2023-01-25 23:05:47:710 INFO] ================ TELEMETRY MESSAGE ===================
[2023-01-25 23:05:47:711 INFO] Server Telemetry is currently not enabled. 
[2023-01-25 23:05:47:711 INFO] Enabling this telemetry helps us improve the game.
[2023-01-25 23:05:47:712 INFO] 
[2023-01-25 23:05:47:712 INFO] To enable this feature, add the line 'emit-server-telemetry=true'
[2023-01-25 23:05:47:712 INFO] to the server.properties file in the handheld/src-server directory
[2023-01-25 23:05:47:712 INFO] ======================================================
```

如果能看到 Server started. 并且没有出现 ERROR，那么恭喜你完成了这一小节...的前半部分。

这时候如果我们断开服务器的ssh连接，再重新连接上之后，就会发现一切又回到了最初。这是因为断开连接相当于注销了登录，运行的程序就被终止了。要解决这个问题，我们可以安装 screen 软件包来创建会话，这样就可以让程序在里面一直运行。

```shell
sudo apt install screen
```

安装完之后，输入以下命令来创建一个名为bds的screen

```shell
screen -S bds
```

然后在里面再运行服务器

```shell
cd bds && ./run.sh
```

如果要离开screen会话，可以按 Ctrl+A+D , 这样不会终止里面的程序

想再次进入的话可以输入：

```shell
screen -r bds
```

输入以下命令可以列出当然创建的所有screen会话

```shell
screen -list
```

如果你成功执行了上面命令，并且在重新连接ssh之后BDS仍然保持运行的话，那么恭喜你完成了这一小节。