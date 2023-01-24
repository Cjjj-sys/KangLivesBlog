---
title: 记录使用 Lib.Harmony 时注入过程中遇到的一个坑
date: "2022-06-28T22:40:32.169Z"
description: 我一开始猜想这可能类似于 **CE(Cheat Engine)** 那样，从内存的层面上去修改一些东西(感觉很复杂，没有研究过)。直到我偷偷地把它拖进了 dnSpy(一个反编译软件)，短暂地分析代码后，得出的结果让我大吃一惊——
---
## 1. 从这里开始

  在一个无聊的下午，我偶然看到某个 Terraria 群里在谈论着一个叫 **TerrariaHelper(自动钓鱼)** 的软件，顿时有了兴致。我之前粗略研究过 **FruitY**(某个直接修改 Terraria 程序集代码而做出来的~~魔改端~~(作弊端)，功能丰富) 的实现代码，但是今天看到的是一个独立的 Helper 程序，没有修改 Terraria 的源文件，也包含了**自瞄，自动回血等等**功能，不禁让人好奇这些功能是如何实现的。

  我一开始猜想这可能类似于 **CE(Cheat Engine)** 那样，从内存的层面上去修改一些东西(感觉很复杂，没有研究过)。直到我偷偷地把它拖进了 dnSpy(一个反编译软件)，短暂地分析代码后，得出的结果让我大吃一惊——居然使用了 **Lib.Harmony**(我接触过 Lib.Harmony 但是我只知道它是用来制作 Unity 游戏的 Mod 的)，然后我才意识到 Lib.Harmony **功能强大**，不仅仅是做 Unity 游戏 Mod 的工具。

## 2. 阅读 Lib.Harmony 的文档

  因为 Lib.Harmony 没有多少中文资料(宵夜97制作过一系列[教程](https://space.bilibili.com/1306433))，我们还是从看它的[官网文档](https://harmony.pardeike.net/index.html)开始！

> # Harmony 2
>
> ## Introduction
>
> Harmony gives you an elegant(优雅的) and high level way to **alter functionality**(实用地更改) in applications written in C#. It does this at **runtime** by monkey patching methods unlike other solutions that change the content of dll files.
>
> It supports **Mono** and **.NET** environments on Windows, Unix and macOS except when Unity uses the stripped down NetStandard profile (.NET 4.x profile works fine). Harmony is used in mainstream Unity games and many **other applications**.(不仅仅用于 Unity 游戏，还有其他的应用程序)
>
> Designed to be used by multiple users (usually called Mods) that would otherwise override each others hooks, it was originally created for the game [RimWorld](https://rimworldgame.com/) and its large modding community by [Andreas Pardeike](https://www.patreon.com/pardeike).
>
> Enjoy!  
> /Andreas Pardeike
>
> ......
>
> # Introduction
>
> *Harmony - a library for patching, replacing and decorating .NET methods during runtime.*
>

  上面的介绍提到了 Lib.Harmony 是一个跨平台的适用于 Mono 和 .NET 运行时环境的在程序运行时对方法进行 修补(patch)，替换(replace) 和 装饰(decorate) 的类库。同时，尽管 Lib.Harmony 最初是为 RimWorld 而创造 且现在大多用于 Unity 游戏的 Mod 创作，但是它仍然适用于其他的符合要求的 .NET 应用程序。

> ### Bootstrapping and Injection
>
> Harmony does not provide you with a way to run your own code within an application that is not designed to execute foreign code. You need a way to inject at least the few lines that start the Harmony patching and this is usually done with a loader. Here are some common examples of loaders (incomplete):
>
> * [Unity Doorstop](https://github.com/NeighTools/UnityDoorstop)
> * [BepInEx](https://github.com/BepInEx/BepInEx)
> * [UnityAssemblyInjector](https://github.com/avail/UnityAssemblyInjector)
> * [MonoJunkie](https://github.com/wledfor2/MonoJunkie)
> * [MInjector](https://github.com/EquiFox/MInjector)
> * and more...
>
> You need to find your own injection method or choose a game that supports user dll loading (usually called Mods) like for example RimWorld ([Wiki](https://rimworldwiki.com/wiki/Modding_Tutorials/)).
>

  上面提到，Lib.Harmony 不是用被设计来在应用程序里面执行外来的代码，所以它没有给你在别的程序里面运行你的代码的方法。而且你需要把代码通过加载器(注入器)注入目标程序才能让它们开始工作。

  上面列举了几个加载器，但是很遗憾，他们都是针对 Mono 运行时的(就是 Unity 程序使用的运行时环境)。所以如果要在其他的应用程序里面使用 Lib.Harmony，需要寻找或者自己做一个注入器。( **注入这个步骤我遇到了第一个坑** )

> ### Adding using nuget
>
> To add Harmony manually to your Visual Studio project, you right-click on `References` in your solution explorer and choose `Manage NuGet Packages`, then search for "Harmony Library" and install it.
>
> ### Import
>
> Once you reference Harmony correctly, you should be able to import it by adding Harmony to your imports. That gives you code completion so you can discover the API:
>
> ```c#
> using HarmonyLib;
> ```
>
> ## Creating a Harmony instance
>
> Most patch operations require a Harmony instance. To instantiate Harmony, you simply call
>
> ```c#
> var harmony = new Harmony("com.company.project.product");
> ```
>
> The id should be in reverse domain notation and must be unique. In order to understand and react on existing patches of others, all patches in Harmony are bound to that id. This allows other authors to execute their patches before or after a specific patch by referring to this id.
>
> ### Patching using annotations
>
> If you prefer annotations to organize your patches, you instruct Harmony to search for them by using `PatchAll()`:
>
> ```c#
> var assembly = Assembly.GetExecutingAssembly();
> harmony.PatchAll(assembly);
>
> // or implying current assembly:
> harmony.PatchAll();
> ```
>
> which will search the given assembly for all classes that are decorated with Harmony annotations. All patches are registered automatically and Harmony will do the rest.
>

  通过上面的教程(或者宵夜97的[教程](https://space.bilibili.com/1306433))可以写出 Lib.Harmony 的"初始化"代码

```c#
using System;
using System.Windows.Froms; // 我们可以用 MessageBox 来看看有没有成功载入
using HarmonyLib;

namespace MyPatch {
    public static class EndPoint {
        public static void Start() {
            var harmony = new Harmony("my.patch");
            harmony.PatchAll();
            MessageBox.Show("成功注入!");
        }
    }
}
```

  似乎一切都没问题，现在寻找注入的方法。我还是参照了 TerrariaHelper 的方式，引用 FastWin32 包，调用里面的 InjectManaged 方法来进行注入。

```c#
bool Injector.InjectManaged(uint processId, string assemblyPath, string typeName, string methodName, string argument)
```

> #### 参数说明：
>
> * processId：目标进程的进程id ->pid
> * assemblyPath：核心Hook 注入的dll 绝对路径
> * typeName：Hook 初始化方法的命名空间，一般注入一个模块dll后需要执行的入口初始化方法，这里是Hook 核心dll 中的HookService.Start 方法的命名空间（Jlion.Process.HookCore.HookService）
> * methodName : 注入后执行的方法名称
> * argument : 方法所需要的参数
>

  当我生成好要注入的 dll 和简易的注入器后，打开了一个测试的程序进行注入，Injector.InjectManaged 方法返回了 true。意思是注入成功了，但是却没有弹出“成功注入”的消息框。我起初以为是注入器的问题(因为 FastWin32 这个库比较久远，是在 .NET Framework 上面开发的，甚至显示不兼容 .NET Framework 4.8，而我的简易注入器是创建的 .NET 6 的项目)，于是我又新建了一个 .NET Framework 的项目，但是问题没有解决。

  我又感觉可能是 FastWin32 太久远了，或许 Windows11 平台上面不兼容它。所以我在 GitHub 上面找了的一款现成的注入器 

[ManagedInjector](https://github.com/enkomio/ManagedInjector) (正是这个注入器帮我解决了问题)。

  当我用这个注入器选择我生成的 dll 时，列表里竟空无一物，无法选择我的 Start 方法进行注入。

  我这才意识到可能是我的 dll 出了问题。

  [经过了漫长的尝试]

  我发现了一个规律，只有当方法的返回值为 int 类型，且参数有且只有一个 string 类型变量时，才能成功被注入。

```c#
using System;
using System.Windows.Froms; // 我们可以用 MessageBox 来看看有没有成功载入
using HarmonyLib;

namespace MyPatch {
    public static class EndPoint {
        public static int Start(string str) { // void -> int, () -> (string str)
            var harmony = new Harmony("my.patch");
            harmony.PatchAll();
            MessageBox.Show("成功注入!");
            return 0;
        }
    }
}
```

将代码改成上面这样后，终于注入成功，弹出了"成功注入"的消息框！
