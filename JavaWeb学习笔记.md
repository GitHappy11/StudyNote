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

#### 在Jsp文件中写Java代码

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
<form action="数据传输到哪个文件（这里可以是Jsp也可以是Servlet）" method="传输方式">
    <input type="类型" name="该Input的名称"/>
    <input type="password" name="password"/>
     <!-- radio表示单选，名字一样的就是和哪个一起单选 然后标签里面的Value值就是传输到服务器的值--> 
  性别：男<input type="radio" name="sex" value="男性"/>女<input type="radio" name="sex" value="女性"/>
     <!--一个提交按钮--> 
    <input type="submit"/>
</form>
 <!--上面传输过来的参数。都会保存在Request里，随时在任何文件内调用--> 
```

#### Request【单个网页】

每个分支网页都会有一个，在网页之间相互传递信息和存储信息

**生命周期：**一个请求生成和下一个请求生成

####page【this】

得到页面内的数据，无法进行传递。

#### PageContext【this】

可以获取到其他的内置对象，也可以获得页面内的数据，然后放置到其他的内置对象中。

#### Session【单个客户端】

在用户的访问及服务器的回应（可能不止一次）就是会话，直到用户关闭网页。每一个客户端对应着一个Session。这个就类似于一个全局的Request，不需要再对象里传来传去。直接可以调用。

```java
//利用Session来存储数据 操作类似于Request 
session.setAttribute("user",user);
```

**生命周期：**打开一个会话的时候和关闭会话。

#### Application【全局】

本质上是一个ServetContext 类 可以new。

整个服务器的Jsp就是一个新建的ServetContext。相当于核心存储信息。

存储一些于所有客户端都有关系的消息。所有客户端共享且相同。

**生命周期：**打开服务器和关闭服务器。

#### 5.Servlet

本质上就是一个类继承了HttpServlet。

然后在类的头上写

```jAva
@WebServlet(name = "RegisterServlet（类名）", value = "/RegisterServlet（类的路径）"
```

然后可以在相应的类（doPost）来写Java代码

和上面一样，接收消息处理消息。

#### Web.xml

配置书写规则以及一些文件（Servlet）的访问路径

该文件路径

D:\Work\JavaWebData\Tomcat\apache-tomcat-9.0.41\webapps\examples\WEB-INF

```xml
<display-name>Second</display-name> //项目名
<welcome-file-list>  //默认的欢迎页面，可有多个，优先级从上到下，需要在web目录下
       <welcome-file>index.jsp</welcome-file>
</welcome-file-list>
```



#### 请求转发和重定向

```java
//（请求转发）跳转至登录界面，然后把两个消息包发送给它，让它去处理 这里不会重新创建。它已经是一个在使用中的对象了
request.getRequestDispatcher("Login.jsp").forward(request,response);
//重定向 和转发有什么区别？（没有消息包，且JSP会切回Login.Jsp（转发不会，还是会停留在Register.jsp文件里））【直接访问Login.jsp】一般是上一个文件遇到无法处理的事件后，直接跳转到Login，是新的Request对象。类似于重新上了这次网页（重启）。
response.sendRedirect("Login.jsp");
```

什么时候使用重定向？没有消息包需要传输和需要更新Request的时候使用。应用场景：用户直接转向其他场景，不需要以前遗留的Request包。

#### 引用页面

经典例子：网页头部和尾部， 如果不使用引用的方式，当你维护头部部分时，每个页面都需要修改，类似于要做成一个预制体。

```jsp
<jsp:include page="head.jsp"></jsp:include>
//这里有一些中间内容。。。
<jsp:include page="footer.jsp"></jsp:include>
```

#### 相对路径和绝对路径

绝对路径就不说了，如果转到其他环境则就会变化【根目录为项目文件夹，服务端可用】，而相对路径在跳转的时候【跳转到其他文件夹的文件】，这样那个文件的相对路径可能就会混乱，导致跳转失败，最好的方法就是使用路径变量来保证绝对/相对路径的正常跳转。

```html
href="<%request.getContextPath()%>/css/style.css" //客户端路径就用这种方式 需要浏览器去解析 
```

#### 关于EL表达式

```html
 request.getAttribute("msg");
<%--这里使用的是EL表达式，可以直接获得--%>
----${msg}---
<%--可以使用方法，获得字符串--%>
----${msg.getUsername}--
<%--也可以使用变量
----${msg.username}---
<%--还可以从Map中获得值【需要.键】--%>
----${map.name}---
<%--也能从List中获取相应的对象--%>
----${List[2].name}---
<%--也可以判断某一个对象是否为空--%>
----${empty user123}---
```

如果其他域里有相同的变量参数：将会以以下的顺序来优先读出内容：page，request，session，application，

#### JSTL

用标签语言来代替信息传递语言，可以更方便的传递信息,类似于将Set，Get 方法封装了起来，更好的调用，再配合EL表达式，进行取出。

需要引入Jar包，更多相关请看文档。

```jsp
--使用标签需要引用核心标签库以及标签变量
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>

<%--下面的这句话就等于 <%request.setAttribute("username","JSTLtest");%>--%>
<c:set var="username" value="JSTLtest"  scope="request"></c:set>
<%--以下两种方式都可以取出,一般情况都使用EL表达式--%>
<c:out value="${username}"></c:out>
${username}

<%--也可以使用判断语句--%>
<c:set var="salary" scope="session" value="${0000*2}"/>
<c:if test="${salary > 2000}">
<%--如果不符合条件下面这条就不会显示--%>
<p>我的工资为: <c:out value="${salary}"/><p>
</c:if>

<%--<c:choose>标签与Java switch语句的功能一样，用于在众多选项中做出选择。--%>
<%--switch语句中有case，而<c:choose>标签中对应有<c:when>，switch语句中有default，而<c:choose>标签中有<c:otherwise>。--%>
<c:choose>
    <c:when test="${salary <= 0}">
        太惨了。
    </c:when>
    <c:when test="${salary > 1000}">
        不错的薪水，还能生活。
    </c:when>
    <c:otherwise>
        什么都没有。
    </c:otherwise>
</c:choose>
```

除了以上基本使用方式，还有循环，遍历等功能，具体查看文档

#### Filter（过滤器）

用于网页与网页之间的跳转后的一些通用设置（跳转后执行的一些方法），例如设置编码，但是要记得有跳转方法，否则会卡在过滤器方法里，导致白屏网页

可以在XML中设置过滤器的配置，也可以在过滤器文件里写通过哪些网页。

#### IDEA（Java）使用技巧

sout 快速打出System.out.println（）

serr 快速打出System.err.println（）

#### 注意事项

更新类的话要重启服务器（可以看底部资料解决该问题： IDEA 中解决tomcat Web项目修改代码需要重新部署项目的问题）。

XML是区分大小写的。

Response输出的内容都会显示在最前端（源代码中）

Java的输出语句模块也可以插入HTML代码，来改变字体样式。相当于富文本，并不是Java代码支持这个功能。

反之也一样，HTML语句里也可以插入Java代码，只要记得使用<%%>标识就好。

Request生命周期只有一次请求，当年请求第二次的时候，前一个Request就会被销毁掉，每次请求都是新的Request；

有的时候中文无法正常显示要转译一下。

控制台输出乱码先设置Tomcat服务器中font文件夹下的logging.properties文件，将UTF-8设置为GBK 然后再Idea里面的配置设置虚拟机选项-Dfile.encoding=GBK

请在跳转网页的代码之前把所有该网页的所有代码事件处理完毕。

同一个Jsp文件里面代码都是在同一个方法下的，并不会因为你用多个<%%>区分了多个代码块，而分为多个方法，举个例子，一个Jsp文件内，不能用同一个名称命名一个变量。



#### 学习资料

http://www.sikiedu.com/my/course/214 课程地址

[XML系列教程 (w3school.com.cn)](https://www.w3school.com.cn/x.asp) 语法知识

[如何使用 IntelliJ IDEA（2020.2）构建一个JavaWeb项目_LK_Lawliet的博客-CSDN博客](https://blog.csdn.net/LK_Lawliet/article/details/108797483)

https://blog.csdn.net/qq_48736958/article/details/110069705 IDEA 中解决tomcat Web项目修改代码需要重新部署项目的问题

[JSP 标准标签库（JSTL） | 菜鸟教程 (runoob.com)](https://www.runoob.com/jsp/jsp-jstl.html)

