# Java

注意事项：导入包（将包复制进lib目录后）之后，记得添加为库。这样才能引用。

**一、命名规范 **https://www.cnblogs.com/zshibo/p/8007123.html)

1、 项目名全部小写

2、 包名全部小写

3、 类名首字母大写，如果类名由多个单词组成，每个单词的首字母都要大写。

如：public class MyFirstClass{}

4、 变量名、方法名首字母小写，如果名称由多个单词组成，每个单词的首字母都要大写。

如：int index=0;

​    public void toString(){}

5、 常量名全部大写

如：public static final String GAME_COLOR=”RED”;

6、所有命名规则必须遵循以下规则：

1)、名称只能由字母、数字、下划线、$符号组成

2)、不能以数字开头

3)、名称不能使用JAVA中的关键字。

4)、坚决不允许出现中文及拼音命名。

**二、注释规范**

**1、**  **类注释**

在每个类前面必须加上类注释，注释模板如下：

/**

\* Copyright (C), 2006-2010, ChengDu Lovo info. Co., Ltd.

\* FileName: Test.java

\* 类的详细说明

*

\* @author 类创建者姓名
  \* @Date  创建日期

\* @version 1.00

*/

 

**2、**  **属性注释**

在每个属性前面必须加上属性注释，注释模板如下：

/** 提示信息 */

private String strMsg = null;

 

**3、**  **方法注释**

在每个方法前面必须加上方法注释，注释模板如下：

/**

\* 类方法的详细使用说明

*

\* @param 参数1 参数1的使用说明

\* @return 返回结果的说明

\* @throws 异常类型.错误代码 注明从此类方法中抛出异常的说明

*/

**4、**  **构造方法注释**

在每个构造方法前面必须加上注释，注释模板如下：

/**

\* 构造方法的详细使用说明

*

\* @param 参数1 参数1的使用说明

\* @throws 异常类型.错误代码 注明从此类方法中抛出异常的说明

*/

 

**5、**  **方法内部注释**

在方法内部使用单行或者多行注释，该注释根据实际情况添加。

如：//背景颜色

​    Color bgColor = Color.RED

## 二 ，Java的代码不同处

比较字符串的时候，不应该使用‘==’ 而是使用Equals方法

```java
if(user.getPassword()==(password))
{
	//这是典型错误
}
if(user.getPassword().equals(password))
{
	//这是正确比较字符串的方式，如果要判断否，就在前面加一个!即可
}
```

三，IDEA快捷键

```java
//代码最后面.var回车或Alt+Enter自动补全返回值
List<User> users = userMapper.selectByExample(userExample);
```

sout 快速打出System.out.println（）

serr 快速打出System.err.println（）