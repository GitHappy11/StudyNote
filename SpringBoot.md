# SpringBoot

#### 再也不用写配置文件啦

#### 建议使用[Spring Initializr](https://start.spring.io/)建立项目

### 1.配置文件

基础的配置还是要配置的

Application.properties

```properties
#数据库配置 5.0
#spring.datasource.url=jdbc:mysql://localhost:3306/test?useSSL=false
#spring.datasource.username=root
#spring.datasource.password=
#spring.datasource.driver-class-name=com.mysql.jdbc.Driver

#数据库配置 8.0
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/test?useUnicode=true&characterEncoding=UTF-8&userSSL=false&serverTimezone=GMT%2B8
spring.datasource.username=root
spring.datasource.password=
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```

### 1.主类

加载配置文件，启动也是这个启动，这边改了默认主类，启动的时候也记得改一下启动器的默认主类

```java
@SpringBootApplication
public class Init {
    public static void main(String[] args) {
        SpringApplication.run(Init.class,args);
        System.out.println("Init SpringBoot Project....");
    }
}

```

### 1.Mapper

三合一文件不需要配置Mapper包 不需要Mapper.xml 直接在接口里一气呵成  User类就不说了

```java
//说明它是Mapper
@Mapper
public interface UserMapper {
//    @方式写sql
    @Select("select * from User")
    List<User> findAll(); //直接就可以用了
}
```

### 2.Controller

控制类，可以进行业务的操作和网页的跳转

```java
@Controller
public class TestController {

    //自动装配 直接就可以使用了
    @Autowired
    private UserMapper userMapper;
    //url
    @RequestMapping("/findAll")
    public  String findAll(){
        System.out.println("Welcome");
        List<User> userList=userMapper.findAll();
        for (User user:userList
             ) {
            System.out.println(user.getName());
        }
        return "";
    }
}
```

