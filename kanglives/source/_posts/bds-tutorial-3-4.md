---
title: 【教程】从零开始一步步搭建我的世界基岩版服务器 (Linux) 3~4
date: 2023-01-30 12:42:14
tags: 
    - Minecraft Bedrock Edition
    - 我的世界基岩版 
    - Linux
    - 云服务器
    - BDS
category: 教程
cover: https://s2.loli.net/2023/01/27/3iw5EFolYPA67mV.png
---
### 【教程】从零开始一步步搭建我的世界基岩版服务器 (Linux) 3~4

在上一节我们按需要调整了 **server.properties** 的相关设置。但是还有一些问题没有说到，这里补充一下。

##### 3. 其他的设置

###### 3.1 设置OP

我们可以在终端里面输入

```shell
op 玩家名称
```

来给予玩家 **OP权限**

###### 3.2 设置白名单

如果在上一节我们开启了白名单，那么我们可以在终端里输入

```shell
allowlist add 玩家名称
```

来添加白名单，如果想移除某人的白名单，可以在终端里输入

```shell
allowlist remove 玩家名称
```

###### 3.3 显示坐标 && 死亡不掉落 && 生物破坏

###### 3.3.1 显示坐标

输入下面的指令即可开启显示坐标

```shell
/gamerule showCoordinates true
```

###### 3.3.2 死亡不掉落

输入下面的指令即可开启死亡不掉落

```shell
/gamerule keepInventory true
```

###### 3.3.3 生物破坏

输入下面的指令即可开启生物破坏

```shell
/gamerule mobGriefing true
```

**注意：关闭生物破坏可以防止苦力怕破坏地形，但是也会导致村民无法拾取食物进行繁殖和进行破坏地形的工作例如种地，所以需要灵活考虑，或者使用命令方块来进行苦力怕防爆**

##### 4. 地图设置

如果想要把自己的地图或者下载的地图在服务器里玩，那么我们就需要在服务器里进行一些设置。

###### 4.1 上传地图

如果我们想使用自己的地图，那么我们第一步是把地图导出。

![](https://s2.loli.net/2023/01/27/3iw5EFolYPA67mV.png)

导出的地图后缀名为.mcworld，它可以被当作zip压缩包。BDS并不能一键导入mcworld，所以让我们先把他的后缀名改为.zip。

这时候我们就遇到了一个问题：怎么把文件上传到服务器呢？

我们可能会想到ftp协议，搭建一个ftp服务如何？可行，但是太麻烦了。

我们的ssh通常都会附带一个sftp，用它就能管理服务器的文件。

因为面板和大部分ssh图形化客户端都附带了sftp管理文件的功能，所以这里着重讨论纯命令行的sftp如何操作。

跟ssh类似，在Windows10及以上都自带了sftp的命令。让我们先打开Windows终端。

然后输入

```powershell
sftp
```

会输出

```shell
usage: sftp [-46AaCfNpqrv] [-B buffer_size] [-b batchfile] [-c cipher]
          [-D sftp_server_path] [-F ssh_config] [-i identity_file]
          [-J destination] [-l limit] [-o ssh_option] [-P port]
          [-R num_requests] [-S program] [-s subsystem | sftp_server]
          destination
```

**懒了，语法不解释了，网上有很多教程**

就是先把**地图的zip文件**上传到**worlds**目录里面

然后**cd**到worlds目录，再用**unzip**解压。假设我们上传的地图叫做**test.zip**

然后我们世界的名字也叫**test**

```shell
mkdir test && unzip test.zip test/
```

这样就成功解压了我们的世界，然后可以回到上一级目录把压缩包删掉

```shell
cd ..
rm test.zip
```

之后再去**server.properties**修改我们要开的世界名称为**test**

```properties
level-name=test
```

现在我们再开启服务器就能玩到我们上传的世界了

###### 4.2 开启实验性

这个我在之前的文章有讲，这里不再赘述

[「教程」如何在BDS开启实验性内容（不用下载存档） | KangLives Blog](https://kanglives.top/post/minecraft-bedrock-server-enable-experiment-options)

##### 5. 资源包和行为包

**原版生存有点单调乏味？**

**不如搞点Addons玩玩！**

假如我们现在从网上下载了一个后缀名为 .mcpack 的资源包或者行为包

和地图一样，第一步修改后缀名为 .zip ，然后上传到服务器的地图的 behavior_packs(行为包) 或者 resource_packs(资源包) 目录内，然后使用unzip解压

假如我们有一个 test.mcpack 的行为包

首先改名为 test.zip

然后上传到我们名为 test 的地图的 behavior_packs 目录内

**worlds/test/behavior_packs/test.zip**

之后新建一个目录并解压到里面，我们这里新建 test行为包 目录

```shell
mkdir test行为包
```

使用unzip解压

```shell
unzip test.zip test行为包/
```

这还没完，我们必须在世界的 world_behavior_packs.json 里面把我们添加的行为包写上去

**world_behavior_packs.json**:

```json

[
	{
		"pack_id" : "5b4692ad-38aa-4c0d-87da-43fd3de5ca8f",
		"version" : [ 1, 0, 0 ]
	}
]
```

如果没有这个文件的话，可以自己创建一个。

**pack_id** 和 **version** 都可以在行为包内 **manifest.json** 文件里找到

**manifest.json**:

```json
{
    "format_version": 2,
    "header": {
        "description": "description",
        "name": "test",
        "uuid": "5b4692ad-38aa-4c0d-87da-43fd3de5ca8f",
        "version": [ 1, 0, 0 ],
        "min_engine_version": [ 1, 14, 0 ]
    },
    "modules": [
        {
            "description": "description",
            "type": "resources",
            "uuid": "9f47a40b-2627-45fa-bb9f-52fd112716d4",
            "version": [1, 0, 0]
        }
    ]
}

```

> "uuid": "5b4692ad-38aa-4c0d-87da-43fd3de5ca8f",
>  "version": [ 1, 0, 0 ],

**uuid** 对应 **pack_id**

**注意：如果有多个行为包，记得在每个条目结尾的花括号后面加上逗号，这个逗号不能漏了，如果漏了逗号再开服，会导致地图里面所有Addon物品和生物都消失**

例如：

```json
[
	{
		"pack_id" : "5b4692ad-38aa-4c0d-87da-43fd3de5ca8f",
		"version" : [ 1, 0, 0 ]
	},
    {
		"pack_id" : "fd3c0dad-38aa-4c0d-87da-43fdd3d5c7da",
		"version" : [ 1, 0, 1 ]
	}
]
```

> "version" : [ 1, 0, 0 ]
>  }, **<---**
>  {
>  "pack_id" : "fd3c0dad-

**资源包同理，只不过是把 behavior 换成了 resource**