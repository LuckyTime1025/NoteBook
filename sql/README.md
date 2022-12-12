# SQL学习笔记
## 数据类型
#### 数值类型
|类型|大小|范围(有符号)|范围(无符号)|用途|
|----|----|----|----|----|
|TINYINT|1 Bytes|(-128，127)|(0，255)|小整数值|
|SMALLINT|2 Bytes|(-32 768，32 767)|(0，65 535)|大整数值|
|MEDIUMINT|3 Bytes|(-8 388 608，8 388 607)|(0，16 777 215)|大整数值|
|INT或INTEGER|4 Bytes|(-2 147 483 648，2 147 483 647)|(0，4 294 967 295)|大整数值|
|BIGINT|8 Bytes|(-9,223,372,036,854,775,808，9 223 372 036 854 775 807)|(0，18 446 744 073 709 551 615)|极大整数值|
|FLOAT|4 Bytes|(-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402 823 466 351 E+38)|0，(1.175 494 351 E-38，3.402 823 466 E+38)|单精度浮点数值|
|DOUBLE|8 Bytes|(-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308)|0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308)|双精度浮点数值|
|DECIMAL|对DECIMAL(M,D) ，如果M>D，为M+2否则为D+2|依赖于M和D的值|依赖于M和D的值|小数值|
#### 日期类型
|类型|大小(bytes)|范围|格式|用途|
|---|---|---|---|---|
|DATE|3|1000-01-01/9999-12-31|YYYY-MM-DD|日期值|
|TIME|3|'-838:59:59'/'838:59:59'|HH:MM:SS|时间值或持续时间|
|YEAR|1|1901/2155|YYYY|年份值|
|DATETIME|8|'1000-01-01 00:00:00' 到 '9999-12-31 23:59:59'|YYYY-MM-DD hh:mm:ss|混合日期和时间值|
|TIMESTAMP|4|'1970-01-01 00:00:01' UTC 到 '2038-01-19 03:14:07' UTC结束时间是第 2147483647 秒，北京时间 2038-1-19 11:14:07，格林尼治时间 2038年1月19日 凌晨 03:14:07|YYYY-MM-DD hh:mm:ss|混合日期和时间值，时间戳|
#### 字符类型
|类型|大小|用途|
|-|-|-|
|CHAR|0-255 bytes|定长字符串|
|VARCHAR|0-65535 bytes|变长字符串|
|TINYBLOB|0-255 bytes|不超过 255 个字符的二进制字符串|
|TINYTEXT|0-255 bytes|短文本字符串|
|BLOB|0-65 535 bytes|二进制形式的长文本数据|
|TEXT|0-65 535 bytes|长文本数据|
|MEDIUMBLOB|0-16 777 215 bytes|二进制形式的中等长度文本数据|
|MEDIUMTEXT|0-16 777 215 bytes|中等长度文本数据|
|LONGBLOB|0-4 294 967 295 bytes|二进制形式的极大文本数据|
|LONGTEXT|0-4 294 967 295 bytes|极大文本数据|
## MySQL-DDL
#### 创建数据库
```sql
CREATE DATABASE IF NOT EXISTS 数据库名;
```
#### 创建数据表
```sql
CREATE TABLE 表名 (
  字段 字段类型 [约束条件] [COMMENT '注释'],
  字段 字段类型 [约束条件] [COMMENT '注释']
)[COMMENT='注释'];
```
#### 复制
```sql
CREATE TABLE 表名 LIKE 被复制的表名;
```
#### 选择数据库
```sql
USE 表名;
```
#### 查看当前数据库
```sql
SELECT DATABASE();
```
#### 查看所有数据库
```sql
SHOW DATABASES;
```
#### 查看所有数据表
```sql
SHOW TABLES;
```
#### 查看表结构
```sql
DESC 表名;
```
#### 查看建表语句
```sql
SHOW CREATE TABLE 表名;
```
#### 删除数据表
```sql
DROP TABLE 表名;
```
#### 删除并创建
```sql
TRUNCATE TABLE 表名;
```
#### 删除数据库
```sql
DROP DATABASE IF EXISTS 数据库名;
```
### 表中字段操作
#### 添加字段
```sql
ALTER TABLE 表名 ADD 新字段名 数据类型 [约束条件] [位置];
```
#### 修改字段类型
```sql
ALTER TABLE 表名 MODIFY 字段名 数据类型;
```
#### 修改字段名称及类型
```sql
ALTER TABLE 表名 CHANGE 字段名 新字段名 数据类型;
```
#### 删除字段
```sql
ALTER TABLE 表名 DROP 字段名;
```
#### 修改表名
```sql
ALTER TABLE 表名 RENAME TO 新表名;
```
#### 修改表注释
```sql
ALTER TABLE 表名 COMMENT="注释";
```
## MySQL-DML
#### 添加数据
```sql
INSERT INTO 表名 (字段名1,字段名2, …) VALUES (值1,值2),(值1,值2);
INSERT INTO 表名 VALUES (值1,值2),(值1,值2);
```
#### 修改数据
```sql
UPDATE 表名 SET 字段名1 = 值1, 字段名2 = 值2, … [WHERE 条件];
```
#### 删除数据
```sql
DELETE FROM 表名 [WHERE 条件];
```
## MySQL-DQL
### 运算符
|比较运算符|功能|
|-|-|
|>|大于|
|>=|大于等于|
|<|小于|
|<=|小于等于|
|=|等于|
|<> 或 !=|不等于|
|BETWEEN…AND…|在某个范围之内（含最小、最大值|
|IN(…)|在 IN 之后的列表中的值，多选一|
|LIKE 占位符|模糊匹配（_匹配单个字符，%匹配任意个字符|
|IS NULL|是NULL|
---
|逻辑运算符|功能|
|-|-|
|AND 或 &&|并且(多个条件同时成立)|
|OR 或 \|\||或者(多个条件任意一个成立)|
|NOT 或 !|非，不是|
### 查询数据
#### 查询所有字段
```sql
SELECT * FROM 表名;
```
#### 查询字段并设置别名
```sql
SELECT 字段名1 AS 别名1,字段名2 AS 别名2,… FROM 表名; 
```
#### 去除重复数据
```sql
SELECT DISTINCT 字段名 FROM 表名;
```
#### 查询条件
```sql
SELECT 字段列表 FROM 表名 WHERE 条件;
```
#### 分组查询
```sql
SELECT 字段列表 FROM 表名 [WHERE 条件] GROUP BY 分组字段名 [HAVING 分组后过滤条件];
```
>WHERE 和 HAVING 的区别：
>- 执行时机不同：WHERE 是分组之前进行过滤，不满足WHERE条件，不参与分组；而 HAVING 是分组之后对结果进行过滤。
>- 判断条件不同：WHERE 不能对聚合函数进行判断，而 HAVING 可以。
#### 排序查询
##### 升序
```sql
SELECT 字段列表 FROM 表名 ORDER BY 字段1 ASC;
```
##### 降序
```sql
SELECT 字段列表 FROM 表名 ORDER BY 字段1 DESC;
```
#### 范围
```sql
SELECT 字段列表 FROM 表名 LIMIT 起始索引,查询记录数;
```
## MySQL-DCL
<table>
    <caption>密码策略</caption>
    <tr>
        <td>固定密码的总长度</td>
        <td>validate_password_length</td>
    </tr>
    <tr>
        <td>指定密码验证的文件路径</td>
    <td>validate_password_dictionary_file</td>
    </tr>
    <tr>
        <td>整个密码中至少要包含大/小写字母的总个数</td>
        <td>validate_password_mixed_case_count</td>
    </tr>
    <tr>
        <td>整个密码中至少要包含阿拉伯数字的个数</td>
        <td>validate_password_number_count</td>
    </tr>
    <tr>
        <td>指定密码的强度验证等级，默认为MEDIUM</td>
        <td>validate_password_policy</td>
    </tr>
</table>

> 0/LOW：只验证长度<br/>1/MEDIUM：验证长度、数字、大小写、特殊字符<br/>2/STRONG：验证长度、数字、大小写、特殊字符、字典文件<br/>

<table>
    <caption>权限</caption>
    <tr>
        <td>ALL,ALL PRIVILEGES</td>
        <td>所有权限</td>
    </tr>
        <tr>
        <td>SELECT</td>
        <td>查询数据</td>
    </tr>
        <tr>
        <td>INSERT</td>
        <td>插入数据</td>
    </tr>
    <tr>
        <td>UPDATE</td>
        <td>修改数据</td>
    </tr>
    <tr>
        <td>DELETE</td>
        <td>删除数据</td>
    </tr>
    <tr>
        <td>ALTER</td>
        <td>修改表</td>
    </tr>
    <tr>
        <td>DROP</td>
        <td>删除数据库/表/视图</td>
    </tr>
    <tr>
        <td>CREATE</td>
        <td>创建数据库/表</td>
    </tr>
</table>

### 创建用户
```sql
CREATE USER '用户名' @'主机名' IDENTIFIED BY '密码';
```
### 查询用户
```sql
SELECT * FROM user;
```
### 修改用户
```sql
ALTER　USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码';
```
### 删除用户
```sql
DROP USER '用户名'@'主机名';
```
### 查看权限
```sql
SHOW GRANTS FOR '用户名'@'主机名'
```
### 授予权限
```sql
GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';
```
### 撤销权限
```sql
REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';
```
### 查看密码策略
```sql
SHOW VARIABLES LIKE 'validate_password%';
```
### 设置密码策略
```sql
set global validate_password_policy=0;
```
### 设置密码长度
```sql
set global validate_password_length=6;
```
### 刷新权限
```sql
flush privileges;
```
## MySQL-函数
### 常见聚合函数
#### 统计数量
```sql
SELECT COUNT() FROM 表名;
```
#### 最大值
```sql
SELECT MAX() FROM 表名;
```
#### 最小值
```sql
SELECT MIN() FROM 表名;
```
#### 平均值
```sql
SELECT AVG() FROM 表名;
```
#### 求和
```sql
SELECT SUM() FROM 表名;
```
### 字符串函数
####  字符串拼接
```sql
CONCAT(S1,S2,…SN)
```
####  将字符串转换为小写
```sql
LOWER(STR)
```
####  将字符串转换为大写
```sql
UPPER(STR)
```
####  左填充（用字符串pad对str的左边进行填充，达到n个字符串长度）
```sql
LPAD(STR,N,PAD)
```
####  右填充（用字符串pad对str的右边进行填充，达到n个字符串长度）
```sql
RPAD(STR,N,PAD)
```
####  去掉字符串头部和尾部的空格
```sql
TRIM(STR)
```
####  返回从字符串start位置起的len个长度的字符串
```sql
SUBSTRING(STR,START,LEN)
```
### 日期函数
#### 返回当前日期
```sql
CURDATE()
```
#### 返回当前时间
```sql
CURTIME()
```
#### 返回当前日期和时间
```sql
NOW()
```
#### 获取指定date的年份
```sql
YEAR(date)
```
#### 获取指定date的月份
```sql
MONTH(date)
```
#### 获取指定date的日期
```sql
DAY(date)
```
#### 返回一个日期/时间值加上一个时间间隔expr后的时间值
```sql
DATE_ADD(date,INTERVAL expr type)
```
#### 返回起始时间date1和结束时间date2之间的天数
```sql
DATEDIFF(date1,date2)
```
### 流程函数
#### 如果value为true则返回true，否则返回false
```sql
IF(value,true,false)
```
#### 如果value1不为空，则返回value1，否则返回value2
```sql
IFNULL(value1,value2)
```
#### 如果val1为true，则返回res1，…否则返回default默认值
```sql
CASE WHEN [val1] THEN [res1] … ELSE [default] END
```
#### 如果expr的值等于val1，返回res1，…否则返回default默认值
```sql
CASE [expr] WHEN [val1] THEN [res1] …ELSE[default] END
```
## MySQL—约束
### 约束
|约束|描述|关键字|
|-|-|-|
|非空约束|限制该字段的数据不能为null|NOT NULL|
|唯一约束|保证该字段的所有数据都是唯一、不重复的|UNIQUE|
|主键约束|主键是一行数据的唯一标识，要求非空且唯一|PRIMARY KEY|
|默认约束|保存数据时，如果未指定该字段的值，则采用默认值|DEFAULT|
|检查约束|保证字段值满足一个条件|CHECK|
|外键约束|用来让两张表的数据之间建立连接，保证数据的一致性和完整性|FOREIGN KEY|
> 要实现主键自增要加上关键字 ```AUTO_INCREMENT```
### 外键约束
> 外键约束是用来保证数据的一致性和完整性

|行为|说明|
|-|-|
|NO ACTION|当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。|
|RESTRICT|当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。|
|CASCADE|当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则也删除/更新外键在子表中的记录。|
|SET NULL|当在父表中删除对应记录时，首先检查该记录是否有对应外键，如果有则设置子表格中该外键值为NULL（这就要求该外键允许取NULL）。|
|SET DEFAULT|父表有变更时，子表将外键列设置成一个默认的值（Innodb不支持）。|
#### 添加外键
```sql
CREATE TABLE 表名(
    字段名 数据类型,
    ...
    [CONSTRAINT] [外键名称] FOREIGN  KEY(外键字段名) REFERENCES 主表(主表列名)
);

ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY(外键字段名) REFERENCES 主表(主表列名) ON UPDATE 行为 ON DELETE 行为;
```
#### 删除外键
```sql
ALTER TABLE 表名 DROP FOREIGN KEY 外键名称;
```
## 多表查询
> 笛卡尔积：笛卡尔乘积是指在数学中，两个集合的所有组合情况。(在多表查询时，需要消除无效的笛卡尔积)
### 多表关系
项目开发中，在进行数据表结构设计时，会根据业务需求及业务模型之间的关系，分析并设计表结构，由于业务之间相互关联，所以各个表结构之间也存在着各种联系，基本上分为三种：
- 一对多：在多的一方设置外键，关联一的一方的主键
- 多对多：建立中间表，中间表包含两个外键，关联两张表的主键
- 一对一：用于表结构的拆分，在其中任何一方设置外键 (UNIQUE)，关联另一方的主键
### 连接查询
#### 内连接
> 相当于查询A、B交集部分数据
- 隐式内连接
```sql
SELECT 字段列表 FROM 表1,表2 WHERE 条件...;
```
- 显式内连接
```sql
SELECT 字段列表 FROM 表1 [INNER] JOIN 表2 ON 连接条件...;
```
#### 外连接
- 左外连接：查询左表所有数据，以及两张表交集部分数据
```sql
SELECT 字段列表 FROM 表1 LEFT [OUTER] JOIN 表2 ON 条件...;
```
- 右外连接：查询右表所有数据，以及两张表交集部分数据
```sql
SELECT 字段列表 FROM 表1 RIGHT [OUTER] JOIN 表2 ON 条件...;
```
- 自连接：当前表与自身的连接查询，自连接必须使用别名
> 自连查询，可以是内连接查询，也可以是外连接查询。
```sql
SELECT 字段列表 FROM 表A 别名A JOIN 表A 别名A ON 条件...;
```
#### 联合查询
> 把多次查询的结果合并起来,形成一个新的查询结果集<br/>
对于联合查询的多张表的列数必须保持一致，字段类型也需要保持一致
- ```UNION``` 合并后去重
- ```UNION ALL```直接合并
```sql
SELECT 字段列表 FROM 表A ...
UNION [ALL]
SELECT 字段列表 FROM 表B ...;
```
#### 子查询
> SQL语句中嵌套 ```SELECT``` 语句，称为嵌套查询，又称子查询<br/>
子查询外部的语句可以是 ```INSERT```/```UPDATE```/```DELETE```/```SELECT``` 的任何一个
```sql
SELECT * FROM t1 WHERE columnl = (SELECT column1 FORM t2)
```
- 根据子查询结果不同，分为：
    - 标量子查询（子查询结果为单个值）
    - 列子查询（子查询结果为一列）
    - 行子查询（子查询结果为一行）
    - 表子查询（子查询结果为多行多列）
- 根据子查询的位置，分为：
    - ```WHERE``` 之后
    - ```FROM``` 之后
    - ```SELECT``` 之后
## MySQL—事务
> 事务是一组操作的集合，它是一个不可分割的工作单位，事务会把所有的操作作为一个整体一起向系统提交或撤销操作请求，即这些操作要么同时成功要么同时失败。<br/>
默认的MySQL事务是自动提交的，也就是说，当执行一条DML语句，MySQL会立即隐式的提交事务
### 查看设置事务的提交方式
```sql
-- 查看事务提交方式
-- 1：自动提交
-- 0：手动提交
SELECT @@autocommit;

--方式 1
-- 设置事务为手动提交
SET @@autocommit = 0；

-- 方式 2
-- 开启事务
START TRANSACTION;
```
### 提交事务
```sql
COMMIT;
```
### 回滚事务
```sql
ROLLBACK;
```
### 开启事务
```sql
START 
```
### 事务的四大特性 (ACID)
- 原子性 （Atomicity）：事务是不可分割的最小操作单元，要么全部成功，要么全部失败
- 一致性 (Consistency)：事务完成时，必须使所有的数据都保持一致状态
- 隔离性 (Isolation)：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行
- 持久性 (Durability)：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的。
### 并发事务问题
|问题|描述|
|-|-|
|脏读|一个事务读到另外一个事务还没有提交的数据|
|不可重复读|一个事务先后读取同一条记录，但两次读取的数据不同，称之为不可重复读|
|幻读|一个事务按照条件查询数据时，没有对应的数据行，但在插入数据时，又发现这行数据已经存在|
### 事务隔离级别
|隔离级别|脏读|不可重复读|幻读|
|:-:|:-:|:-:|:-:|
|Read uncommitted|√|√|√|
|Read committed|×|√|√|
|Repeatable Read(默认)|×|×|√|
|Serializable|×|×|×|
> 隔离级别越高，数据安全性越高，但是性能越低
#### 查看事务隔离级别
```sql
SELECT @@TRANSACTION_ISOLATION;
```
#### 设置事务隔离级别
```sql
SET [SESSION|GLOBAL] TRANSACTION ISOLATION LEVEL [READ UNCOMMITTED|READ COMMITTED|REPEATABLE READ|SERIALIZABLE]
```
- SESSION ：当前客户端窗口有效
- GLOBAL ：针对所有客户端的窗口有效