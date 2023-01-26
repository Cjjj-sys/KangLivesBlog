---
title: 「教程」从零开始一步步搭建我的世界基岩版服务器 (Linux) 2
date: 2023-01-26 12:00:18
tags: 
    - Minecraft Bedrock Edition
    - 我的世界基岩版 
    - Linux
    - 云服务器
    - BDS
category: 教程
cover: https://s2.loli.net/2023/01/26/a4nf27hRLBdNqlx.png
---
### [教程]从零开始一步步搭建我的世界基岩版服务器 (Linux) 2

##### 2. 进入服务器并调整 server.properties 设置

###### 2.1 进入服务器

上一小节我们成功开启了BDS，并且让它在我们断开ssh之后依然保持运行。那么这一节就让我们进入自己的服务器看看还有什么事情需要做吧。

在你尝试使用ssh连接服务器的时候就已经得到了服务器的IP地址，接下来让我们打开Minecraft基岩版，并通过服务器IP地址加入服务器。首先打开**Minecraft基岩版**，找到**添加服务器**，然后在里面输入**我们服务器的IP地址**，端口保持**默认的19132**，名称可以**随便填**。例如：

```log
名称: 我的服务器
服务器地址: 1.2.3.4
端口: 19132
```

**注意：如果无法连接到世界，请检查服务器的防火墙设置，不同的云服务器平台有不同的设置入口，可以去网上查找对应的教程。确保打开了UDP协议的19132端口。**

如果我们成功进入了服务器，那么恭喜你完成了这一小节。

###### 2.2 调整 server.properties 设置

完成上一小节之后，就已经可以说我们搭建了一个 **无OP原版纯净生存服务器** 了。如果你想让自己的服务器更加个性化，可以进行下面的一些配置。

1.19.41 版本 BDS 内默认的 server.properties 内容如下：

```properties
server-name=Dedicated Server
# Used as the server name
# Allowed values: Any string without semicolon symbol.

gamemode=survival
# Sets the game mode for new players.
# Allowed values: "survival", "creative", or "adventure"

force-gamemode=false
# force-gamemode=false (or force-gamemode  is not defined in the server.properties)
# prevents the server from sending to the client gamemode values other
# than the gamemode value saved by the server during world creation
# even if those values are set in server.properties after world creation.
# 
# force-gamemode=true forces the server to send to the client gamemode values
# other than the gamemode value saved by the server during world creation
# if those values are set in server.properties after world creation.

difficulty=easy
# Sets the difficulty of the world.
# Allowed values: "peaceful", "easy", "normal", or "hard"

allow-cheats=false
# If true then cheats like commands can be used.
# Allowed values: "true" or "false"

max-players=10
# The maximum number of players that can play on the server.
# Allowed values: Any positive integer

online-mode=true
# If true then all connected players must be authenticated to Xbox Live.
# Clients connecting to remote (non-LAN) servers will always require Xbox Live authentication regardless of this setting.
# If the server accepts connections from the Internet, then it's highly recommended to enable online-mode.
# Allowed values: "true" or "false"

allow-list=false
# If true then all connected players must be listed in the separate allowlist.json file.
# Allowed values: "true" or "false"

server-port=19132
# Which IPv4 port the server should listen to.
# Allowed values: Integers in the range [1, 65535]

server-portv6=19133
# Which IPv6 port the server should listen to.
# Allowed values: Integers in the range [1, 65535]

view-distance=32
# The maximum allowed view distance in number of chunks.
# Allowed values: Positive integer equal to 5 or greater.

tick-distance=4
# The world will be ticked this many chunks away from any player.
# Allowed values: Integers in the range [4, 12]

player-idle-timeout=30
# After a player has idled for this many minutes they will be kicked. If set to 0 then players can idle indefinitely.
# Allowed values: Any non-negative integer.

max-threads=8
# Maximum number of threads the server will try to use. If set to 0 or removed then it will use as many as possible.
# Allowed values: Any positive integer.

level-name=Bedrock level
# Allowed values: Any string without semicolon symbol or symbols illegal for file name: /\n\r\t\f`?*\\<>|\":

level-seed=
# Use to randomize the world
# Allowed values: Any string

default-player-permission-level=member
# Permission level for new players joining for the first time.
# Allowed values: "visitor", "member", "operator"

texturepack-required=false
# Force clients to use texture packs in the current world
# Allowed values: "true" or "false"

content-log-file-enabled=false
# Enables logging content errors to a file
# Allowed values: "true" or "false"

compression-threshold=1
# Determines the smallest size of raw network payload to compress
# Allowed values: 0-65535

compression-algorithm=zlib
# Determines the compression algorithm to use for networking
# Allowed values: "zlib", "snappy"

server-authoritative-movement=server-auth
# Allowed values: "client-auth", "server-auth", "server-auth-with-rewind"
# Enables server authoritative movement. If "server-auth", the server will replay local user input on
# the server and send down corrections when the client's position doesn't match the server's.
# If "server-auth-with-rewind" is enabled and the server sends a correction, the clients will be instructed
# to rewind time back to the correction time, apply the correction, then replay all the player's inputs since then. This results in smoother and more frequent corrections.
# Corrections will only happen if correct-player-movement is set to true.

player-movement-score-threshold=20
# The number of incongruent time intervals needed before abnormal behavior is reported.
# Disabled by server-authoritative-movement.

player-movement-action-direction-threshold=0.85
# The amount that the player's attack direction and look direction can differ.
# Allowed values: Any value in the range of [0, 1] where 1 means that the
# direction of the players view and the direction the player is attacking
# must match exactly and a value of 0 means that the two directions can
# differ by up to and including 90 degrees.

player-movement-distance-threshold=0.3
# The difference between server and client positions that needs to be exceeded before abnormal behavior is detected.
# Disabled by server-authoritative-movement.

player-movement-duration-threshold-in-ms=500
# The duration of time the server and client positions can be out of sync (as defined by player-movement-distance-threshold)
# before the abnormal movement score is incremented. This value is defined in milliseconds.
# Disabled by server-authoritative-movement.

correct-player-movement=false
# If true, the client position will get corrected to the server position if the movement score exceeds the threshold.


server-authoritative-block-breaking=false
# If true, the server will compute block mining operations in sync with the client so it can verify that the client should be able to break blocks when it thinks it can.

chat-restriction=None
# Allowed values: "None", "Dropped", "Disabled"
# This represents the level of restriction applied to the chat for each player that joins the server.
# "None" is the default and represents regular free chat.
# "Dropped" means the chat messages are dropped and never sent to any client. Players receive a message to let them know the feature is disabled.
# "Disabled" means that unless the player is an operator, the chat UI does not even appear. No information is displayed to the player.

disable-player-interaction=false
# If true, the server will inform clients that they should ignore other players when interacting with the world. This is not server authoritative.
```

下面挑几个常用的设置项解释一下：

- **server-name**:
  
  ![](https://s2.loli.net/2023/01/26/I87YZX2K9cfexpD.png)
  
  ```properties
    server-name=Offline
  # 服务器名称，会在服务器列表那里显示n
  ```
  
- **gamemode**:
  
  ![](https://s2.loli.net/2023/01/26/XDUqlj5ObS9LgEm.png)
  
  ```properties
    gamemode=survival
  # 对新玩家的默认游戏模式
  # 可以填: survival(生存), creative(创造), 或 adventure(冒险)
  ```
  
- **difficulty**:
  
  ![](https://s2.loli.net/2023/01/26/obL63tMD5XiFCfq.png)
  
  ```properties
    difficulty=easy
  # 设置游戏难度
  # 可以填: peaceful(和平), easy(简单), normal(普通), 或 hard(困难)
  ```
  
- **allow-cheats**:
  
  ![](https://s2.loli.net/2023/01/26/WicnMzE59wAvy4b.png)
  
  ```properties
    allow-cheats=false
  # 是否激活作弊 (true/false)
  ```
  
- **max-players**:
  
  ![](https://s2.loli.net/2023/01/26/YzBNbLsXl4UOQ1v.png)
  
  ```properties
    max-players=10
  # 服务器最大人数
  ```
  
- **online-mode**:
  
  ```properties
    online-mode=true
  # 是否开启Xbox身份验证 (true/false)
  # 就是基岩版的正版验证，只允许登录了账号的玩家加入服务器
  ```
  
- **allow-list**:
  
  ```properties
    allow-list=false
  # 是否开启白名单 (true/false)
  # 开启白名单前必须开启Xbox身份验证
  ```
  
- **server-port**:
  
  ![](https://s2.loli.net/2023/01/26/4Ws2opSki1xwZme.png)
  
  ```properties
    server-port=19132
  # 服务器的IPV4端口
  # 数值范围: [1, 65535]
  ```
  
- **server-portv6**:
  
  ![](https://s2.loli.net/2023/01/26/XeGS54YCEHNDlV3.png)
  
  ```properties
    server-portv6=19133
  # 服务器的IPV6端口
  # 数值范围: [1, 65535]
  ```
  
- **view-distance**:
  
  ![](https://s2.loli.net/2023/01/26/lfvDBbcLO2oWFqR.png)
  
  ```properties
    view-distance=32
  # 最大可视距离
  # 数值范围: >=5
  ```
  
- **tick-distance**:
  
  ![](https://s2.loli.net/2023/01/26/9FuNWVwka4SnUCj.png)
  
  ```properties
    tick-distance=4
  # 模拟距离
  # 数值范围: [4, 12]
  ```
  
- **level-name**:
  
  ![](https://s2.loli.net/2023/01/26/aHQhUtexK67yMFq.png)
  
  ```properties
    level-name=Bedrock level
  # 服务器使用的地图名称
  # 注意：是地图文件夹的名称, 如果worlds文件夹内不存在则会创建地图
  # 不能包含的字符: /\n\r\t\f`?*\\<>|\":
  ```
  
- **level-seed**:
  
  ![](https://s2.loli.net/2023/01/26/tKdv5ekEX2S3FcJ.png)
  
  ```properties
    level-seed=114514
  # 服务器创建地图时使用的种子
  ```
  
- **default-player-permission-level**:
  
  ![](https://s2.loli.net/2023/01/26/wog3YuNG2J8beZd.png)
  
  ```properties
    default-player-permission-level=member
  # 玩家进入的默认权限
  # 可以填: visitor(访客), member(成员), operator(管理员)
  ```
  
- **texturepack-required**:
  
  ![](https://s2.loli.net/2023/01/26/i2rlMsAQt8pdNRo.png)
  
  ```properties
    texturepack-required=false
  # 强制玩家使用和服务器相同的资源包 (true/false)
  ```