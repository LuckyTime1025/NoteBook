# Hive SQL

## DDL(Data Definition Language)

### 数据库

#### 创建数据库

```sql
CREATE DATABASE [IF NOT EXISTS] database_name
[COMMENT database_comment]
[LOCATION hdfs_path]
[WITH DBPROPERTIES (property_name=property_value, ...)];
```

#### 查询数据库

```sql
SHOW DATABASE [LIKE 字符串];
```

> `Like` 表达式说明：* 表示任意个字符，| 表示或的关系

#### 查看数据库信息

```sql
DESCRIBE DATABASE [EXTENDED] 数据库名;
```

#### 修改数据库

```sql
--修改数据库属性
ALTER DATABASE 数据库名 SET DBPROPERTIES (属性名=属性值, ...);

--修改数据位置
ALTER DATABASE 数据库名 SET LOCATION 路径;

--修改数据库所有者
ALTER DATABASE 数据库名 SET OWNER USER 用户名;

```

> 修改数据库位置，不会改变当前已有表的路径信息，而只是改变后续创建的新表的默认的父目录。

#### 删除数据库

```sql
DROP DATABASE [IF EXISTS] 数据库名 [RESTRICT|CASCADE];
```

> `RESTRICT`：严格模式，若数据库不为空，则会删除失败，默认为该模式。  
> `CASCADE`：级联模式，若数据库不为空，则会将库中的表一并删除。

#### 切换数据库

```sql
USE 数据库名;
```

### 表

#### 创建表

```sql
CREATE [TEMPORARY] [EXTERNAL] TABLE [IF NOT EXISTS] [数据库名.]表名
[(字段名 数据类型 [COMMENT 描述], ...)]
[COMMENT 表描述]
[PARTITIONED BY (字段名 数据类型 [COMMENT 描述], ...)]
[CLUSTERED BY (字段名, 字段名, ...) 
[SORTED BY (字段名 [ASC|DESC], ...)] INTO num_buckets BUCKETS]
[ROW FORMAT row_format] 
[STORED AS file_format]
[LOCATION 路径]
[TBLPROPERTIES (属性=属性值, ...)]
```

1. `TEMPORARY`：临时表，该表只在当前会话可见，会话结束，表会被删除。  
2. `EXTERNAL`（重点）：外部表，与之相对应的是内部表（管理表）。管理表意味着 Hive 会完全接管该表，包括元数据和 HDFS 中的数据。而外部表则意味着 Hive 只接管元数据，而不完全接管 HDFS 中的数据。  
3. `PARTITIONED BY`（重点）:创建分区表  
4. `CLUSTERED BY ... SORTED BY...INTO ... BUCKETS`（重点）：创建分桶表
5. `ROW FORMAT`（重点）:指定 `SERDE`，`SERDE` 是 `Serializer and Deserializer`的简写。`Hive` 使用 `SERDE` 序列化和反序列化每行数据。
6. `STORED AS`（重点）：指定文件格式，常用的文件格式有，textfile（默认值），`sequence file`，`orc file`、`parquet file`等等。
7. `LOCATION`：指定表所对应的 HDFS 路径，若不指定路径，其默认值为：`${hive.metastore.warehouse.dir}/db_name.db/table_name`
8. `TBLPROPERTIES`：用于配置表的一些 KV 键值对参数

- 详情可参考 [Hive-Serde](https://cwiki.apache.org/confluence/display/Hive/DeveloperGuide#DeveloperGuide-HiveSerDe)。
语法说明如下：  
  *语法一*：`DELIMITED` 关键字表示对文件中的每个字段按照特定分割符进行分割，其会使用默认的 `SERDE` 对每行数据进行序列化和反序列化

```sql
ROW FORAMT DELIMITED 
[FIELDS TERMINATED BY char] 
[COLLECTION ITEMS TERMINATED BY char] 
[MAP KEYS TERMINATED BY char] 
[LINES TERMINATED BY char] 
[NULL DEFINED AS char]
```
  
- FIELDS TERMINATED BY：列分隔符
- COLLECTION ITEMS TERMINATED BY：map、struct 和 array 中每个元素之间的分隔符
- MAP KEYS TERMINATED BY：map 中的 key 与 value 的分隔符
- LINES TERMINATED BY：行分隔符

  *语法二*：`SERDE` 关键字可用于指定其他内置的 SERDE 或者用户自定义的 `SERDE`。例如 `JSON`、`SERDE`，可用于处理 `JSON` 字符串。

```sql
ROW FORMAT SERDE serde_name [WITH SERDEPROPERTIES (属性名=属性值, ...)]
```

##### 基本数据类型

|数据类型 | 说明 | 定义 |
|-|-|-|
|`tinyint`|`1byte` 有符号整数||
|`smallint`|`2byte` 有符号整数||
|`int`|`4byte` 有符号整数||
|`bigint`|`8byte` 有符号整数||
|`boolean`|布尔类型，`true` 或者 `false`||
|`float`|单精度浮点数||
|`double`|双精度浮点数||
|`decimal`|十进制精准数字类型|`decimal(16,2)`|
|`varchar`|字符序列，需指定最大长度，最大长度的范围是[1,65535]|`varchar(32)`|
|`string`|字符串，无需指定最大长度||
|`timestamp`|时间类型||
|`binary`|二进制数据||

##### 复杂数据类型

| 类型 | 说明 | 定义 | 取值 |
|-|-|-|-|
|`array`|数组是一组相同类型的值的集合|`array<string>`|`arr[0]`|
|`map`|`map` 是一组相同类型的键 - 值对集合|`map<string, int>`|`map['key']`|
|`struct`|结构体由多个属性组成，每个属性都有自己的属性名和数据类型|`struct<id:int, name:string>`|`struct.id`|

##### 类型转换

`Hive` 的基本数据类型可以做类型转换，转换的方式包括隐式转换以及显示转换。

###### 隐式转换

1. 任何整数类型都可以隐式地转换为一个范围更广的类型，如 `tinyint` 可以转换成 `int`，`int` 可以转换成 `bigint`。
2. 所有整数类型、`float` 和 `string` 类型都可以隐式地转换成`double`。
3. `tinyint`、`smallint`、`int`都可以转换为`float`。
4. `boolean` 类型不可以转换为任何其它的类型。

>详情可参考 `Hive` 官方说明：[Allowed Implicit Conversions](https://cwiki.apache.org/confluence/display/hive/languagemanual+types#LanguageManualTypes-AllowedImplicitConversions)  
>
###### 显示转换

可以借助 `cast` 函数完成显示的类型转换

```sql
cast(expr as <type>)
```

#### Create Table As Select（CTAS）建表

> 该语法允许用户利用 `select` 查询语句返回的结果，直接建表，表的结构和查询语句的结构保持一致，且保证包含 `select` 查询语句放回的内容。

```sql
CREATE [TEMPORARY] TABLE [IF NOT EXISTS] 表名 
[COMMENT 表描述] 
[ROW FORMAT row_format] 
[STORED AS file_format] 
[LOCATION 路径]
[TBLPROPERTIES (属性名=属性值, ...)]
[AS select_statement]
```

#### Create Table Like 语法

> 该语法允许用户复刻一张已经存在的表结构，与上述的 `CTAS` 语法不同，该语法创建出来的表中不包含数据。

```sql
CREATE [TEMPORARY] [EXTERNAL] TABLE [IF NOT EXISTS] [db_name.]table_name
[LIKE exist_table_name]
[ROW FORMAT row_format] 
[STORED AS file_format] 
[LOCATION 路径]
[TBLPROPERTIES (属性名=属性值, ...)]
```

#### 内部表与外部表

##### 内部表

> `Hive` 中默认创建的表都是的内部表，有时也被称为管理表。对于内部表，Hive 会完全管理表的元数据和数据文件。

```sql
CREATE TABLE IF NOT EXISTS student(
    id int, 
    name string
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
LOCATION '/user/hive/warehouse/student';
```

准备如下文件 `student.txt`

``` test
1001 student1
1002 student2
1003 student3
1004 student4
1005 student5
1006 student6
1007 student7
1008 student8
1009 student9
1010 student10
1011 student11
1012 student12
1013 student13
1014 student14
1015 student15
1016 student16
```

上传到 Hive 表指定的路径

```shell
hadoop fs -put student.txt  /user/hive/warehouse/student
```

##### 外部表

> 外部表通常可用于处理其他工具上传的数据文件，对于外部表，Hive 只负责管理元数据，不负责管理 HDFS 中的数据文件。

```sql
create external table if not exists student(
    id int, 
    name string
)
row format delimited fields terminated by ' '
location '/user/hive/warehouse/student';
```

上传文件到 Hive 表指定的路径

```shell
hadoop fs -put student.txt /user/hive/warehouse/student
```

#### SERDE 和复杂数据类型

如下格式的 JSON 文件需要由 Hive 进行分析处理，请考虑如何设计表？
> 注：以下内容为格式化之后的结果，文件中每行数据为一个完整的 JSON 字符串。

```json
{
    "name": "dasongsong",
    "friends": [
        "bingbing",
        "lili"
    ],
    "students": {
        "xiaohaihai": 18,
        "xiaoyangyang": 16
    },
    "address": {
        "street": "hui long guan",
        "city": "beijing",
        "postal_code": 10010
    }
}
```

可以考虑使用专门负责 JSON 文件的 JSON Serde，设计表字段时，表的字段与 JSON 字符串中的一级字段保持一致，对于具有嵌套结构的 JSON 字符串，考虑使用合适复杂数据类型保存其内容。最终设计出的表结构如下：

```sql
create table teacher
(
    name     string,
    friends  array<string>,
    students map<string,int>,
    address  struct<city:string,street:string,postal_code:int>
)
row format serde 'org.apache.hadoop.hive.serde2.JsonSerDe'
location '/user/hive/warehouse/teacher';
```

创建该表，并准备以下文件 `teacher.txt`。
> 注意：需要确保文件中每行数据都是一个完整的 JSON 字符串，JSON SERDE 才能正确的处理。

```json
{"name":"dasongsong","friends":["bingbing","lili"],"students":{"xiaohaihai":18,"xiaoyangyang":16},"address":{"street":"hui long guan","city":"beijing","postal_code":10010}}
```

上传文件到 Hive 表指定的路径

```shell
hadoop fs -put teacher.txt /user/hive/warehouse/teacher
```

##### create table as select 和 create table like

###### create table as select

```sql
create table teacher1 as select * from teacher;
```

###### create table like

```sql
create table teacher2 like teacher;
```

#### 查看表

##### 展示所有表

```sql
SHOW TABLES [IN 数据库名] LIKE ['字符'];
```

> like 通配表达式说明：*表示任意个任意字符，|表示或的关系。

##### 查看表信息

```sql
DESCRIBE [EXTENDED | FORMATTED] [数据库名.]表名
```

- EXTENDED：展示详细信息
- FORMATTED：对详细信息进行格式化的展示

#### 修改表

##### 重命名表

```sql
ALTER TABLE 表名 RENAME TO 新表名
```

##### 增加列

该语句允许用户增加新的列，新增列的位置位于末尾。

```sql
ALTER TABLE 表名 ADD COLUMNS (字段名 数据类型 [COMMENT 描述], ...)
```

##### 更新列

该语句允许用户修改指定列的列名、数据类型、注释信息以及在表中的位置。

```sql
ALTER TABLE table_name CHANGE [COLUMN] 旧字段名 新字段名 数据类型 [COMMENT 描述] [FIRST|AFTER 字段名]
```

##### 替换列

该语句允许用户用新的列集替换表中原有的全部列。

```sql
ALTER TABLE 表名 REPLACE COLUMNS (字段名 数据类型 [COMMENT 描述], ...)
```

#### 删除表

```sql
DROP TABLE [IF EXISTS] 表名;
```

#### 清空表

```sql
TRUNCATE [TABLE] 表名;
```

##### Error

###### Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask

- 详细信息

```error
FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. MetaException(message:Got exception: org.apache.hadoop.fs.FileAlreadyExistsException Path is not a directory: /user/hive/warehouse/student
        at org.apache.hadoop.hdfs.server.namenode.FSDirMkdirOp.mkdirs(FSDirMkdirOp.java:55)
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.mkdirs(FSNamesystem.java:3438)
        at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.mkdirs(NameNodeRpcServer.java:1166)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.mkdirs(ClientNamenodeProtocolServerSideTranslatorPB.java:742)
        at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
        at org.apache.hadoop.ipc.ProtobufRpcEngine2$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine2.java:621)
        at org.apache.hadoop.ipc.ProtobufRpcEngine2$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine2.java:589)
        at org.apache.hadoop.ipc.ProtobufRpcEngine2$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine2.java:573)
        at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:1213)
        at org.apache.hadoop.ipc.Server$RpcCall.run(Server.java:1089)
        at org.apache.hadoop.ipc.Server$RpcCall.run(Server.java:1012)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:422)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1899)
        at org.apache.hadoop.ipc.Server$Handler.run(Server.java:3026)
)
```

- 原因

Hive 尝试创建一个名为"/user/hive/warehouse/student"的目录时失败了，因为该路径已经存在，并且是一个文件而不是目录。

- 解决方法

```shell
hdfs dfs -rm -r /user/hive/warehouse/student
```

## DML(Data Manipulation Language)

## DQL(Data Query Language)

## DCL(Data Control Language)
