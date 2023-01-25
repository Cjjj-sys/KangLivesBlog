---
title: BroMaker教程 [1]
date: 2023-01-25 04:26:58
tags: Broforce, C#, Mod
category: 教程
---
本教程会教你如何使用BroMaker创建一个自己的Bro

**首先是需要的知识**

* C# 基础知识
* Unity Mod Manager 的 Mod 的开发经验 (~~不是必需的~~)
* dnSpy 的基本操作 (不是必需的)

# 第一部分 : 我的第一个Bro

## 在开始之前:

确保你已经下载并安装了 [BroMaker](https://www.nexusmods.com/broforce/mods/13) 

确保你至少有一个 C# 的 IDE (例如 Visual Studio, SharpDevelop, Rider ...)

## 现在开始吧:

首先打开你的 IDE 并新建一个类库 (我这里用 SharpDevelop 做演示)

![image.png](assets/image-20220612200707-t8widea.png)

> **在这里注意**！**如果后续生成失败，请按照下列标黄的步骤操作**
>
> 需要选择 **.NET Framework 3.5** 框架，在**项目属性-生成中修改目标框架**
>
> ![image.png](assets/image-20220612203520-9jfyblm.png)
>
> 然后选择 C# 3.0 和 .NET Framework 3.5
>
> ![image.png](assets/image-20220612203954-y9rw9wq.png)
>
> 再点击转换
>

新建后将**类名**和**文件名**改为和**命名空间**相同的名字(方便管理)

![image.png](assets/image-20220612201104-f72igpu.png)

点击 **项目-添加引用**

![image.png](assets/image-20220612201218-9oy7xb8.png)

点击 **.NET集合浏览器-浏览**

![image.png](assets/image-20220612201303-nyr9sgq.png)

选择 **BroMaker.dll** (在你的 Broforce 的 **Mods\BroMaker** 目录里面)

![image.png](assets/image-20220612201541-2kykdev.png)

点击打开后，以同样的方式添加对下列程序集的引用

* **Assembly-CSharp.dll** (位于 **Broforce\Broforce_beta_Data\Managed 目录**内）
* **UnityEngine.CoreModule.dll** (位于 **Broforce\Broforce_beta_Data\Managed 目录**内)
* **UnityModManager.dll** (位于 **Broforce\Broforce_beta_Data\Managed\UnityModManager 目录**内)

完成后你的引用列表应如图所示

![image.png](assets/image-20220612210336-22hzqem.png)

然后在代码中添加**对 BroMakerLib** 和 **UnityModManagerNet** 的 using 语句

```csharp
using BroMakerLib;
using UnityModManagerNet;
```

然后像一般的Mod一样创建Main类

```csharp
static class Main
{
	static bool Load(UnityModManager.ModEntry modEntry)
        {
            return true;
        }

}
```

再**继承 BroBaseMaker 类**

```csharp
public class MyBro : BroBaseMaker
{

}
```

做到这一步你的代码应该长这样:

```csharp
using System;
using System.Collections.Generic;
using BroMakerLib;
using UnityModManagerNet;

namespace MyBro
{
	static class Main
	{
		static bool Load(UnityModManager.ModEntry modEntry)
	        {
	            return true;
	        }

	}

	public class MyBro : BroBaseMaker
	{

	}
}
```

尝试生成一下项目，如果没有成功请检查上述步骤是否正确完成(**特别是对程序集的引用**)。

![image.png](assets/image-20220612203242-fiuo5vn.png)

将下列代码粘贴进 **MyBro 类**

```csharp
public override void bm_SetupBro(Player player)
{
	/**
	this.bm_DefaultMaterial = BroMaker.CreateMaterialFromFile();
	this.bm_DefaultGunMaterial = BroMaker.CreateMaterialFromFile();
	this.bm_avatarMaterial = BroMaker.CreateMaterialFromFile();
	this.bm_ammoMaterial = BroMaker.CreateMaterialFromFile();
	**/
	//上述为Bro外形的自定义,下节讲解
	base.bm_SetupBro(player);
}

protected override void Awake()
{
	originalSpecialAmmo = 3; // 特殊弹药(技能)的最大数量. (最小为0,最大为6)
	fireRate = 0.10f; // 两次开火的间隔时间(毫秒)
	base.Awake();
}

protected override void Start()
{
	base.Start();
	Rambro rambro = HeroController.GetHeroPrefab(HeroType.Rambro) as Rambro;
	BrodellWalker brodellWalker = HeroController.GetHeroPrefab(HeroType.BrodellWalker) as BrodellWalker;

	Projectile projectileRambro = Projectile.Instantiate(rambro.projectile, rambro.projectile.transform);
	//上述为获取 Rambro 的 实例 和 BrodellWalker 的 实例 ，同时注意上一行在获取子弹时必须要写（会在下一节讲解）
			  
	projectile = projectileRambro; 
	specialGrenade = brodellWalker.specialGrenade;		  
	//定义你的Bro的子弹和特殊弹药，在这里是Rambro的子弹和BrodellWalker的手榴弹
}
```

现在我们已经定义了一些Bro的属性，接下来应该**向BroMaker注册我们的新Bro**

将下列代码粘贴进 **Main 类** 中的 **Load 方法**内

```csharp
new NewBroInfo(typeof(MyBro), "MyBro");
```

好了，接下来生成你的项目

在生成的目录下新建 Info.json 输入以下内容

```json
{
    "Id": "MyBro",
    "DisplayName": "Mybro",
    "Author": "Author",
    "Version": "0.0.1",
    "Requirements": ["BroMaker"],
    "LoadAfter": ["BroMaker"],
    "AssemblyName": "MyBro.dll",
    "EntryMethod": "MyBro.Main.Load"
}
```

然后将生成的 MyBro.dll 和 Info.json 一起打包为 MyBro.zip

再通过UnityModManager安装，然后进游戏查看效果吧


未完待续...


by:KangKang QQ:2646381627

