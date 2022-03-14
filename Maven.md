# Maven

### Jar包管理工具

下载地址：[Maven – Download Apache Maven](https://maven.apache.org/download.cgi)

### 1.配置文件

在目录下的**conf**文件夹下**settings.xml**

首先先配置一下下载的Jar包放在哪里，直接从上面的注释里复制一份下来即可

```xml
  <!-- localRepository
   | The path to the local repository maven will use to store artifacts.
   |
   | Default: ${user.home}/.m2/repository
  <localRepository>/path/to/local/repo</localRepository>
  -->
  <localRepository>D:\Tools\apache-maven-3.8.5\repository</localRepository>
```

### 2.配置环境变量

计算机属性->高级系统设置->环境变量

首先编辑系统变量 新建一个  名字MAVEN_HOME   路径就是Maven的根目录

然后编辑Path   新建 %MAVEN_HOME%\bin 

![image-20220314111446313](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220314111446313.png)

然后打开Cmd 输入mvn  测试查看结果 

### 3.建立项目

推荐网站：[Spring Initializr](https://start.spring.io/)   设置好项目后直接下载，IDEA可以直接打开  然后会自动下载jar包

### 4.注意事项

后面pom.xml添加jar的时候，如果没设置自动load 记得要在右上角按一下load键，才会去下载依赖。