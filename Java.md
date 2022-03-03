# Java

注意事项：导入包（将包复制进lib目录后）之后，记得添加为库。这样才能引用。

**命名规范 ****https://www.cnblogs.com/zshibo/p/8007123.html)

## 一、独特类型

###### 列表

```java

```

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

int和integer  int的默认值是0，integer的默认值是null  根据具体需求使用。

函数中参数不能有默认值，需要其他方法实现，例如重载



## 三，IDEA快捷键

```java
//代码最后面.var回车或Alt+Enter自动补全返回值
List<User> users = userMapper.selectByExample(userExample);
```

sout 快速打出System.out.println（）

serr 快速打出System.err.println（）

### 好用的插件整理

## **Free Mybatis plugin**      

可以从dao接口跳转到[mybatis](https://so.csdn.net/so/search?q=mybatis&spm=1001.2101.3001.7020)的xml文件中，还能找到对应的方法，平时我们从controller的方法跳转到service都可以ctrl+点击跳转找到对应的方法，但是从dao到xml就没办法了，只能复制方法名，到对应的xml里查找，效率很低，这款插件就解决了这个问题。

### **阿里巴巴代码规约扫描**

一款阿里巴巴代码规范约束插件，对代码规范等很有帮助，可以养成良好的代码规范。

在idea中settings-->plugins，搜索alibaba回车，然后选择如图所示，点击installed（安装），然后等待下载安装好后重启idea即可。

[idea spring 中没有标识_IDEA 中几款好用的插件，分享给你！_齐妹爱了爱了的博客-CSDN博客](https://blog.csdn.net/weixin_35713375/article/details/112176580?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0.pc_relevant_default&spm=1001.2101.3001.4242.1&utm_relevant_index=2)

