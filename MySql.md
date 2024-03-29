# MySql



## 常用语句

参考示例表：

user      字段：id（int） name（Varchar）isMan（tinyint）age（int） c_id(int)  time（datetime）

country 字段：c_id（int） c_name（Varchar）c_capital(Varchar)

### 查询语句

指定查询某个表的某个列的某个ID

```sql
select * from user where id = '%ID%'
```

模糊查询某个表的某个列的某个名字（有相符字符的都会列出来）

```sql
SELECT * FROM user WHERE name LIKE "%"#{name}"%"
```

查询多个ID数据(如果不存在的ID他则不会返回，只会返回存在的ID数据)

```sql
SELECT * FROM `user`  WHERE id IN (2,3,4)
```

表内一共有多少列数据

```sql
select COUNT(*) from user
```

查询出来的语句按顺序排序

```sql
select * from user ORDER BY c_id
```

#### 多表查询

查询表内信息以及相关表的关联信息 【表后是代替符（用u代替user）】

```sql
SELECT * FROM `user` u LEFT JOIN country c ON u.c_id =c.c_id
```

信息太多可进行筛选显示 把*【表示查询所有】替换掉即可

```sql
SELECT u.`name`,c.c_name  FROM `user` u LEFT JOIN country c ON  u.c_id =c.c_id
```

#### 条件查询

"="表示要完全符合，like表示模糊查询，只要有存在就行，添加条件就用AND

```sql
SELECT * FROM `user` u WHERE u.isMan=1 AND u.`name` LIKE '%1%' AND u.c_id=1
```

### 插入语句

完整插入 **如果id为自增主键，可以设置成NUll或者0，MySQL会自动处理**

```sql
insert into user value (#{id},#{name},#{isMan},#{age},#{time})
```

不完整插入 只插入想要的值，其他的值为表中设置的默认值

```mysql
insert into user (name,isMan) value (#{name},#{isman})
```

### 更新语句

通过ID更新Name

```sql
update user  set name =#{name} where ID =#{id}
```

### 删除语句

通过ID删除

```sql
delete from user where id=#{id}
```



### 字符串拼接和占位符

```sql
#{占位符}	    //尽量使用占位符 但是是以'占位符的形式传入数据库' 
//以占位符的形式获得字符串拼接的效果 示例： "%"#{name}"%"
$%字符串拼接%  //容易被SQL注入
```

