# 	Spring

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

**IOC的控制**:就是由spring帮我们负责创建销毁对象,掌控对象的生命周期等，我们在需要使用对象的时候跟Spring申请即可，包括一些需要配置的系统类，比如JDBC。

###### 配置XMl

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
</beans>
```

配置完毕后就可以直接向Spring要对象，当然，这个对象类（com.bean.User）是提前设置好的。

```java
private  void Test1(){
//根据配置文件获取容器对象,文件名记得跟着后缀！这边还会自动调用一次所有bean类的构造方法（如果不带延迟加载标签），但是如果构造方法带参，则会报错
ApplicationContext ac=new ClassPathXmlApplicationContext("applicationContext.xml");
//通过getBean获取配置好的User对象（向Spring要对象），如果对象带延迟加载标签，则构造方法会在这里再启动。
User user = ac.getBean(User.class);
System.out.println(user);
//关闭容器（如果对象还是Spring管理，且标注了销毁方法，则会执行销毁方法，需要ClassPathXmlApplicationContext 创建的容器才能执行close）
//ac.close();
```

但是这样只能New对象，对象的变量还是需要DI依赖注入的支持。

### 3.根据配置来设置对象的值  DI

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--    配置对象-->
    <!--    name是一个名字，我们可以通过这个name来利用容器获取对象 name可以使用特殊字符，这边还有一个类似于name的标签：ID 但是唯一且不能使用特殊字符，这边统一使用name-->
    <!--    class是被管理对象的全包名，Spring会通过这个包名来创建对象-->
    <!--    layz-init 懒加载，只有在对象创建的的时候再加载，初始化时不会加载并执行构造方法-->
    <!--    scope=“singleton” 设定这个类是单例，只会有一份，后面创建等于是获取，不会再次执行构造方法,prototype则相反，多例的-->
    <!--    init-method 里面可以填写初始化方法，每次创建都会执行-->
    <!--    destroy-method 里面可以填写销毁方法，每次销毁都会执行,但是如果对象是多例的，这个对象创建出来后就交给你管理了，在容器被关闭的时候就不会执行销毁方法了-->
    <bean name="user" class="com.bean.User" lazy-init="true" scope="prototype" init-method="UserInit" destroy-method="UserDestroy">
        <!--        property 设定变量 value 设定值 必须有set方法-->
        <property name="name" value="1111"/>
<!--        引用类型-->
        <property name="country" ref="country"/>
    </bean>

    <bean name="country" class="com.bean.Country" >
        <!--        property 设定变量 value 设定值 必须有set方法-->
        <property name="name" value="China"/>
    </bean>
<!--    构造方法注入-->
    <bean name="Administrator" class="com.bean.Administrator">
<!--        可以指定Type 类型，这样定位会更加精确-->
<!--        index 指定这个参数是在构造方法的位置 如果有多个构造方法重载可以通过顺序，类型来确定使用哪个构造方法-->
        <constructor-arg name="name" value="admin" type="java.lang.String" index="0"/>
        <constructor-arg name="password" value="123"/>
    </bean>
<!--    复杂方法注入-->
    <bean name="team" class="com.bean.Team">
<!--        Array 对象列表就直接使用ref注入 如果只有一个值，也可以像上面变量一样把Value写在标签里面-->
        <property name="userArray">
            <array>
                <ref bean="user"/>
            </array>
        </property>
<!--        List-->
        <property name="userList">
            <list>
                <value>1</value>
                <value>abc</value>
                <ref bean="user"/>
            </list>
        </property>
<!--        Set-->
        <property name="userSet">
            <set>
                <value>1</value>
                <value>set</value>
            </set>
        </property>
<!--        Map-->
        <property name="userMap">
            <map>
                <entry key="username" value="eleven"/>
                <entry key-ref="user" value-ref="country"/>
            </map>
        </property>
<!--        properties-->
        <property name="userProperties">
            <props>
                <prop key="name">Eleven</prop>
                <prop key="age">21</prop>
            </props>
        </property>
    </bean>
</beans>
```

### 4.Spring中的注解配置

使用注解配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">
<!--    可以直接读取其他XML 等于合为一体-->
    <import resource="applicationContext.xml"/>
    <!--component可以理解为对象，我们把对象放进容器中，对Spring来说就是把组件放进来了-->
    <!--base-package: 扫描你给的这个包下所有的类，看上面有没有注解。扫描到注解，会启动注解配置。
                      如果这个包下面还有子包的话，还会扫描子包下的类 -->
    <context:component-scan base-package="com.bean"/>
</beans>
```

在相应的类中标记，即可让Spring管理

```java
//<bean name="user" class="com.bean.User">
//加上注解相当于交给Spring管理了
@Component("Visitor")
//功能相似 但是可以通过不同的注解来区分
//@Controller("Visitor") //对应web层
//@Service("Visitor") //对应Service
//@Repository("Visitor") //对应dao层

//使用注解配置类
@Scope(scopeName = "prototype")
public class Visitor {
    private  String name="666"; //暴力反射注入 不推荐 推荐在set方法上注入
    private  Country country;

    public Country getCountry() {
        return country;
    }
    @Autowired //自动装配 这个类的属性已经是Spring管理的情况下可以自动装配
    public void setCountry(Country country) {
        this.country = country;
    }
    public String getName() {
        return name;
    }
    @Value("我的名字")//Set注入
    public void setName(String name) {
        this.name = name;
    }
    @Override
    public String toString() {
        return "Visitor{" + "name='" + name + '\'' + '}';
    }
    @PostConstruct() //在构造方法后调用 类似于init-method
    public void VisitorInit(){
        System.out.println("Visitor Init");
    }
    @PreDestroy //在销毁后调用 类似于destroy-method
    public  void  VisitorDestroy(){
        System.out.println("Visitor Destroy");
    }
}
```

### 5.使用Junit进单元测试

需要Spring.test包，IDEA @RunWith会自动安装

```java
//使用junit进行测试，帮助我们创建容器
@RunWith(SpringJUnit4ClassRunner.class)
//直接读取配置文件
@ContextConfiguration("classpath:applicationContext_Annotation.xml")
public class TestJUnit {

    //直接使用Spring读取配置好的属性
    @Resource(name = "Visitor")
    private Visitor visitor;
    @Test
    public   void  Test(){
        System.out.println(visitor.toString());
    }
}
```

### 6.Spring中的AOP

可以在不动底层的情况下增强代码。不用一个个重写代码，增强后的类，切入点都会带着这个增强BUFF。

##### JoinPoint 连接点 ：目标对象中 哪些方法会被拦截，然后增强。

##### Pointcut 切入点：筛选接入点，选择想要增强的方法（从接入点中选）。

##### Advice 通知/增强：增强的代码。

##### Introduction 介入/引入 :在执行时期动态加入一些方法或行为。

##### Aspect 切面： 通知+切入点 通知应用到哪个切入点。

##### Target 目标：被代理对象。

##### Weaving 织入：把切面的代码应用到目标对象来创建新的代理对象的过程。

##### Proxy 代理：把切面的代码应用到目标对象来创建新的代理对象。就是创建增强后返回的对象

设置一个类，它就是准备用于增强的类。 

```java
//Target 目标：被代理对象。
public class UserServiceImpl implements UserService {
    @Override
    //JoinPoint 连接点 Pointcut 切入点
    public void Save() {
        System.out.println("Saving");
    }
    @Override
    //JoinPoint 连接点 Pointcut 切入点
    public void Delete() {
        System.out.println("Deleting");
    }
    @Override
    //JoinPoint 连接点 Pointcut 切入点
    public void Update() {
        System.out.println("Updating");
    }
    @Override
    //JoinPoint 连接点 
    public void Find() {
        System.out.println("Finding");
    }
}
```

设置一个代理类，它就是用于返回增强后的类。**做这个的过程就是正在织入。**

```java
//UserService 代理类
public class UserServiceProxy {
    public UserService getUserServiceProxy(UserService userService){
        //动态代理 Proxy 代理
      return (UserService)Proxy.newProxyInstance(UserServiceProxy.class.getClassLoader(), UserServiceImpl.class.getInterfaces(), new InvocationHandler() {
            @Override
          	//Aspect 切面
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                //Advice 通知/增强 原始操作之前
                System.out.println("准备进行数据库操作");
                //调用原始的方法
                Object invoke=method.invoke(userService,args);
                //Advice 通知/增强 原始操作之后
                System.out.println("进行数据库操作结束");
                return invoke;
            }
        });
    }
}
```

然后就可以调用增强后的类了。

```java
public class TestAop {
    @Test
    public  void  Test(){
        //创建代理对象
        UserServiceProxy usProxy= new UserServiceProxy();
        //创建对象
        UserService us=new UserServiceImpl();
        //获得增强后的代理对象
        UserService usPowerUp = usProxy.getUserServiceProxy(us);
        //执行增强后的类的方法
        usPowerUp.Find();
    }
}
```

### 7.AOP的自定义通知

可以配置相应的XML和自定义通知类。将通知方法封装起来。

**需要导入下面两个jar包：aspectjweaver.jar包以及其依赖的aopalliance.jar包。**

首先设置一个自定义通知类，里面包含了通知方法以及如何通知。

```java
//自定义通知类
public class AopAdvice {
    //Before 前置通知 在目标方法前调用
    //在本例子中：就是用于代替System.out.println("准备进行数据库操作"); 类推后置通知
    public  void Before(){
        System.out.println("准备进行数据库操作---------前置通知");
    }
    //以下都是后置通知，在目标方法后调用
    //After 最终通知（在目标方法执行后，无论是否出现异常，都会调用）相当于Finally
    public void After(){
        System.out.println("数据库操作完毕！---------后置通知--最终通知");
    }
    //afterReturning 成功通知（在目标方法执行后，并且执行成功）
    private void  AfterReturning(){
        System.out.println("数据库操作成功！---------后置通知--成功通知");
    }
    //AfterThrowing 异常通知（在目标方法执行后，并且执行失败）
    private void  AfterThrowing(){
        System.out.println("数据库操作失败！---------后置通知--失败通知");
    }
    //Around 环绕通知 需要我们手动调用方法，并且可以设置通知
    public void  Around(MethodInvocationProceedingJoinPoint pjp) throws Throwable {
        System.out.println("数据库手动排查中！---------前置通知--环绕通知");
        Object proceed=pjp.proceed();
        System.out.println("数据库手动排查中！---------后置通知--环绕通知");
    }
}
```

然后配置XML 把相应的通知类配置到需要增强的类中

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

<!--    目标对象（想要增强的对象）-->
    <bean name="userService" class="com.service.UserServiceImpl"></bean>
<!--        通知对象-也就是你自定义的通知类-->
    <bean name="aopAdvice" class="com.aop.AopAdvice"></bean>
    <aop:config>
<!--        切面 通知+切入点 -->
<!--        首先配置切入点-->
<!--        id：唯一标识  expression：切入点表达式 可以配置要增强的方法（就是把连接点变成切入点）-->
        <aop:pointcut  expression="execution( * com.service.*ServiceImpl.*(..))" id="userServicePc"/>
<!--可以使用*进行代替,参数用..代替，代替就说明只要符合没被代替的条件都会被配置，包括包名，方法，类名中的一段都行-->
<!--        <aop:pointcut id="userServicePcAll" expression="execution(public  *  com.service.*ServiceImpl.*(..))"/>-->
<!--        然后配置通知 ref 你的自定义通知类-->
        <aop:aspect ref="aopAdvice">
<!--            配置各种通知-->
            <aop:before method="Before" pointcut-ref="userServicePc"/>
            <aop:after method="After" pointcut-ref="userServicePc"/>
            <aop:after-returning method="AfterReturning" pointcut-ref="userServicePc"/>
            <aop:after-throwing method="AfterThrowing" pointcut-ref="userServicePc"/>
            //todo
<!--            <aop:around method="Around" pointcut-ref="userServicePc"/>-->
        </aop:aspect>
    </aop:config>
</beans>
```

### 8.Spring与JDBC

这边使用了c3p0与数据库连接，**所以需要c3p0-0.9.5.2.jar包和mchange-commons-java-0.2.12**

设置好Dao层和数据库信息，就可以准备尝试查询了。数据库信息最好写在配置文件里。这边只是做测试。

```java
public class UserDaoImpl implements  UserDao{

    //配置c3p0 //连接数据库
    private  static  ComboPooledDataSource dataSource;
    static{
        try{
            dataSource =new ComboPooledDataSource();
            dataSource.setDriverClass("com.mysql.cj.jdbc.Driver");
            dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=GMT%2B8");
            dataSource.setUser("root");
            dataSource.setPassword("");
        } catch (PropertyVetoException e) {
            System.out.println("数据库连接出错！"+e);
        }
    }

    @Override
    public User SelectUserByID(Integer id) {
        JdbcTemplate jt=new JdbcTemplate(dataSource);
        String sql="select * from user where  id= ?";
        User user = jt.queryForObject(sql, new RowMapper<User>() {
            @Override
            public User mapRow(ResultSet resultSet, int i) throws SQLException {
                User user = new User();
                user.setName(resultSet.getString("name"));
                return user;
            }
        }, id);
        return user ;
    }
}
```

```java
public class TestJDBC {
    @Test
    public void Test(){
        UserDao userDao=new UserDaoImpl();
        User user=userDao.SelectUserByID(1);
        System.out.println(user.getName());
        }
    }
```

