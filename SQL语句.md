## Sql语句

### SELECT 查询

语法： 关键字h大小写都可以

```sql
-- 这是注释

-- 从 FROM 指定的表中，查询出所有数据。* 表示所有列
SELECT * FROM 表名称

-- 从 FROM 指定的表中，查询出指定列名称的数据
SELECT 列名称 FROM 表名称

select username, password from users 
```

### INSERT 插入

语法：

```sql
insert into 表名(元素，元素)  ('待添加的元素'，'待添加的元素')
```

```sql
-- 向uers这个表中的 username，password 属性添加元素
insert into users(username, password) values('tony stark', '098123')
```

### Update 更新

注意：要对应字符类型，字符类型一定要加 ' '

```sql 
UPDATE 表名称 SET 列名称=新值 WHERE 列名称=某值

UPDATE users SET username='chenbobo' WHERE username='admin'

UPDATE users SET password='admin123', status=1 WHERE id=1
```

### DELETE 删除

```sql 
DELETE FROM users WHERE id=4
```

### WHERE 语句

用于匹配语句：

```sql
-- 在 person 表中匹配 age<20 的目标
SELECT * FROM person WHERE age<20
```

注：

​	不等于的表示方法： <> 

​	也可以使用 !=

### AND / OR 联结词

AND 相当于 js 中的 &&

OR 相当于 js 中的 ||

```sql
select * from person where age < 20 and id>1
select * from person where age < 20 or name=chen
```

### ORDER BY 排序

升序排序：

​	对于 `user` 标准中的数据，按照 `status` 进行升序排序

```sql 
-- 一下这两条语句等价
select * from users order by status
select * from users order by status ASC
```

降序排序：

​	使用`desc`就是降序排序

```sql
select * from users order by id desc
```

多重排序：

​	按照状态进行降序排序，再按照用户名首字母进行升序排序

```sql
select * from users order by status desc,username asc
```

### COUNT(*) 总条数

返回表中的总数：

```sql
select count(*) from users where status=0
```

![image-20220327195829580](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20220327195829580.png)

使用 AS 关键字其别名

```sql
select count(*) as total from users where status=0
```

![image-20220327195939693](C:\Users\chen2\AppData\Roaming\Typora\typora-user-images\image-20220327195939693.png)

