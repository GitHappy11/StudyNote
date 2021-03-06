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

##### 11.NHibermate链接数据库功能集成到服务端

想要读取数据库内容，可以使用SQL语句。这里使用NHibernate包读取数据库内容

首先在Visual Studio上安装NHibernate插件

右键引用，点击管理NuGet程序包，搜索NHibernate，点击安装。

NHibernate依赖于数据库，所以我们还要安装MySQLData包，方法与上面一致。

安装完两个包后，我们就可以开始编写NHibernate的配置文件。

文件名必须为**hibernate.cfg.xml** 没有H

```xml
<?xml version="1.0" encoding="utf-8" ?>
<hibernate-configuration xmlns="urn:nhibernate-configuration-2.2">
  <session-factory>
    <property name="connection.provider">NHibernate.Connection.DriverConnectionProvider</property>
    <property name="dialect">NHibernate.Dialect.MySQL5Dialect</property>
      <!--使用什么数据库-->
    <property name="connection.driver_class">NHibernate.Driver.MySqlDataDriver</property>
      <!--数据库地址,名称以及账号密码-->
    <property name="connection.connection_string">Server=localhost;Database=nhtest;User ID=root;Password=;</property>
    <property name="show_sql">true</property>
  </session-factory>
</hibernate-configuration>
```

​	修改完毕之后，就可以建立脚本让数据库里的数据和类进行对应。一个类对应数据库里的一个表，建议建立一个文件夹【Model】来单独存储这些映射表。模板文件名**User**

```c#
namespace OAServer.Model
{
    public class User
    {
        public virtual int IDUser { get; set; }
        public virtual string UserName { get; set; }
        public virtual string Accout { get; set; }
        public virtual string Password { get; set; }
        public virtual int Role { get; set; }
        public virtual int OnlineState { get; set; }
    }
}
```

​	然后再创建XML文件来进行编写映射文件，一个XML文件对应一个类，如上，也建议建一个文件夹【Mapping】来单独存储。模板文件名**User.hbm.xml**

关于映射类型表格 https://nhibernate.info/previous-doc/v5.2/ref/mapping.html#mapping-types 

​	**此XML为嵌入的资源**

```xml
<?xml version="1.0" encoding="utf-8" ?>
<!--NH版本and程序集名and命名空间-->
<hibernate-mapping xmlns="urn:nhibernate-mapping-2.2"
                   assembly="OAServer"
                   namespace="OAServer.Model">
  <!--类名 表名  进行映射-->
  <class name="User" table="user">
    <!--    类里面   表里面   类型  主键-->
    <id name="IDUser" column="iduser" type="Int32">
      <!--用本地自带的生成器自动增长-->
      <generator class="native"></generator>
    </id>
    <!--非主键的写法-->
    <property name="UserName" column="username" type="String"></property>
    <property name="Account" column="account" type="String"></property>
    <property name="Password" column="password" type="String"></property>
    <property name="Role" column="role" type="Int32"></property>
    <property name="OnlineState" column="onlinestate" type="Int32"></property>



  </class>

</hibernate-mapping>
```

​	以上工作准备完毕后，就可以尝试NHibernate功能是否可以正常使用了，首先新建脚本【NhibernateHelper】来初始化NHibernate。

```c#

using NHibernate;
using NHibernate.Cfg;

namespace OAServer
{
    class NHibernateHelper
    {
        private static ISessionFactory sessionFactory;
        private static ISessionFactory SessionFactory
        {
            get
            {
                if (sessionFactory==null)
                {
                    var configuration = new Configuration();
                    //解析数据库配置
                    configuration.Configure();
                    //解析程序集配置
                    configuration.AddAssembly("OAServer");
                    //建立工厂
                    sessionFactory = configuration.BuildSessionFactory();
                }
                return sessionFactory;
            }
            
        }

        //初始化Nhiberna方法
        public static ISession OpenSessionByNhibernate()
        {
            return SessionFactory.OpenSession();
        }
    }
}
```

​	然后创建Nhibernate功能接口，一个表对应一个功能接口。模板还是user表，

```c#
using OAServer.Model;
using System.Collections.Generic;

namespace OAServer.Manager
{
    interface IUserManager
    {
        void Add(User user);
        void Update(User user);
        void Delete(User user);
        //重载 多种方式获取用户
        User GetUser(int id);
        User GetUser(string userName);
        //得到所有的用户信息
        //ICollection是一种集合的接口,扩展了功能。
        ICollection<User> GerAllUsers(); 
        
    }
}
```

​	最后实现这些接口

```c#

using System.Collections.Generic;
using OAServer.Model;
using NHibernate;
using NHibernate.Criterion;

namespace OAServer.Manager
{
    class UserManager : IUserManager
    {
        public void Add(User user)
        {
            ////创建工厂
            //ISession session = NHibernateHelper.OpenSessionByNhibernate();
            ////创建事务
            //ITransaction transaction = session.BeginTransaction();
            ////保存事务
            //session.Save(user);
            ////提交事务
            //transaction.Commit();
            ////关闭事务
            //transaction.Dispose();
            ////关闭工厂
            //session.Close();


            //使用Using可以省下关闭工厂步骤，它会在把工厂内的方法执行完毕后自动关闭工厂，事务同理
            using (ISession session = NHibernateHelper.OpenSessionByNhibernate())
            {
                using (ITransaction transaction = session.BeginTransaction())
                {

                    session.Save(user);
                    transaction.Commit();
                }
            }
        }

        public void Delete(User user)
        {
            using (ISession session = NHibernateHelper.OpenSessionByNhibernate())
            {
                using (ITransaction transaction = session.BeginTransaction())
                {
                    //根据主键进行更新删除操作，也可以使用Get方法利用主键进行查找后删除
                    session.Delete(user);
                    transaction.Commit();
                }
            }
        }

        public ICollection<User> GerAllUsers()
        {
            using (ISession session = NHibernateHelper.OpenSessionByNhibernate())
            {
                //获取整个表的集合
                IList<User> users = session.CreateCriteria(typeof(User)).List<User>();
                return users;
                
            }
        }

        public User GetUser(int id)
        {
            using (ISession session = NHibernateHelper.OpenSessionByNhibernate())
            {
                //根据表内主键进行查询,查询的时候不需要开启事务，因为查询不涉及数据的更改
                User user = session.Get<User>(id);
                return user;
            }
        }


        public User GetUser(string userName)
        {
            using (ISession session = NHibernateHelper.OpenSessionByNhibernate())
            {
                //让NH知道我们查的是哪个表
               ICriteria criteria= session.CreateCriteria(typeof(User));
                //添加查询条件  参数 字段名，查询内容
                criteria.Add(Restrictions.Eq("UserName" ,userName));
                //返回查询到的对象
                User user = criteria.UniqueResult<User>();

                ////一条语句结束
                //User user = session.CreateCriteria(typeof(User)).Add(Restrictions.Eq("UserName"), userName);
                return user; 
            }
        }

        public void Update(User user)
        {
            using (ISession session = NHibernateHelper.OpenSessionByNhibernate())
            {
                using (ITransaction transaction = session.BeginTransaction())
                {
                    session.Update(user);
                    transaction.Commit();
                }
            }
        }

        public bool VerifyUser(string account, string password)
        {
            using (ISession session=NHibernateHelper.OpenSessionByNhibernate())
            {
                //添加多个查询条件
                User user = session.CreateCriteria(typeof(User))
                    .Add(Restrictions.Eq("Account", account))
                    .Add(Restrictions.Eq("Password", password))
                    .UniqueResult<User>();
                if (user == null)
                {
                    return false;
                }
                else
                    return true;
            }
        }
    }
}
```

​	有了以上方法后，就可以调用这些方法，进行数据库的操作了，就无须写Sql语句。

##### 12.服务端与客户端进行带有数据的通信

​	先创建一个新的类库，作为服务端与客户端的一个共识，让两方都明白要传递的信息是干什么用的。

​	创建完毕后，设置生成一份到Unity中。然后在服务端引用一下该项目，这样就建立起了共识关系。

​	当然还有Event事件【服务端直接通知客户端，无需客户端先行申请】

```c#
using System;
namespace Common
{
    public enum OperationCode:byte
    {
        Login,
    }
}
namespace Common
{
    public enum EventCode:byte
    {
        Default,
    }
}

```

​	然后先在Unity中创建Request抽象类，以后发起请求的类都要继承于这个抽象类，便于统一管理请求

```c#
using Common;
using ExitGames.Client.Photon;
public abstract class Request
{
    //标记此事件的操作类型（是干什么的）
    public OperationCode opCode;
    //创建该事件的时候所做的操作（可带参数）
    public abstract void DefaultRequest();
    //处理服务端回应的方法（可带参数）
    public abstract void OnOperationResponse(OperationResponse operationResponse);
}
```

​	然后在NetSvc中建立好处理服务端的回应的数据的方法整理

```c#
using ExitGames.Client.Photon;
using System.Collections.Generic;
using UnityEngine;
using Common;


public class NetSvc : SystemRoot,IPhotonPeerListener 
{
    public static NetSvc Instance;

    //无法在Editor界面编辑
    [HideInInspector]
    public StatusCode statusCode;
    //保存所有的Request事件
    public Dictionary<OperationCode, Request> RequestDict = new Dictionary<OperationCode, Request>();
    //保存所有的Event事件
    public Dictionary<EventCode, BaseEvent> EventDict = new Dictionary<EventCode, BaseEvent>();
    private bool isReClient = false;
    private PhotonPeer peer;
    

    #region 客户端连接服务端的方法
    private void ServerSetup()
    {
        //服务器初始化
        //通过Listender接收服务器端的响应(监听服务器的类就是this)  链接方式（Udp）
        peer = new PhotonPeer(this, ConnectionProtocol.Udp);
        //设置链接的服务器IP和端口  要连接的服务器名
        peer.Connect("127.0.0.1:5055", "OA");

    }
    #endregion
    #region 监听服务器方法
    public void OnEvent(EventData eventData)
    {
        EventCode code = (EventCode)eventData.Code;
        BaseEvent e = null;

        if (EventDict.TryGetValue(code, out e))
        {
            e.OnEvent(eventData);
        }
        else
        {
           

        }
    }
    public void OnOperationResponse(OperationResponse operationResponse)
    {
        OperationCode opCode = (OperationCode)operationResponse.OperationCode;
        Request request = null;
        bool temp = RequestDict.TryGetValue(opCode, out request);
        if (temp)
        {
            request.OnOperationResponse(operationResponse);
        }
        else
        {
              //这是网络消息回复的时候，没有客户端没有找到相应的处理方法，因为客户端每次只能生成一个处理方法，多个同样的网络消息涌进，就会导致找不到处理方法，一般要等到网络消息过来处理完毕后，客户端再发送一个网络消息给服务端。解决方式：来一个缓冲条，等待网络消息接收完毕后才能发送下一个网络消息。所谓的网络慢转圈圈就是这种情况了。
        }

    }
    //服务器状态改变的时候执行的方法
    public void OnStatusChanged(StatusCode statusCode)
    {
        this.statusCode = statusCode;
        switch (statusCode)
        {
            case StatusCode.Connect:
                OARoot.Instance.AddDynTips("服务器链接成功！", "服务器连接状态");
                isReClient = false;
                break;
            case StatusCode.Disconnect:
                if (isReClient==false)
                {
                    OARoot.Instance.AddDynTips("当前网络状态不佳，无法连接服务器，正在重新链接！进入离线模式！", "服务器连接状态");
                }
                isReClient = true;
                break;
            case StatusCode.Exception:
                break;
            case StatusCode.ExceptionOnConnect:
                break;
            case StatusCode.SecurityExceptionOnConnect:
                break;
            case StatusCode.QueueOutgoingReliableWarning:
                break;
            case StatusCode.QueueOutgoingUnreliableWarning:
                break;
            case StatusCode.SendError:
                break;
            case StatusCode.QueueOutgoingAcksWarning:
                break;
            case StatusCode.QueueIncomingReliableWarning:
                break;
            case StatusCode.QueueIncomingUnreliableWarning:
                break;
            case StatusCode.QueueSentWarning:
                break;
            case StatusCode.ExceptionOnReceive:
                break;
            case StatusCode.TimeoutDisconnect:
                break;
            case StatusCode.DisconnectByServer:
                break;
            case StatusCode.DisconnectByServerUserLimit:
                break;
            case StatusCode.DisconnectByServerLogic:
                break;
            case StatusCode.EncryptionEstablished:
                break;
            case StatusCode.EncryptionFailedToEstablish:
                break;
            default:
                break;
        }
        if (isReClient)
        {
            ServerSetup();
        }

    }
    public void DebugReturn(DebugLevel level, string message)
    {
        Debug.LogWarning("ServerInfo:" + "--Level:" + level + "--Message:" + message);
    }
    #endregion
    #region 服务器在Unity中运行的机制
    private void Start()
    {
        ServerSetup();
    }
    private void Update()
    {

        peer.Service(); 
    }
    #endregion
    #region 客户端处理事件响应的方法
    public void AddRequest(Request request)
    {
        //将事件加入事件字典，等待处理
        RequestDict.Add(request.OpCode, request);
    }

    public void RemoveRequest(Request request)
    {
        //处理完的事件移除事件字典
        RequestDict.Remove(request.OpCode);
    }
#endregion

    //处理向服务端发出的请求
    public void SendRequset(OperationCode opCode,Dictionary<byte,object> data,Request request)
    {
        if (statusCode==StatusCode.Connect)
        {
            peer.OpCustom((byte)opCode, data, true);
            AddRequest(request);
        }
    	//如果不是在连接状态就说明客户端是离线模式
        else
        {
            switch (opCode)
            {
                case OperationCode.Default:
                    break;
                case OperationCode.Login:
                    OARoot.Instance.AddTips("当前处于离线模式！无法登录，进入观赏UI模式，所有数据均为缓存数据，不具有时效性。");
                    LoginSys.Instance.RspLogin();
                    break;
                default:
                    break;
            }

        }
        
    }
    //服务端向客户端的回应处理后的方法
    public void DeleteRequest(Request request)
    {
        RemoveRequest(request);
    }

    //网络服务初始化
    public override void InitSys()
    {
        Instance = this;
    }
}
```

 最后就可以new一个方法来发送事件给服务器啦！以下是实例：

```c#
public class NetLogin:Request
{
    private readonly string _account;
    private readonly string _password;
    //初始化
    public NetLogin(string account,string password)
    {
        OpCode = OperationCode.Login;
        _account = account;
        _password = password;
        DefaultRequest();
    }
    //发送带参数事件给服务端
    public override void DefaultRequest()
    {
        Dictionary<byte, object> data = new Dictionary<byte, object>();
        data.Add((byte)UserCode.Account, _account);
        data.Add((byte)UserCode.password, _password);
        NetSvc.Instance.SendRequset(OpCode, data, this);
    }
    //服务端回应
    public override void OnOperationResponse(OperationResponse operationResponse)
    {
        switch ((LoginCode)operationResponse.ReturnCode)
        {
            case LoginCode.Success:
                OARoot.Instance.AddTips("登录成功！");
                LoginSys.Instance.RspLogin();
                break;
            case LoginCode.AccountNothing:
                OARoot.Instance.AddTips("账号不存在！");
                break;
            case LoginCode.PasswordError:
                OARoot.Instance.AddTips("账号和密码不匹配！");
                break;
            case LoginCode.Online:
                OARoot.Instance.AddTips("该账号已经在线！");
                break;
            default:
                break;
        }
        NetSvc.Instance.DeleteRequest(this);
    }
}
```

 	这时候客户端已经发送数据到服务端了，服务端需要处理这些客户端数据。

​	首先，创建一个Handler【处理客户端发来的数据】抽象类，来写一些Handler的公有属性

```c#
using Common;
using Photon.SocketServer;

namespace OAServer.Handler
{
    public abstract class BaseHandler
    {
        //来表明是处理客户端的哪一个请求
        public OperationCode opCode = OperationCode.Default;
        //处理客户端发来的数据   三个参数分别为，客户端发来的数据包，服务端的一些数据包设置，在服务端上该客户端的数据
        public abstract void OnOperationRequest(OperationRequest operationRequest, SendParameters sendParameters, ClientPeer clientPeer);
      
    }
}
```

一个示例Handler

```c#

```

​	有了Handler之后，我们要在服务端初始化的时候也要把如何处理Handler的方法初始化，该方法要在**服务端初始化**的时候执行。

​	首先一个字典，来专门存储处理Handler，到时候客户端来数据，直接从字典里寻找相应的Code，执行相应的Handler。

```c#
//存储Handler
public Dictionary<OperationCode, BaseHandler> handlerDict = new Dictionary<OperationCode, BaseHandler>();
//初始化Handler
private void InitHandler()
{
            LoginHandler loginHandler = new LoginHandler();
}
```

​	接下来先做一个小工具，来获取到字典里的值。写在Common下，可以在客户端也调用这个方法。

```c#

using System.Collections.Generic;

namespace Common.Tools
{
    public class DictTool
    {
        //得到字典内的值
        public static T2 GetValue<T1,T2>(Dictionary<T1,T2> dict,T1 key)
        {
            T2 value;
            bool isSuccess = dict.TryGetValue(key, out value);
            if (isSuccess)
            {
                return value;
            }
            else
            {
                return default(T2);
            }

        }

    }
}

```

  	然后就可以到Peer类里按HandlerCode来处理Handler了

```c#
 protected override void OnOperationRequest(OperationRequest operationRequest, SendParameters sendParameters)
        {
            BaseHandler handler = DictTool.GetValue<OperationCode, BaseHandler>(ServerRoot.Instance.handlerDict, (OperationCode)operationRequest.OperationCode);
            if (handler!=null)
            {
                handler.OnOperationRequest(operationRequest, sendParameters, this);
            }
            else
            {
                //这里可以写一个默认的Handler处理
                BaseHandler deafultHandler = DictTool.GetValue<OperationCode, BaseHandler>(ServerRoot.Instance.handlerDict, OperationCode.Default);
                deafultHandler.OnOperationRequest(operationRequest, sendParameters, this);
            }
            
        }
```

