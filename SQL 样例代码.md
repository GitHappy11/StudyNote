# SQL 样例代码

### SQL Lite 题目：根据一定条件整合数据更改相应列 

### 测试表：

![image-20220908164144270](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNoteimage-20220908164144270.png)   

需要Update的 列为  isSuccess

同一个人（name和card一致，两个条件都要） 的 money 总和 大于 100 则 isSuccess =1

或 type == 公共业务 则isSuccess = 1

不然 isSuccess =0

注意（可能有同名情况，但是card唯一）

最终结果应为

![image-20220908164206420](https://image-1258199940.cos.ap-shanghai.myqcloud.com/D:/StudyNoteimage-20220908164206420.png)

 

### 答案代码

```sqlite
-- 需要更新的表
UPDATE Test
-- 需要更新的字段和内容
SET isSuccess = '1' 
-- 设置执行条件
WHERE  -- where 筛选数据 

-- 把name和card拼接起来 相同的为一个整体数据
	card IN (  -- 使用in关键字设置多个整体数据
	SELECT
		card -- 分组标识头
	FROM
		Test 
	-- 拼接 name和card 对数据进行分组
	GROUP BY
	( `name` || card )
-- 设置执行条件
HAVING -- HAVING 挑选数据
-- 这段整体数据中 如果满足以下任意条件（OR） 则执行update内容。
	SUM(money) > 100)  -- money总值大于100
	OR type = '公共业务' -- type为公共业务  
```

