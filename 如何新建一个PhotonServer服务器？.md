# 如何新建一个PhotonServer服务器？

#### 客户端：Unity3D

##### 示例项目：OA系统

##### 1.打开PhotonServer配置文件

##### 2.设置配置文件

##### 3.VS新建一个对应的服务器类库

生成的位置记得要改为PhotonServer/Delay/服务器文件夹名/bin

##### 4.在PhotonServer文件夹内新建一个服务器文件夹部署dll文件

##### 5.类库引用（都在PhotonServer/Lib文件夹里都有）：

PhotonHostRuntimeInterfaces  

Photon.SocketServer 

Log4net

ExitGameLibs Log4net 

ExitGames.Logging.Log4Net

##### 6.主类引入命名空间 Photon.SocketServer

【这个类是服务端主类】

让主类继承于ApplicationBase，实现抽象类

继承的三个方法分别是：

CreatePeer () //当一个客户端请求链接的时候

Setup()//服务端初始化的时候

TearDown()//当服务器关闭的时候

```c#
using System;
using Photon.SocketServer;

namespace OAServer
{
    //所有Sever端  主类都要集成ApplicationBase
    public class ServerRoot : ApplicationBase
    {

        //当一个客户端请求链接的时候
        protected override PeerBase CreatePeer(InitRequest initRequest)
        {
            throw new NotImplementedException();
        }


        //服务器初始化
        protected override void Setup()
        {
            throw new NotImplementedException();
        }


        //当服务器关闭的时候
        protected override void TearDown()
        {
            throw new NotImplementedException();
        }
    }
}

```

##### 7.新建一个Peer类

【这个类是客户端的一个在服务端对应的一个类（每当有一个客户端链接过来就会创建一个peer对象，用于操作，可以保存一些客户端的信息】

引入命名空间 Photon.SocketServer 

让主类继承于ClientPeer

继承的两个方法分别是：

OnDisconnect（）//客户端断开操作

OnOperationRequest()//客户端链接操作

```c#
using System;
using Photon.SocketServer;
using PhotonHostRuntimeInterfaces;

namespace OAServer
{
    public class Peer : ClientPeer

    {
        //客户端断开的操作（一般做一些清理操作）
        protected override void OnDisconnect(DisconnectReason reasonCode, string reasonDetail)
        {
            throw new NotImplementedException();
        }

        //客户端链接上的操作
        protected override void OnOperationRequest(OperationRequest operationRequest, SendParameters sendParameters)
        {
            throw new NotImplementedException();
        }
    }
}

```

##### 8.Peer类中添加构造函数，使得Peer类能够做一些初始化工作

```c#
public Peer(InitRequest initRequest):base(initRequest)
```

然后在服务器主类中修改方法（当客户端链接的时候，就new一个Peer类）

```c#
//当一个客户端请求链接的时候
        protected override PeerBase CreatePeer(InitRequest initRequest)
        {
            return new Peer(initRequest);
        }
```

到了这步应该就可以测试启动服务器了（记得生成一下）

##### 9.配置Log4net文件

去其他项目扒一个过来

然后修改配置文件 关键的就一行 表示输出的目录和文件名

```xml
 <file type="log4net.Util.PatternString" value="%property{Photon:ApplicationLogPath}\\OAServer.Server.log" />
```

然后文件属性配置：**复制到输出目录：始终复制**

在服务器初始化的时候初始化日志文件和设置日志文件的输出目录

```c#
//配置日志输出
private static readonly ILogger log = LogManager.GetCurrentClassLogger();
 //服务器初始化
        protected override void Setup()
        {
            //日志初始化
            //设置日志文件输出目录
            log4net.GlobalContext.Properties["Photon:ApplicationLogPath"] = Path.Combine(this.ApplicationRootPath, "OAServer/log");
            //获取配置文件
            FileInfo configFileInfo = new FileInfo(Path.Combine(this.BinaryPath, "log4net.config"));
            if (configFileInfo.Exists)
            {
                //建立日志工厂  使用的是log4net日志插件
                LogManager.SetLoggerFactory(Log4NetLoggerFactory.Instance);
                //让log4net读取配置文件
                XmlConfigurator.Configure(configFileInfo);
            }

            log.Info("服务器初始化完毕！");

        }
```

设置完毕后重启服务器，就可以查看日志文件输出是否成功了

##### 10.Unity客户端文件配置

先去PhotonServer/Lib文件夹找Photon3Unity3D.dll 

拖入Unity的Plugins文件夹内

使用单例模式配置PhotonServer服务

```C#
/****************************************************
    文件：NetSvc.cs
	作者：Happy-11
    日期：2020年12月12日23:37:10
	功能：网络服务
*****************************************************/

using ExitGames.Client.Photon;
using UnityEngine;


public class NetSvc : SystemRoot,IPhotonPeerListener 
{
    private static NetSvc Instance;

    private PhotonPeer peer;

    public override void InitSys()
    {
        Instance = this;
    }

    

    private void ServerSetup()
    {
        //服务器初始化
        //通过Listender接收服务器端的响应(监听服务器的类就是this)  链接方式（Udp）
        peer = new PhotonPeer(this, ConnectionProtocol.Udp);
        //设置链接的服务器IP和端口  要连接的服务器名
        peer.Connect("127.0.0.1:5055", "OA");

    }
    #region 监听服务器方法
    public void OnEvent(EventData eventData)
    {
       
    }

    public void OnOperationResponse(OperationResponse operationResponse)
    {
        
    }
    //服务器状态改变的时候执行的方法
    public void OnStatusChanged(StatusCode statusCode)
    {
        Debug.Log(statusCode);
    }
    public void DebugReturn(DebugLevel level, string message)
    {
        Debug.Log("ServerInfo:" + "--Level:" + level + "--Message:" + message);
    }
    #endregion
    private void Start()
    {
        ServerSetup();
    }
    private void Update()
    {
        peer.Service();
    }

}
```

配置完毕后启动客户端，可以测试查看是否已经链接成功了。