# SpringMVC

示例案例：SSM_SpringMVC

视频教程：Siki学院SSM教程

### 1.创建工程

IDEA可以直接创建SpringMVC工程    选择创建->Spring->勾选SpringMVC

### 2.配置文件

#### Web.imi

确认拦截器和拦截对象，这是一个动态页面和静态页面的区别所在。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--    加载Spring配置文件-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/applicationContext.xml</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
<!--    将servlet-class和url-pattern构成联系，从而使URL映射到类servlet-class所指定的类中-->
    <servlet>
        <servlet-name>springMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--    加载Spring配置文件-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/springMVC.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
<!--    设置拦截规则-->
    <servlet-mapping>
<!--        拦截规则-->
<!--        1.以扩展名拦截  *.htm *.do *.action 这个名字随意 默认是form，不拦截静态资源 如.jpg .css .js .png   什么情况都可以使用-->
<!--        2."/"拦截       不拦截.jsp    拦截静态资源 如.jpg .css .js .png   RESTful 风格    -->
<!--        3."*/"全部拦截   不推荐使用   -->
        <servlet-name>springMVC</servlet-name>
        <url-pattern>*.form</url-pattern>
    </servlet-mapping>
</web-app>
```

确认了Spring配置文件名称及路径后，创建相应的springMVC.xml 和applicationContext.xml

#### applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">

</beans>
```

#### springMVC.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">
    <!--开启注解扫描-->
    <context:component-scan base-package="com.controller"></context:component-scan>
</beans>
```

确认注解扫描的包，然后创建相应的包，接着创建Controller

#### ItemController

```java
@Controller //确认控制器职位
public class ItemController {
    //代替url 在域名直接输入list.form 就会执行该方法
    @RequestMapping(value = "list.form")
    public ModelAndView list(){
        //创建页面对象
        ModelAndView mav=new ModelAndView();
        //显示相应页面 这个页面自己写
        mav.setViewName("/WEB-INF/jsp/item_list.jsp");
        return mav;
    }
}
```

以上步骤完成后，就可以打开服务器测试了，在主域名后面添加上/list.form 即可访问该页面

######  传递数据

**小坑注意**  写后端请先看看前端！两者的变量名需要需要统一规定！

```java
@Controller
//大前提路径，下面的所有Mapping都要加上这个路径前置
@RequestMapping(value = "/main/")
public class ItemController {
    //代替url 在域名直接输入list.form 就会执行该方法
    //一般在前端使用，会顺便带着参数过来
    //value是一个数组 可以使用多请求空间 记得加上{}
    //method 限定请求类型 当然也是数组，可以多限定，不写的话是全支持，不限定
    @RequestMapping(value ={ "/item/list.do","mylist.do"},method = RequestMethod.POST)
    public ModelAndView list(){
        //创建页面对象
        ModelAndView mav=new ModelAndView();
        //给页面传递数据
        //测试数据 注意：数据的变量名应与前端对应
        ItemInfo itemInfo=new ItemInfo("0","第一","MMO","100");
        ItemInfo itemInfo1=new ItemInfo("1","第2","MMO","100");
        List<ItemInfo> itemList =new ArrayList<>();
        itemList.add(itemInfo);
        itemList.add(itemInfo1);
        //将数据列表返回给页面
        mav.addObject("itemList",itemList);
        //显示相应页面
        mav.setViewName("/WEB-INF/jsp/item_list.jsp");
        return mav;
    }
```

###### Controller方法返回值

**（传递数据方法）数据一般都是从前端传过来的，然后后端处理**

1.直接使用ModelAndView  就是上面的那种方法

2.void：使用原生的Reque，Response。（麻烦）

3.String：转发（forword）,重定向（redirect）,返回视图名（推荐使用，可以用来拆分数据和视图）

```java
    //Sting 转发 从该方法跳转至其他页面
    @RequestMapping("forwardString.do")
	//@RequestBody 使得该方法返回值也能被其他方法接受
	@RequestBody
	//@RequestBody 使得可以接受json数据	
    public  User forwardString(@RequestBody User user){
        //从前端传过来的数据这边可以直接使用了
        //不一定得一模一样的参数类型，只要你的类里包含该参数类型（变量名得一样），例如这边前端只传递了一个String name，它会自动注入到类中相应的变量里
        System.out.println(user.name);
        //forward: + 转发的请求
        // 请求A到请求B  请求A的值，请求B也可以使用 可以通过Return传值
        return  "forward:/main/mylist.do";
    }
    //String 重定向  无法重定向WEB-INF内的jsp 里面的是受保护的
    @RequestMapping("redirectString.do")
    public  String redirectString(){
        return "redirect:/index.jsp";
    }
    //返回视图名：推荐使用 拆分视图和数据之间的关系
    @RequestMapping("modelString.do")
    public  String modelString(Model model){
        //传递数据
        //测试数据 注意：数据的变量名应与前端对应
        ItemInfo itemInfo=new ItemInfo("0","第一","MMO","100");
        ItemInfo itemInfo1=new ItemInfo("1","第2","MMO","100");
        List<ItemInfo> itemList =new ArrayList<>();
        itemList.add(itemInfo);
        itemList.add(itemInfo1);
        model.addAttribute("itemList",itemList);
        return "/main/mylist.do";
    }
```

## 3.异常处理器

[任务学习 - SiKi学院 - 生命不息，学习不止！ (sikiedu.com)](http://www.sikiedu.com/course/277/task/14160/show)

自定义捕获异常，将报错显示在网页上

通过实现HandlerExceptionResolver接口完成异常处理；异常处理器原理；

![img](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageclip_image002.jpg)

使用异常处理器捕获运行时异常；

使用异常处理器捕获自定义异常；

通过注解完成全局异常处理；

通过@ControllerAdvice注解使@ExceptionHandler异常处理注解应用到所有使用@RequestMapping的方法上；

处理运行时异常：直接打印错误信息；

处理自定义异常：将错误信息输出到error页面上； 

## 4.拦截器

[任务学习 - SiKi学院 - 生命不息，学习不止！ (sikiedu.com)](http://www.sikiedu.com/course/277/task/14164/show)

SpringMvc中的拦截器：

SpringMvc拦截器帮我们按照一定规则拦截请求，后根据开发人员自定义的拦截逻辑进行处理；

自定义拦截器需要实现HandlerInterceptor接口；

自定义的拦截器实现类需要在SpringMvc配置文件中配置；

可以配置多个拦截器，配置的顺序会影响到拦截器的执行顺序，配置在前的先执行；

**HandlerInterceptor**有3个接口：

​             i.     **preHandle** 预处理：在拦截方法前执行；

​             ii.     **postHandle** 后处理：在拦截方法后执行；

​            iii.     **afterCompletion** 渲染后处理：在页面渲染后执行；

拦截器的应用：权限检查，日志记录，性能检测等；

![image-20220311092612656](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageimage-20220311092612656.png)

 

**总结的五条拦截器规则；**

**preHandle**预处理：--------------根据拦截器定义的顺序，正向执行；

**postHandle**后处理：-------------根据拦截器定义的顺序，逆向执行；

**afterCompletion**渲染后处理：---根据拦截器定义的顺序，逆向执行；

**postHandle**预处理：-------------所有拦截器都返回成功调用；

**atterCompletion**渲染后处理：---preHandle返回true调用；

## 5.静态资源放行

[任务学习 - SiKi学院 - 生命不息，学习不止！ (sikiedu.com)](http://www.sikiedu.com/course/277/task/14168/show)

在web.xml配置DispatcherServlet的url-pattern以扩展名结尾，例如*.do，*.html 等；

在web.xml配置default servlet-mapping的url-pattern，以目录形式和扩展名形式；

在springmvc.xml中配置<mvc:resources/>，该方法需要注意以下几点：

​             i.     需要开启mvc:annotation-driven注解驱动；

​             ii.     如果配置了拦截器，需要在拦截器中进行过滤，否则会被拦截；

​            iii.     路径名不要打错 ;

参考资料

![Spring_mvc完整处理流程图_普通版](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNote/imageSpring_mvc完整处理流程图_普通版.png)
