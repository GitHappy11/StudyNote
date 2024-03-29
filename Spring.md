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
            dataSource.setDriverClass("com.mysql.jdbc.Driver");
            dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test");
            dataSource.setUser("root");
            dataSource.setPassword("");
        } catch (PropertyVetoException e) {
            System.out.println("数据库连接出错！"+e);
        }
    }
    JdbcTemplate jt=new JdbcTemplate(dataSource);

    @Override
    public void SaveUser(User user) {
        String sql="insert into user values (null,?,null ,null ,null ,null )";
        jt.update(sql,user.getName());
    }

    @Override
    public void DeleteByID(Integer id) {
        String sql="delete from user where id =?";
        jt.update(sql,id);
    }

    @Override
    public void UpdateByUser(User u,Integer id) {
        String sql="update user set name=? where id= ?";
        jt.update(sql,u.getName(),id);
    }

    @Override
    public User SelectUserByID(Integer id) {
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

    @Override
    public List<User> SelectAllUser() {
        String sql="select * from user";
        List<User> userList = jt.query(sql, new RowMapper<User>() {
            public User mapRow(ResultSet resultSet, int i) throws SQLException {
                User user = new User();
                user.setName(resultSet.getString("name"));
                return user;
            }
        });
        return userList ;
    }

    @Override
    public Integer SelectAllUserToNum() {
        String sql="select count(*) from user";
        Integer count = jt.queryForObject(sql, Integer.class);
        return count;
    }
}

```

```java
//老套的测试，这边就不多写了
public class TestJDBC {
    @Test
    public void Test(){
        UserDao userDao=new UserDaoImpl();
        User user=userDao.SelectUserByID(1);
        System.out.println(user.getName());
        }
    }
```

上面是一般使用情况，这边开始使用Spring开始管理

#### 首先创建XML文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
<!--依赖关系 DAO->jdbcTemplate->dataSource-->

<!--    DataSource-->
    <bean name="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/test"/>
        <property name="user" value="root"/>
        <property name="password" value=""/>
    </bean>
<!--    jdbcTemplate-->
    <bean name="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>
<!--    dao-->
    <bean name="userDao" class="com.dao.UserDaoImpl">
        <property name="jt" ref="jdbcTemplate"/>
    </bean>
<!--    这边可以直接撇开jdbcTemplate 直接注入 详情查看siki spring 29课时-->
    <!--依赖关系 DAO->dataSource-->
<!--    <bean name="userDao" class="com.dao.UserDaoImpl">-->
<!--        <property name="dataSource" ref="dataSource"/>-->
<!--    </bean>-->
    
</beans>
```

然后就可以使用Spring注入跑测试了

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext_JDBC.xml")
public class TestJDBC {
    @Resource(name = "userDao")
    private UserDao userDao;
    @Test
    public void Test(){
        System.out.println(userDao.SelectUserByID(8).getName());
        }
    }
```

当然也可以外部引入JDBC的配置文件

##### db.properties

```properties
jdbc.driverClass =com.mysql.jdbc.Driver
jdbc.jdbcUrl =jdbc:mysql://localhost:3306/test
jdbc.user =root
jdbc.password =
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context-4.2.xsd">
<!--依赖关系 DAO->jdbcTemplate->dataSource-->
<context:property-placeholder location="db.properties"/>
<!--    DataSource-->
    <bean name="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driverClass}"/>
        <property name="jdbcUrl" value="${jdbc.jdbcUrl}"/>
        <property name="user" value="${jdbc.user}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
```

### 9.SpringAOP事务

使得JDBC语句有同生共死的功能，例如转账模式，在程序中，转账一人扣钱，一人加钱，但是如果扣钱失败，加钱也应该跟着失败，而不是扣钱失败，加钱成功

**注意事项：数据库引擎必须是innodb才能使用事务操作！MySQL默认使用的引擎是MyISAM**

MySQL 中修改数据表的存储引擎的语法格式如下：

```mysql
ALTER TABLE <表名> ENGINE=<存储引擎名>;
```

原子性（Atomicity）：一个事务中的多个操作要么都成功要么都失败。
一致性（Consistency）：(例如存钱操作,存之前和存之后的总钱数应该是一致的。
隔离性（Isolation）：事务与事务应该是相互隔离的。
持久性（Durability）：事务一旦提交,数据要持久保存。

配置XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context-3.0.xsd
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.0.xsd">
<!--依赖关系 DAO->jdbcTemplate->dataSource-->
<context:property-placeholder location="db.properties"/>
<!--    DataSource-->
    <bean name="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driverClass}"/>
        <property name="jdbcUrl" value="${jdbc.jdbcUrl}"/>
        <property name="user" value="${jdbc.user}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
<!--    jdbcTemplate-->
    <bean name="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>
<!--    dao-->
    <bean name="userDao" class="com.dao.UserDaoImpl">
        <property name="jt" ref="jdbcTemplate"/>
    </bean>
<!--    这边可以直接撇开jdbcTemplate 直接注入 详情查看siki spring 29课时-->
    <!--依赖关系 DAO->dataSource-->
<!--    <bean name="userDao" class="com.dao.UserDaoImpl">-->
<!--        <property name="dataSource" ref="dataSource"/>-->
<!--    </bean>-->

<!--    AOP事务配置-->
<!-- 配置事务核心管理器 不同平台不一样-->
    <bean name="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

<!--    事务通知 开启配置-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
<!--        配置方法-->
        <tx:attributes>
<!--            方法名-隔离级别-传播行为-是否对数据库进行修改(只读模式)-有修改就是false-->
            <tx:method name="SaveAndSelect" isolation="DEFAULT" propagation="REQUIRED" read-only="false"/>
<!--             也可以使用通配符*-->
            <tx:method name="Select*" isolation="DEFAULT" propagation="REQUIRED" read-only="false"/>
        </tx:attributes>
    </tx:advice>
<!--    AOP配置-->
    <aop:config>
        <aop:pointcut  expression="execution( * com.dao.*Impl.*(..))" id="txPc"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPc"/>
    </aop:config>
</beans>
```

配置方法 如果第二个对数据库的操作出现了问题，则第一个操作就会回滚

```java
    public void SaveAndDelete() {
        User user=new User();
        user.setName("AOP事务");
        SaveUser(user);
        //制造报错
        int a=1/0;
        DeleteByID(5);
    }
```

注解法  只要开启即可

```xml
<!--    事务通知 开启注解事务-->
    <tx:annotation-driven/>
```

```java
    @Transactional(isolation = Isolation.DEFAULT,propagation = Propagation.REQUIRED,readOnly = false)
    public void SaveAndDelete() {
        User user=new User();
        user.setName("AOP事务");
        SaveUser(user);
        //制造报错
        int a=1/0;
        DeleteByID(5);
    }
```

### 10.Spring与Mybatis

需要再添加两个包：mybatis-3.5.7.jar   mybatis-spring-2.0.7.jar

然后创建SqlMapConfig.xml  确认下哪些类需要映射 这边没有写数据库配置，因为会在后面的Spring配置。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <typeAliases>
        <package name="com.bean"/>
    </typeAliases>
</configuration>
```

然后创建Mapper接口及相应的Mapper.xml

```java
public interface UserMapper {
    //操作数据库进行扣款和加款
    //扣款
    void  subMoney(User user);
    //加款
    void  addMoney(User user);
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--mapper所使用的命名空间【限定某些类使用】-->
<mapper namespace="com.dao.mapper.UserMapper">
<!--    使用占位符#{}，可以使用到User里的参数-->
    <update id="subMoney" parameterType="User">
        update user set money = money - #{transferMoney} where name =#{name};
    </update>
    <update id="addMoney" parameterType="User">
        update user set money = money + #{transferMoney} where name=#{name};
    </update>
</mapper>
```

然后Spring配置，如果需要事务的话就配置事务

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context-3.0.xsd
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">
    <context:property-placeholder location="db.properties"/>
    <!--    DataSource-->
    <bean name="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driverClass}"/>
        <property name="jdbcUrl" value="${jdbc.jdbcUrl}"/>
        <property name="user" value="${jdbc.user}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
<!--    Mybatis 基础配置 数据库配置 表配置-->
<bean name="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"/>
    <property name="configLocation" value="sqlMapConfig.xml"/>
</bean>
<!--    mapper工厂 配置基本包-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.dao.mapper"/>
    </bean>
<!--    配置业务实现类-->
    <bean name="userServiceImpl" class="com.service.UserServiceImpl"></bean>

    <!-- 需要事务核心管理器 -->
    <bean name="mybatisTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!-- 配置事务通知 -->
    <tx:advice id="txAdvice" transaction-manager="mybatisTransactionManager">
        <tx:attributes>
<!--            哪些方法需要这个事务-->
            <tx:method name="transferMoney" isolation="DEFAULT" propagation="REQUIRED" read-only="false"/>
        </tx:attributes>
    </tx:advice>
    <!-- 配置aop -->
    <aop:config>
        <aop:pointcut expression="execution(* com.service.*ServiceImpl.*(..))" id="txPc"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPc"/>
    </aop:config>
</beans>
```

然后就可以将Spring配置进业务层实现类里，进行测试

```java
public class UserServiceImpl implements UserService {
    //接口用类型进行配置
    @Resource(type=UserMapper.class)
    private UserMapper userMapper;
    @Override
    public void transferMoney(String subName,String addName,int money) {
        //确定金额和对象
        User user=new User();
        user.setName(subName);
        user.setTransferMoney(money);
        //确定了user和money 进行扣款操作
        userMapper.subMoney(user);
//        int a=1/0; //制造报错 测试回滚
        //被接收人
        User user2=new User();
        user2.setName(addName);
        user2.setTransferMoney(money);
        userMapper.addMoney(user2);
    }
```

当然测试类也要相应的配置

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext_Mybatis.xml")
public class TestMyBatis {
    @Resource(name = "userServiceImpl")
    private UserService userService;
    @Test
    public  void Test(){
       userService.transferMoney("哈哈哈","JDBC",10);
    }
}
```

