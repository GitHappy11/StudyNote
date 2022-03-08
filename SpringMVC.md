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
