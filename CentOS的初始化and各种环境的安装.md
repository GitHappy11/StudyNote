# CentOS的初始化and各种环境的安装

## 一 、安装 Centos7

### 1.准备材料：

#### （1）VMware Workstation 16 Pro

##### （2）Centos7镜像文件：阿里云镜像站下载。

#### 阿里云镜像站：[https://developer.aliyun.com/mirror/](https://developer.aliyun.com/mirror/)

#### （3）配置安装

### **二、配置网络**

#### 1.先自动获取一个IP地址，然后查看IP。

![image-20220316111011369](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111011369.png)

#### 2.从WM虚拟网络编辑器中，查看IP配置

![image-20220316111042162](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111042162.png)

#### 3.打开网络配置文件

![image-20220316111049333](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111049333.png)

4. #### 修改配置文件，设置为静态IP。

![image-20220316111059640](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111059640.png)

修改完成后按**Esc**，输入**:wq**保存退出。

#### 5.这时候尝试ping [www.baidu.com](http://www.baidu.com/)就可以ping通了

### 三、安装GUI 界面（可省略）个人喜欢图形界面所以安装

#### 1．下载图形界面包和管理工具

![image-20220316111141781](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111141781.png)

#### 2．等待一会，会出现两三次Y/N的选择，输入Y即继续。

![image-20220316111202500](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111202500.png)

#### 3.设置启动模式为图形化界面，并设置为默认启动模式。

![image-20220316111222217](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111222217.png)

#### 4.输入命令Reboot重新启动后就是GUI界面了。

###  四、安装并开启SSH 远程登录功能

#### 1.安装OpenSSH-Server

![image-20220316111353986](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111353986.png)

如果你是GUI界面，记得要使用Root账户登录。

![image-20220316111410250](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111410250.png)

#### 2.启动SSH服务器

Start:启动 Restart：重启 Stop：关闭

然后验证一下是否启动22端口。

![image-20220316111435205](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111435205.png)

#### 3.设置SSH服务为开机启动

Enable：启用 Disable：禁用

![image-20220316111442155](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111442155.png)

#### 4.使用Putty远程连接SSH服务

输入IP地址即可连接，默认端口为22

![image-20220316111451162](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111451162.png)

### 五、安装Docker 并启动 Kali

#### 1.首先先安装Docker

![image-20220316111526655](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111526655.png)

和上面一样，有Y/N，选Y就完事了。

#### 2.去Docker官网注册一个账户，以便登录。

[https://hub.docker.com/](https://hub.docker.com/)

#### 3.启动Docker，然后登录你的Docker账户，换镜像源，并且拉取Kali镜像。

##### （1）首先启动Docker

![image-20220316111621306](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111621306.png)

##### （2）启动完毕后就可以登录了。

![image-20220316111644261](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111644261.png)

##### （3）换阿里云的镜像源，下载速度会快很多。

进入配置文件【如果没安装VIM请使用VI】

![image-20220316111657232](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111657232.png)

更改镜像源

【注意:请仔细检查，如果错误会导致Docker无法启动】

![image-20220316111718918](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111718918.png)

{&quot;registry-mirrors&quot;: [&quot;https://l10nt4hq.mirror.aliyuncs.com&quot;]}

修改方式和修改网络配置文件的方式一样。

然后加载配置文件，重新启动Docker。

![image-20220316111740001](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111740001.png)

#### （4）拉取Kali镜像【其他镜像类似操作】

![image-20220316111802498](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111802498.png)

查看镜像ID，是否已经下载成功

![image-20220316111748655](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111748655.png)

#### （5）启动Kail容器

带入ImageID可启动，-i -t为前台启动-d为后台启动。

在容器中，按下Ctrl+P+Q就可以退出容器，但不关闭容器。

![image-20220316111810433](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111810433.png)

Docker ps 查看正在运行的容器，这里Kail已正在运行。

### 六、搭建LAMP(Linux+Apache+MySQL+PHP)环境

#### 1.安装Apache

##### （1）使用yum安装

![image-20220316111853049](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111853049.png)

##### （2）启动Apache服务

![image-20220316111900035](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316111900035.png)

##### （3）设置Httod服务为开机启动

![image-20220316111907525](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220316111907525.png)

##### （4)查看服务是否正常运行

浏览器中输入服务器IP或【本地IP】127.0.0.1即可查看。

#### 2.安装MySQL

##### （1）使用yum安装

![image-20220316112102717](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316112102717.png)

##### （2）开启mysql服务，并设置开机启动

![image-20220316112226994](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316112226994.png)

#####   （3）登录数据库

![image-20220316112317194](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316112317194.png)

#### 3.安装PHP

##### （1）使用yum安装

![image-20220316112328371](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316112328371.png)

#### （2） 将PHP与MySQL关联起来

### ![image-20220316112334600](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316112334600.png)

### ![image-20220316112341327](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220316112341327.png)

（3）添加配置文件info.php

```
vi info.php
<?php
      phpinfo(); ?>

```

####   (4)重启Httpd服务

Systemctl restart httpd

#### (5)查看服务是否启动

127.0.0.1/info.php