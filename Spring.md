# Spring

示例案例：SSM_Spring

视频教程：Siki学院SSM教程

官方文档：[Spring | Home](https://spring.io/)

### 1.创建Spring项目并启动

IDEA可以直接创建Spring项目并自动补足依赖

然后创建依赖约束XML(applicationContext.xml)  IDEA也可以直接创建

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

### 2.向Spring要对象   IOC

Spring是一个容器，在XML里配置好对象后，就可以直接向Spring要对象，而不需要我们去New对象

**IOC的反转:**创建对象这份工作由我们自己执行反转给spring帮我执行

**IOC的控制**:就是由spring帮我们负责创建销毁对象,掌控对象的生命周期等，我们在需要使用对象的时候跟Spring申请即可。

###### 配置XMl

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
<!--    配置对象-->
    <bean name="user" class="com.bean.User"></bean>
</beans>
```

配置完毕后就可以直接向Spring要对象，当然，这个对象类（com.bean.User）是提前设置好的。

```java
private  void Test1(){
//根据配置文件获取容器对象,文件名记得跟着后缀！
ApplicationContext ac=new ClassPathXmlApplicationContext("applicationContext.xml");
//通过getBean获取配置好的User对象（向Spring要对象）
User user = ac.getBean(User.class);
System.out.println(user);
```

但是这样只能New对象，对象的变量还是需要DI依赖注入的支持。

### 3.根据配置来设置对象的值  DI

依赖注入，将值通过配置的方式（XML）为变量初始化/赋值。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
<!--    配置对象-->
    <bean name="user" class="com.bean.User">
    <!--    配置对象变量-->
        <property name="name" value="Happy"></property>
    </bean>
</beans>
```

