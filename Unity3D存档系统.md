# Unity3D存档系统

## 			实例项目：HalloweenShooting

```c#
//没有游戏存档路径的话，就创建一个存档路径
if (!Directory.Exists(savePath))
{
   Directory.CreateDirectory(savePath);
}
```

```c#
//DefaultCompany 就是生成项目的默认公司名，后面的就是你的项目名
Application.persistentDataPath=C:/Users/Eleven/AppData/LocalLow/DefaultCompany/InventroyTutorial
//这个路径就是Assets的根目录
Application.dataPath
```

### 	1.PlayerPrefs

​	这是一个本地持久化保存与读取的类

​	工作原理非常简单，以键值对的形式将数据保存在文件中，然后程序可以根据这个名称取出上次保存的数值。

```c#
SetFloat()，SetInt()，SetString() //写入数据 一共可以写入三种数据
GetFloat()，GetInt()，GetString() //与上面类似
DeleteKey()，DeleteAll() //删除数据
HasKey(“SS”) //检查数据,是否有该键
Save() //保存数据 一般情况无需保存
```

​	适合用于保存本地设置数据。**实例演示在GameManager脚本中，保存背景音乐是否开启的设置。**

​	后面的实例都在Save脚本中，**该脚本可直接再其他项目中使用**【需要Json的依赖库】

### 2.二进制方式

​	优点：简单粗暴

​	缺点：存档文件基本没有可读性

**存储：**

（1）直接存储

```c#
	    //获得一个需要保存数据的类 我这里就把LocalData类里的数据保存下来
        //注意：这个类是要标记可序列化的[Serializable],获得这个SaveData类的方式最好是用一个方法初始化一下【其他保存方式也类似】
        SaveData saveData = new SaveData();
        saveData.shootNum = LocalData.shootNum;
        saveData.score = LocalData.score;
        //创建一个二进制格式化程序
        BinaryFormatter bf = new BinaryFormatter();
        //创建一个文件流 后面文件保存的格式什么都可以，可以创建自己的专属格式【这里使用txt可以更直接看到里面写了什么】
        FileStream fileStream = File.Create(savePathByBin);
        //用二进制格式化程序的序列化方法来序列化Save对象
        //参数：创建的文件流，和序列化的对象
        bf.Serialize(fileStream, saveData);
        //关闭流  这里可以使用Using方式，可能会更简单
        fileStream.Close();
        //如果文件存在，说明保存成功【后续保存可能需要对比文件差异】
        if (File.Exists(savePathByBin))
```

（2）JsonUnility

```c#
	    //二进制转换
        BinaryFormatter binaryFormatter = new BinaryFormatter();
        //创建存档文件【后缀随意，本质是txt】
        FileStream file = File.Create(savePath+"/Save.save");
        //将需要保存的类数据保存为json文件
        var json = JsonUtility.ToJson(bagData);
        //转化为二进制格式,写入文件夹中
        binaryFormatter.Serialize(file, json);
        //可以使用Using简化
        file.Close();
```

**读取：**

(1)直接存储

```c#
	    //反序列化过程
        //创建一个二进制格式化程序
        BinaryFormatter bf = new BinaryFormatter();
        //先看文件是否存在
        if (File.Exists(savePathByBin))
        {
            //打开一个文件流 参数：文件路径，文件操作模式【打开】
            FileStream fileStream = File.Open(savePathByBin, FileMode.Open);
            //调用格式化程序的反序列化方法，讲文件流转化为一个Save对象
            SaveData saveData = (SaveData)bf.Deserialize(fileStream);
            //关闭文件夹流【一样的，可以使用using】
            fileStream.Close();
            //后面就是把取出来的这个类的数据附加到游戏内了
            LocalData.score = saveData.score;
            LocalData.shootNum = saveData.shootNum;
        }	
```

(2)JsonUnility

```c#
		   //二进制转换
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            //打开文件
            FileStream file = File.Open(savePath + "/Save.save", FileMode.Open);
            //使用存档文件重写序列化的ObjectScript类
            JsonUtility.FromJsonOverwrite((string)binaryFormatter.Deserialize(file), bagData);
            //可以使用Using简化
            file.Close();
```

3.Json方式

​	优点：存档文件有可读性较好，较为简单

​	缺点：需要一个依赖库【LitJson.dll】，可以使用Unity自带的【JsonUnility.ToJson(要保存的类)】

**存储：**

（1）LitJson.dll

```c#
	    //获得一个需要保存数据的类 我这里就把LocalData类里的数据保存下来
        //注意：这个类是要标记可序列化的[Serializable],获得这个SaveData类的方式最好是用一个方法初始化一下【其他保存方式也类似】
        SaveData saveData = new SaveData();
        saveData.shootNum = LocalData.shootNum;
        saveData.score = LocalData.score;
        //利用JsonManager将Save对象转化为Json格式的字符串
        string saveJsonStr = JsonMapper.ToJson(saveData);
        //创建一个StreamWrite，并将字符串写入文件中
        StreamWriter sw = new StreamWriter(savePathByJson);
        sw.Write(saveJsonStr);
        //关闭StreamWrite【同理，可以使用Using
        sw.Close();
        //如果文件存在，说明保存成功【后续保存可能需要对比文件差异】
        if (File.Exists(savePathByJson))
```



*读取：**

```c#
        //先看文件是否存在
        if (File.Exists(savePathByJson))
        {
            //创建一个StreamReader，用来读取流
            StreamReader sr = new StreamReader(savePathByJson);
            //将读取到的流赋值给jsonStr
            string jsonStr = sr.ReadToEnd();
            //关闭读取流【可以使用Using】
            sr.Close();
            //将字符串JsonStr转换为Save对象
            SaveData saveData = JsonMapper.ToObject<SaveData>(jsonStr);
            //后面就是把数据读取出来了
            if (saveData!=null)
            {
                LocalData.score =saveData.score ;
                LocalData.shootNum = saveData.shootNum;
                Debug.Log("加载成功！");
            }
        }

```

### 4.XML方式

​	优点：存档文件可读性最好，存档格式最为完整，一个文件可以容纳多个存档

​	缺点：太过存档过程繁琐

**存储：**

```c#
 	    //创建XML文件的存储路径
        XmlDocument xmlDoc = new XmlDocument();
        //创建根节点，即最上层节点
        XmlElement root = xmlDoc.CreateElement("save");
        //设置根节点中的值
        root.SetAttribute("name", "saveFile1");
        //遍历save中存储的数据，将数据转换成XML格式 列表专用存储方式，此次实例没有，可做参考
        #region 列表数据存储
        //创建XmlElement
        //XmlElement target;
        //XmlElement targetPosition;
        //XmlElement monsterType;
        //for (int i = 0; i < save.livingTargetPositions.Count; i++)
        //{
        //    target = xmlDoc.CreateElement("target");
        //    targetPosition = xmlDoc.CreateElement("targetPosition");
        //    //设置InnerText值
        //    targetPosition.InnerText = save.livingTargetPositions[i].ToString();
        //    monsterType = xmlDoc.CreateElement("monsterType");
        //    monsterType.InnerText = save.livingMonsterTypes[i].ToString();

        //    //设置节点间的层级关系 root -- target -- (targetPosition, monsterType)
        //    target.AppendChild(targetPosition);
        //    target.AppendChild(monsterType);
        //    root.AppendChild(target);
        //}
        #endregion
        //设置射击数和分数节点并设置层级关系  xmlDoc -- root --(target-- (targetPosition, monsterType), shootNum, score)
        XmlElement shootNum = xmlDoc.CreateElement("shootNum");
        shootNum.InnerText = LocalData.shootNum.ToString();
        root.AppendChild(shootNum);
        XmlElement score = xmlDoc.CreateElement("score");
        score.InnerText = LocalData.score.ToString();
        root.AppendChild(score);
        xmlDoc.AppendChild(root);
        xmlDoc.Save(savePathByXML);
		//检查数据文件
        if (File.Exists(savePathByXML))
```

**读取：**

```c#
if (File.Exists(savePathByXML)//检查文件是否存在
            SaveData save = new SaveData();
            //加载XML文档
            XmlDocument xmlDoc = new XmlDocument();
            xmlDoc.Load(savePathByXML);
            #region 列表的读取方式
            ////通过节点名称来获取元素，结果为XmlNodeList类型
            //XmlNodeList targets = xmlDoc.GetElementsByTagName("target");
            ////遍历所有的target节点，并获得子节点和子节点的InnerText
            //if (targets.Count != 0)
            //{
            //    foreach (XmlNode target in targets)
            //    {
            //        XmlNode targetPosition = target.ChildNodes[0];
            //        int targetPositionIndex = int.Parse(targetPosition.InnerText);
            //        //把得到的值存储到save中
            //        save.livingTargetPositions.Add(targetPositionIndex);

            //        XmlNode monsterType = target.ChildNodes[1];
            //        int monsterTypeIndex = int.Parse(monsterType.InnerText);
            //        save.livingMonsterTypes.Add(monsterTypeIndex);
            //    }
            //}
            #endregion
            //得到存储的射击数和分数
            XmlNodeList shootNum = xmlDoc.GetElementsByTagName("shootNum");
            int shootNumCount = int.Parse(shootNum[0].InnerText);
            save.shootNum = shootNumCount;
            XmlNodeList score = xmlDoc.GetElementsByTagName("score");
            int scoreCount = int.Parse(score[0].InnerText);
            save.score = scoreCount;
            //数据读取
            LocalData.score = save.score;
            LocalData.shootNum = save.shootNum;
```

### 5.ScriptableObject

​	ScriptableObject里的信息不会随着游戏的关闭而消失，仅限项目未生成状态，生成项目后，就不会保存了。

就需要上面的方法保存.

​	Scriptable object在游戏打包发布后不可在游戏进行中修改，可以被json覆盖，但是存取的方法不会像Unity Editor里那么简单直接。它的用处可以看这篇博客https://blog.csdn.net/candycat1992/article/details/52181814
以下是官方文档里的话
In a deployed build, however, you can’t use ScriptableObjects to save data, but you can use the saved data from the ScriptableObject Assets that you set up during development.

笔者的理解是：ScriptableObjects这个类型并不推荐在正式的(交付后)运行中保存实时数据，我们可以更多地用它在开发过程中实时地保存调试数据。