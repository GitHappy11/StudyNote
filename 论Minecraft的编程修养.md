# 论Minecraft的编程修养

基本知识掌握

1.MC中 20Tick=1秒

## 数据包

基本逻辑目录

![image-20210118211856387](D:\WorkData\StudyNote\image\image-20210118211856387.png)

#### Pack.mcmeta

```json
{
	"pack":
	{
        //版本编号
		"pack_format":6,
        //数据包简介
		"description":"My First Minecraft Datapacks"
	}
}
```

#### Load.json  Tick.json

**Load**:用于加载数据后第一时间调用的函数（类似Unity的Start方法）

**Tick：**加载完数据包后每一个Tick都会调用的函数（类似Unity的Update方法）

```json
{
	"values":
	[
		"minecraft:init" //前面是命名空间（和开始的命名空间对应，后面是对应的mcfunction文件，命令在文件里写）
	]
}
```

#### Init.mcfunction Tick.mcfunction

**Init:**对应Load

**Tick:**对应TIck

文件内部写MC指令，不要加/

```json
#会执行下面的命令（根据文件名来决定执行次数和顺序）
time set day
```



## 关于标签

标签文件放于【1.16数据包\data\minecraft\tags\items】路径下，文件名就是标签名

```json
{
    #这里表示添加标签还是覆盖标签，一般情况下都是添加
	"replace":"false",
	"values":
	[
    	#需要添加到这个tag的物品的命名空间
		"minecraft:oak_planks",
		"minecraft:birch_planks",
		"minecraft:spruce_planks",
		"minecraft:dark_iak_planks",
		"minecraft:jungle_planks",
		"minecraft:acacia_planks",
	]
}
```



### 关于自定义合成

**自定义合成优先级较高，会覆盖掉原版的合成。**

#### 1.有序合成

```json
{
    #Type 指的是什么类型的合成  这里是有序合成（必须按照合成表的顺序摆放）
	"type":"crafting_shaped",
    #摆放顺序（#号只是一个代替符号，可以用任何符号或字母代替，不同的符号代表不同的物品，空格代表无物品）
    #只用凑在一起的4格，就可以在背包中合成，不需要工作台
	"pattern":
	[
		"   ",
		"## ",
		"## "
	],
	#key就是指定这个物品是什么（命名空间指定）【相关命名空间可以查询Wiki或者在MC中启动高级模式查看】
	"key":
	{
		"#":
		{
			"item":"minecraft:phantom_membrane"
		}
	},
	#合成结果
	#itme:合成的物品
	#count:合成数量
	"result":
	{
		"item":"minecraft:elytra",
		"count":1
	}
}
```

#### 2.无序合成

```json
{
    #这里类型改为shapeless 意思就是无序合成
	"type":"crafting_shapeless",
    #这里面写材料（一个材料占走一个大括号）
	"ingredients":
	[
		{
			"item":"minecraft:iron_ingot"
			
		},
		
		{
			"item":"minecraft:black_dye"
		}
	],
	"result":
	{
		"item":"minecraft:netherite_ingot",
		"count":1
	}
}
```

#### 3.标签合成

标签合成必须是有序合成（但是如果只有一个物品）它会自动变为无序合成

```json
{
    #Type 指的是什么类型的合成  这里是有序合成（必须按照合成表的顺序摆放）
	"type":"crafting_shaped",
    #摆放顺序（#号只是一个代替符号，可以用任何符号或字母代替，不同的符号代表不同的物品，空格代表无物品）
    #只用凑在一起的4格，就可以在背包中合成，不需要工作台
	"pattern":
	[
		"   ",
		"## ",
		"## "
	],
	#key就是指定这个物品是什么（命名空间指定）【相关命名空间可以查询Wiki或者在MC中启动高级模式查看】
	"key":
	{
		"#":
		{
            #这里不是用item（改为了tag），表示只要是planks标签的都可以进行代替
			"tag":"minecraft:planks"
		}
	},
	#合成结果
	#itme:合成的物品
	#count:合成数量
	"result":
	{
		"item":"minecraft:elytra",
		"count":1
	}
}
```

#### 4.特殊工作台合成

熔炉：smelting

篝火：campfire_cooking

高炉：blasting

```json
{
    #使用什么工作台（这里是熔炉）
	"type":"minecraft:smelting",
	"ingredient":
	{
        #材料
		"item":"minecraft:iron_ingot"
	},
	#生成物
	"result":"minecraft:quartz",
	#获得经验值
	"experience":3,
	#烧制时间【注意：这里的数字指的是Tick 一般20Tick为1秒，这边转化为秒就是10秒】
	"cookingtime":200
	}
}
```

切石机：stonecutting

```json
{
    #使用什么工作台（这里是切石机）
	"type":"minecraft:stonecutting",
	"ingredient":
	{
        #材料
		"item":"minecraft:iron_ingot"
	},
	#生成物
	"result":"minecraft:quartz",
	#数量
	"count":4
	}
}
```

f

#### 常用命令

```apl
//重新加载数据包
/reload 
//查看数据包列表
/datapack list 

/scoreboard objectives add <对象名称> <主要参数>  "显示名称"
主要参数：totalKillCount(杀怪数)、playerKillCount(杀死其他玩家次数)
例子：
/scoreboard objectives add plyaerkill playerKillCount "杀人榜"



/scoreboard objectives setdisplay <显示位置> <对象名称>
sidebar(右侧)、list（列表,按Tab键显示）、belowName (玩家头顶名称下方)
例子：
/scoreboard objectives setdisplay sidebar plyaerkill 

死亡不掉落：/gamerule keepInventory true
设置世界重生点：/setworldspawn
```



#### 版本编号

![image-20210117141108145](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20210117141108145.png)

