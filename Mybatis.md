# Mybatis

## For Java

示例案例：SSM_Mybatis

视频教程：Siki学院SSM教程

### 1.引入Lib，需要Mybatis以及依赖库，和数据库驱动的Jar包

### 2.创建核心XML 配置文件（sqlMap），包含了对 MyBatis 系统的核心设置。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
<!--    默认配置文件-->
    <environments default="development">
        <environment id="development">
<!--            使用JDBC的事务-->
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
<!--                数据库驱动-->
                <property name="driver" value="${driver}"/>
<!--                数据库地址-->
                <property name="url" value="${url}"/>
<!--                数据库账号-->
                <property name="username" value="${username}"/>
<!--                数据库密码-->
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
<!--        Mapper映射文件的位置-->
        <mapper resource="org/mybatis/example/BlogMapper.xml"/>
    </mappers>
</configuration>
```

### 3.创建Mapper

首先要创建一个用于映射的类（User.java）。

```java

public  class  User
{
    int id;
    String name;
    boolean isMan;
    Integer age;
    Data time;
//后面省略构造函数
}
```

然后再创建用于映射的XML文件（UserMapper.xml）

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--mapper所使用的命名空间【限定某些类使用】-->
<mapper namespace="UserMapper">
<!--    编写查询语句  查询的内容="该方法的名字"  parameterType="查询对象的数据类型"   resultType="返回值的数据类（映射类）"  -->
    <select id="selectUserByID"  parameterType="int"  resultType="com.bean.User">
-- sql语句
    select * from user where id = #{id}
  </select>
</mapper>

```

### 4.初始化Mybatis

创建初始化类（InitMybatis） 尝试使用

```java
public class InitMybatis
{
    //配置文件路径
    String resourcePath="sqlMapConfig.xml";
    //入门程序           声明异常
    public  void Init() throws IOException
    {
        //读取配置文件
        InputStream inputStream= Resources.getResourceAsStream(resourcePath);
        //需要sqlSessionFactoryBuilder
        SqlSessionFactoryBuilder ssfb=new SqlSessionFactoryBuilder();
        //创建sqlSessionFactory
        SqlSessionFactory ssf=ssfb.build(inputStream);
        //生产sqlSession
        SqlSession ss=ssf.openSession();
        //操作数据库   参数：Mapper.id    查询内容
         User user= ss.selectOne("UserMapper.selectUserByID",1);
        System.out.println(user.getName());

    }
}
```

