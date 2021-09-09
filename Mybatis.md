

# Mybatis

## For Java

示例案例：SSM_Mybatis

视频教程：Siki学院SSM教程

官方文档：[mybatis – MyBatis 3 | 入门](https://mybatis.org/mybatis-3/zh/getting-started.html)

### 1.引入Lib，需要Mybatis以及依赖库，和数据库驱动的Jar包

### 2.创建核心XML 配置文件（sqlMap），包含了对 MyBatis 系统的核心设置。

配置顺序有规则【可跳过某些配置段】：详情请查看官方文档

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--   读取配置文件【可省略，明文直接写在配置文件】-->
    <properties resource="db.properties" />
<!--    别名设置-大小写不敏感【可省略】-->
    <typeAliases>
<!--        设置类的别名-->
        <typeAlias type="com.bean.User" alias="user" />
<!--        设置包名，就可以直接扫描这个包下的所有类，就无需包名前缀了 -->
        <package name="com.bean"/>
    </typeAliases>
<!--    数据库连接信息-->
    <environments default="development">
        <environment id="development">
<!--            使用JDBC的事务-->
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
<!--                数据库驱动-->
                <property name="driver" value="${jdbc.driver}"/>
<!--                数据库地址-->
                <property name="url" value="${jdbc.url}"/>
<!--                数据库账号-->
                <property name="username" value="${jdbc.username}"/>
<!--                数据库密码-->
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
<!--多种映射方式 单选-->
    <mappers>
<!--        Mapper映射文件的位置【相对路径】-->
<!--        <mapper resource="mapper\UserMapper.xml"/>-->
        <!--        Mapper映射文件的位置【绝对路径】-->
<!--        <mapper url="file:\\\D:\Java\SSM_Mybatis\src\mapper\UserMapper.xml"/>-->
        <!--        Mapper映射文件的位置【以接口映射，配置文件名必须与接口名相同且在相同目录下】-->
        <mapper class="mapper.UserMapper"/>
        <mapper class="mapper.CountryMapper"/>
        <!--        Mapper映射文件的位置【映射包和子包下的所有文件，规则与接口映射相同[一次性把所有接口都映射完]】-->
<!--        <package name="mapper"/>-->

    </mappers>
</configuration>
```

### db.properties配置文件

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/test?useSSL=false
jdbc.username=root
jdbc.password=zxc123456
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

sql语句必须成立才能返回相应的参数，包括后面的模糊查询，你设置了几个条件就必须给几个条件。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--mapper所使用的命名空间【限定某些类使用】-->
<mapper namespace="mapper.UserMapper">
<!--    编写查询语句  查询的内容="该方法的名字"  parameterType="查询对象的数据类型"   resultType="返回值的数据类（映射类）"  -->
    <select id="selectUserByID"  parameterType="int"  resultType="com.bean.User">
-- sql语句 查询 某ID 单返回
    select * from user where id = #{id}
  </select>
    <!--    这里设置了别名 可以直接使用别名  不需要带包名 -->
    <select id="selectUsersByName"  parameterType="String"  resultType="user">
-- sql语句 模糊查询 某姓名 多返回
    SELECT * FROM  user  WHERE  name  LIKE "%"#{name}"%"
  </select>
<!--    需要查询某个类下的某个对象的某个变量值-->
    <select id="selectUserByUserVoID"  parameterType="UserVo"  resultType="com.bean.User">
-- sql语句 查询 某ID 单返回
    select * from user where id = #{user.id}
  </select>
    <select id="selectUserCount"   resultType="int">
-- sql语句 查询 一共有多少条信息 这里程序中没有返回值的话要按照SQL返回的类型进行设置返回值类型
    select COUNT(*) from user
  </select>
<!--   关于resultMap： Type还是要返回的类型【这里就算是List也没关系。他会自动加到列表中】 id与下面的resultMap要统一，自定义-->
    <resultMap  type="com.bean.User" id="user">
<!--   id表示主键 result表示普通的属性    这边就算映射关系如果类的变量名和数据库的变量名不一致就需要用这个方法进行手动映射，如果相同则可以省略不写，这里只是做一个演示，本可以省略-->
        <id property="id" column="id"></id>
    </resultMap>
    <select id="selectAllUsers" resultMap="user">
        select * from  user
    </select>
<!--    通过条件查询符合条件的用户列表-->
    <select id="selectIfUsers" parameterType="User" resultType="User">
        SELECT * From user WHERE isMan=#{isMan} AND  name like "%"#{name}"%" AND c_id=#{c_id}
    </select>
<!--插入和删除-->
    <insert id="insertUser"  parameterType="com.bean.User">
        insert into user value (#{id},#{name},#{isMan},#{age},#{time})
    </insert>
    <update id="UpdateUserByID" parameterType="com.bean.User">
        update user  set name =#{name} where ID =#{id}
    </update>
    <delete id="DeleteUserByID" parameterType="com.bean.User">
        delete from user where id=#{id}
    </delete>
</mapper>

```

一对一查询及一对多查询

```xml
<!--    使用ResultMap进行多表查询 必须要写映射，就算名字一样也要写- 配置文件如果以及绑定了包名就不需要再写前置了  一对一查询 每一个用户只属于一个国家-->
    <resultMap id="usersVo" type="UserVo">
<!--        对应的属性-->
        <id property="id" column="id"/>
        <result property="name" column="name"/>
<!--        对应的类-->
        <association property="country" javaType="Country">
            <!--在对应的类里在映射一次属性，多表查询必须要写映射，就算名字一样也要写-->
            <result property="c_name" column="c_name"/>
        </association>
    </resultMap>
<!--    你查什么数据就会返回什么数据，如下例子 只查这三个属性就只会返回这个三个属性，其他属性都是null-->
    <select id="selectAllUsersVo" resultMap="usersVo">
        SELECT u.id,u.name,c.c_name FROM `user` u LEFT JOIN country c ON u.c_id=c.c_id
    </select>
<!--一对多 一个国家有多个用户-->
    <resultMap id="countryVo" type="CountryVo">
        <id property="c_id" column="c_id"/>
        <result property="c_name" column="c_name"/>
<!--        一对多关系-->
        <collection property="userList" ofType="User">
            <id property="id" column="id"/>
            <result property="name" column="name"/>
        </collection>
    </resultMap>

    <select id="selectAllCountryVo" resultMap="countryVo">
        select c.c_id,c.c_name,u.id,u.`name` from  country c LEFT JOIN  `user` u ON u.c_id=c.c_id
    </select>
```

#### 动态SQL标签

##### if标签  使用if语句拼接SQL语句，防止条件不足导致SQL语句不成立 也有可能导致拼接后SQL语句不成立。

```sql
<!--    通过条件查询符合条件的用户列表 使用if语句拼接SQL语句，防止条件不足导致SQL语句不成立-->
    <select id="selectIfUsers" parameterType="User" resultType="User">
        SELECT * From user WHERE
        <if test="isMan!=null and isMan!='' ">
            isMan=#{isMan}
        </if>
        <if test="name!=null and name!=''">
            AND  name like "%"#{name}"%"
        </if>
         <if test="c_id!=null and c_id!=''">
             AND c_id=#{c_id}
         </if>
    </select>
```

##### Where标签  解决and符号问题，代替where关键词，防止使用if时拼接sql导致sql语句不成立

```sql
SELECT * From user
        <where>
        <if test="isMan!=null and isMan!='' ">
            isMan=#{isMan}
        </if>
        <if test="name!=null and name!=''">
            AND  name like "%"#{name}"%"
        </if>
         <if test="c_id!=null and c_id!=''">
             AND c_id=#{c_id}
         </if>
        </where>
```

##### trim标签 定制where标签的规则

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
        //生产对象
        User user =new User(3,"Two",true,11,new Date());
        //插入操作
        ss.insert("UserMapper.insertUser",user);
        //改动数据库的操作都需要提交
        ss.commit();
        //操作数据库   参数：Mapper.id    查询内容
        User user= ss.selectOne("UserMapper.selectUserByID",1);
        //请注意：单返回和多返回的方法不一样！
        List<User> users= ss.selectList("UserMapper.selectUserByName","O");
        System.out.println(user.getName());
    }
}
```

### 5.建立Dao层方便使用

如果使用动态代理则可以跳过

```java
public class UserDaoImpl implements UserDao {
    //创建sqlSessionFactory
    private SqlSessionFactory ssf;
    public  UserDaoImpl(SqlSessionFactory ssf) {
        this.ssf=ssf;
    }
    //id查询用户
    @Override
    public User getUserByID(int id) {
        //生产sqlSession
        SqlSession ss = ssf.openSession();
        //这里的明文建议设置为变量or常量
        User user = ss.selectOne("UserMapper.selectUserByID", id);
        return user;
    }
}
```

### 6.Mapper动态代理

#### 四大原则

1.接口方法名需要与mapper.xml的要调用的sql语句一致

```xml
<select id="selectUserByID">
```

2.接口的形参类型需要与mapper.xml parameterType 一致

```xml
<select parameterType="String">
```

3.接口的返回值类型需要与mapper.xml resultType一致

```xml
<insert parameterType="com.bean.User">
```

4.mapper.xml 中的namespace要与接口和包名一致

```xml
<mapper namespace="mapper.UserMapper">
```

设置好相应的接口

```java
package mapper;
import com.bean.User;
public interface UserMapper {
    //查询
    //记住要按照XML文件配置包名，类名，方法名，参数类型，返回值类型
    public User selectUserByID(int id);

}
```

然后会自动动态代理，就可以直接使用了 不需要实现接口

```java
UserMapper mapper=ssf.openSession().getMapper(UserMapper.class);
User user=mapper.selectUserByID(3);
```

当bean（User）对象字段与数据表字段不匹配时，就要用resultMap进行映射【就是说代码里变量名叫ID，而数据库里叫u_id】两个名字不同，就要手动进行映射。**多表关联**的时候也要使用

```xml
<!--Type还是要返回的类型【这里就算是List也没关系。他会自动加到列表中】 id与下面的resulMap要统一，自定义-->
    <resultMap  type="com.bean.User" id="user">
<!--  这边就算映射关系如果类的变量名和数据库的变量名不一致就需要用这个方法进行手动映射-->
        <result property="id" column="id"></result>
    </resultMap>
    <select id="selectAllUsers" resultMap="user">
        select * from  user
    </select>
```
