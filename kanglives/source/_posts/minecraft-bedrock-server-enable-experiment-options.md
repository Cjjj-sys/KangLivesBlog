---
title: 「教程」如何在BDS开启实验性内容（不用下载存档）
date: 2023-01-25 06:15:55
tags: Minecraft Bedrock Edition, 我的世界基岩版, NBT, BDS
category: 教程
cover: /img/enable-experiment.png
---
#### 「教程」如何在BDS开启实验性内容（不用下载存档）
  我们都知道Minecraft基岩版由于使用C++编写，反编译难度较高，所以几乎没有完善的第三方Mod加载器。大部分的Mod(即Addon)都是依附于官方提供的接口。而像是一些比较新的接口通常不会在正式版中默认启用，必须在世界设置里打开“实验性内容”相关选项才能使用。这就给不少开服的小白带来了困扰，由于BDS没有方便的图形化界面（它甚至没有配置文件来配置实验性功能的相关内容），所以难以直接在BDS里修改存档的实验性内容相关设置。通常的做法是把存档下载到本地，然后导入本地的Minecraft基岩版客户端，修改世界设置后再导出为.mcworld文件重新上传到服务器。这种方法行之有效，但是却比较繁琐。那么有没有一种简单的方法来修改这些设置呢？

  在回答这个文件之前，让我们先去翻一翻 Minecraft 中文Wiki 看看我们世界的实验性设置到底存储在哪里。链接: [https://minecraft.fandom.com/zh/wiki/基岩版世界格式](https://minecraft.fandom.com/zh/wiki/基岩版世界格式)

![image.png](assets/image-20230125140907-98gfw4h.png "level.dat")

  可以看到，我们世界的设置都存放在 level.dat 文件内，而 level.dat 为 NBT 格式文件。那么我们就有了思路: 能不能用 NBT 编辑器 直接修改 level.dat 来开启实验性内容呢？

  鉴于不少人手上都有经典的 NBT Explorer ，所以我们先使用 NBT Explorer 试一试。 

![image.png](assets/image-20230125140958-ehtvogj.png "NBTExplorer")

  但是很遗憾，由于 NBT Explorer 已经很久没有更新了，它似乎并不能正确读取 Minecraft基岩版 的 level.dat 文件。那么我这里我找到一个比较新的 NBT 编辑器 —— NbtStudio，在它的Github主页也明确写了支持 Minecraft基岩版 level.dat 文件的编辑。 

![image.png](assets/image-20230125141026-pl1j12z.png)

  可以看到它是基于 NBTExpolorer 开发的，所以操作页面基本与 NBTExpolorer 一致。 

![image.png](assets/image-20230125141047-rg81s6q.png "使用 NBT Studio 打开 level.dat")

  使用 NBT Studio 打开 level.dat 之后，我们看到了熟悉的NBT文件结构，也找到了心心念念的实验性内容设置项，在这里我已经把它们全部开启了（即全部的值都改为1），在退出之前别忘了保存。如果你是使用Windows来运行BDS，那么可以直接在服务器里使用 NBT Studio 进行操作，如果你是使用Linux来运行BDS或者是面板服的话，可以单独把 level.dat 文件下载下来，编辑完成之后再上传覆盖。


  这样不需要把庞大的存档下载到本地进行一系列繁琐的操作就可以轻松实现在BDS上开启实验性内容啦。
:::tip
如果没有找到实验性选项，可以手动添加那几个选项，切后面的数字为一，然后再把原来的选项删除。
:::
  附上 NBT Studio 的下载链接: [https://github.com/tryashtar/nbt-studio/releases](https://github.com/tryashtar/nbt-studio/releases)