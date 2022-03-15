# SpringBoot

#### 再也不用写配置文件啦

#### 建议使用[Spring Initializr](https://start.spring.io/)建立项目

### 1.配置文件

基础的配置还是要配置的

Application.properties

```properties
#静态数据css js image 请放在static中  页面请放在templates中

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

#Thymeleaf的编码
spring.thymeleaf.encoding=utf-8

#热部署静态文件
spring.thymeleaf.cache=false

#使用HTML5标准
spring.thymeleaf.mode=HTML5#静态数据css js image 请放在static中  页面请放在templates中

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
//代表它是持久层，可被自动装配，防止报错
@Repository
public interface UserMapper {

//    @方式写sql
    @Select("select * from User")
    List<User> selectAllUsers(); //直接就可以用了

    @Select("select * from user where id =#{id}")
    User selectUserByID(int id);

    @Select("Select * from user where name=#{username} and password=#{password}")
    User selectUserByNameAndPassword(String username,String password);

    //Mapper无法重载 在Service层重载吧
//   @Insert("insert into user (name,password) value (#{name},#{password})")
//   void  addUser(String name,String password);

    @Insert("insert into user (name,password) value (#{username},#{password})")
    int  addUser(User user);//返回值int  代表影响了多少行的数据

    //有些需求可能需要根据不同的情况输出不同的Sql语句 需要使用一个方法来返回对应的Sql语句
    //参数 类名 类中的方法名 返回一个String类型的sql语句 user也会被当成参数传过去
//    @InsertProvider(type = Provider.class,method = "saveUser")
//    void  addUser(User user);
}
```

### 2.Controller

控制类，可以进行业务的操作和网页的跳转  **注意事项：Url大小写敏感** 

```java
@Controller
public class UserController {
    @Autowired
    private UserService userService;
    @RequestMapping("/register")
    public String register(User user, String repassword, Model model){
        //检验两次密码是否相同（这个应该在前端就检测完毕，不应该传到后端，这里只是测试）
        if (repassword.equals(user.getPassword())){
            //跳转至相应的成功页面
            boolean isSuccess=userService.insertUser(user);
            //偷懒写法 正规的还是用if
            model.addAttribute("SuccessMsg","数据库插入成功");
            model.addAttribute("errorMsg","数据库插入失败");
            return isSuccess? "login-success":"error";
        }
        else {
            //传递一些数据给前端
            model.addAttribute("errorMsg","不一致");
            //这里应该弹窗显示错误，不应该是跳转错误页面
            return  "error";
        }
    }
    @RequestMapping("/login")
    public String login(String username,String password){
        System.out.println(username);
        System.out.println(password);
        if (userService.loginUser(username,password)==null){
            return  "error";
        }
        else {
            return  "login-success";
        }
    }
}
```

Controller一定要和前端对应！

```htaccess
<div class="materialContainer">
//表单方式对应
		<form th:action="@{~/login}" method="post">
			<div class="box">
				<div class="title">登录</div>
				<div class="input">
					<label for="name">用户名</label>
					<input type="text" name="username" required="required" id="name">
					<span class="spin"></span>
				</div>
				<div class="input">
					<label for="pass">密码</label>
					<input type="password" name="password" required="required" id="pass">
					<span class="spin"></span>
				</div>
				<div class="button login">
					<button type="submit">
						<span>登录</span>
						<i class="fa fa-check"></i>
					</button>
				</div>
				<a href="javascript:" class="pass-forgot">忘记密码？</a>
			</div>
		</form>
		
		<div class="overbox">
			<form th:action="@{~/register}" method="post">
				<div class="material-button alt-2">
					<span class="shape"></span>
				</div>
				<div class="title">注册</div>
				<div class="input">
					<label for="regname">用户名</label>
					<input type="text" name="username" required="required" id="regname">
					<span class="spin"></span>
				</div>
				<div class="input">
					<label for="regpass">密码</label>
					<input type="password" name="password" required="required" id="regpass">
					<span class="spin"></span>
				</div>
				<div class="input">
					<label for="reregpass">确认密码</label>
					<input type="password" name="repassword" required="required" id="reregpass">
					<span class="spin"></span>
				</div>
				<div class="button">
					<button type="submit">
						<span>注册</span>
					</button>
				</div>
			</form>
		</div>
```



### 3.Service

```java
@Service
public class UserServiceImpl implements UserService {

    @Autowired
    private UserMapper userMapper;

    //如果数据库中有重复数据（账户密码一致） 则会返回List User会报null
    //应该拆分登录步骤，注册也是一样，先得保证账户不重复  这边就不做测试了
    @Override
    public User loginUser(String name, String password) {
        return userMapper.selectUserByNameAndPassword(name,password);
    }

    @Override
    public void insertUser(String name, String password) {
//        userMapper.addUser(name,password);
    }

    @Override
    public boolean insertUser(User user) {
        int rows=userMapper.addUser(user);
        return rows>0?true:false;
    }
}
```

