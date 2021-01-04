# JavaWeb学习笔记

**Git项目：FristJavaWeb**

#### 1.初识XML

XML文件开头必须是XML声明

然后是其他约束规则

再然后就是根元素 和 元素

**约束文档**

（1）DTD约束文档

分为内部引入，外部引入，外部引入（网络）

（2）Schema约束文档

可以同时用两个约束文档，如果有重复的约束条件，可以使用命名空间区分

详情请看FristJavaWeb项目。

**Dom4J解析建立XML文档**

详情请看FristJavaWeb项目。

实际项目在暗黑战神项目中有应用到。

#### 2.在Jsp文件中写Java代码

```Java
//引入包的形式
<%@ page import="javax.xml.crypto.Data" %>
<%@ page import="java.util.Date" %>
```

```java
<%!int count=0;  %> //属于Java自定义变量代码块
 //和在Java代码部分定义有什么区别？答：这里面定义的是成员（全局）变量，在网页上会保存下来，而直接写在java代码部分的属于局部变量，每当刷新网页就会初始化。
    
<% out.println("Hello World") %>  //属于Java代码部分，不带System，会在网页上显示
    
<%=str+Show()%>
//内容输出表达式，可以直接输出字符串和执行方法

http://localhost:8080/FirstJavaWeb_war_exploded/index.jsp?username="Happy11"&password=111
//在网址最后+?+变量名+内容 可以给服务端传参数
out.println(request.getParameter("username"));
out.println(request.getParameter("password"));
//然后服务端用Request方法来接收这个参数

```

```html
 <!--一个登陆示例--> 
<form action="数据传输到哪个文件" method="传输方式">
    <input type="类型" name="该Input的名称"/>
    <input type="password" name="password"/>
     <!-- radio表示单选，名字一样的就是和哪个一起单选 然后标签里面的Value值就是传输到服务器的值--> 
  性别：男<input type="radio" name="sex" value="男性"/>女<input type="radio" name="sex" value="女性"/>
     <!--一个提交按钮--> 
    <input type="submit"/>
</form>
 <!--上面传输过来的参数。都会保存在Request里，随时在任何文件内调用--> 
```



#### IDEA（Java）使用技巧

sout 快速打出System.out.println（）

serr 快速打出System.err.println（）

#### 注意事项

XML是区分大小写的。

Response输出的内容都会显示在最前端（源代码中）

Java的输出语句模块也可以插入HTML代码，来改变字体样式。相当于富文本，并不是Java代码支持这个功能。

反之也一样，HTML语句里也可以插入Java代码，只要记得使用<%%>标识就好。

Request生命周期只有一次请求，当年请求第二次的时候，前一个Request就会被销毁掉，每次请求都是新的Request；

有的时候中文无法正常显示要转译一下。



#### 学习资料

[XML系列教程 (w3school.com.cn)](https://www.w3school.com.cn/x.asp) 语法知识

[如何使用 IntelliJ IDEA（2020.2）构建一个JavaWeb项目_LK_Lawliet的博客-CSDN博客](https://blog.csdn.net/LK_Lawliet/article/details/108797483)



