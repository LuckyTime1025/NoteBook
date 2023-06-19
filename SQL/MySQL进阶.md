# MySQL 进阶

## MySQL 体系结构

- 连接层  
    最上层是一些客户端和连接服务，主要完成一些类似于连接处理、授权认证、及相关的安全方案。服务器也会为安全接入的每个客户端验证它所具有的操作权限。
- 服务层  
    第二层架构主要完成大多数的核心服务功能，如 `SQL` 接口，并完成缓存的查询，`SQL` 的分析和优化，部分内置函数的执行。所有存储引擎的功能也在这一层实现，如过程、函数等。
- 引擎层  
    存储引擎真正的负责 `MySQL` 中的数据存储和提取，服务器通过 `API` 和存储引擎进行通信。不同的存储引擎具有不同的功能，这样我们可以根据自己的需要，来选取合适的存储引擎。
- 存储层  
    主要是将数据存储在文件系统之上，并完成与存储引擎的交互。

## 存储引擎简介

|Engine|Support|Comment|Transactions|XA|Savepoints|
| - | - | - | - | - | - |
|CSV|YES|Stores tables as CSV files|NO|NO|NO|
|MRG_MyISAM|YES|Collection of identical MyISAM tables|NO|NO|NO|
|MEMORY|YES| Hash based, stored in memory, useful for temporary tables|NO|NO|NO|
|Aria|YES|Crash-safe tables with MyISAM heritage. Used for internal temporary tables and privilege tables|NO|NO|NO|
|MyISAM|YES|Non-transactional engine with good performance and small data footprint| NO|NO|NO|
|SEQUENCE|YES|Generated tables filled with sequential values|YES|NO|YES|
|InnoDB|DEFAULT|Supports transactions, row-level locking, foreign keys and encryption for tables|YES|YES|YES|
|PERFORMANCE_SCHEMA|YES|Performance Schema|NO|NO|NO|

> 存储引擎就是存储数据、建立索引、更新/查询数据等技术的实现方式。存储引擎是基于表的，而不是基于库的，所以存储引擎也可被称为表类型

### 在创建表时，指定存储引擎

```sql
CREATE TABLE 字段名 (
    字段 字段类型 [COMMENT 字段描述]
)ENGINE = INNODB [COMMENT 表描述];
```

### 查看当前数据库支持的存储引擎

```sql
SHOW ENGINES;
```

## 存储引擎特点

### InnoDB

- 介绍
  `InnoDB` 是一种兼顾高可靠性和高性能的通用存储引擎，在 `MySQL5.5` 之后，`InnoDB` 是默认的 `MySQL` 存储引擎
- 特点
  - `DML` 操作遵循 `ACID`模型，支持事务
  - 行级锁，提高并发访问性能
  - 支持外键 `FOREIGN KEY` 约束，保证数据的完整性和正确性
- 文件
  `InnoDB` 引擎的每张表都会对应一个 `表明.idb` 的表空间文件，存储该表的表结构（frm、sdi）、数据和索引。参数：`innodb_file_per_table`
- 逻辑结构

  ```mermaid
    flowchart  LR
        subgraph TableSpece
            Segment
        end
        subgraph Segment
            Extent
        end
        subgraph Extent
            Page
        end
        subgraph Page
            Row
        end
  ```

### MyISAM

- 介绍
  - `MyISAM` 是 `MySQL` 早期的默认存储引擎
- 特点
  - 不支持事务
  - 支持表锁，不支持行锁
  - 访问速度快
- 文件
  - 表名.sdi：存储表结构信息
  - 表名.MYD：存储数据
  - 表名.MYI：存储索引

### Memory

- 介绍
  - `Memory` 引擎的表数据是存储在内存中的，由于受到硬件、或断电等因素的影响，只能将这些表作为临时表或缓存使用
- 特点
  - 内存存放
  - `hash` 索引（默认）
- 文件
  - `表名.sdi`：存储表结构信息

|特点|InnoDB|MyISAM|Memory|
| :-: | :-: | :-: | :-: |
|存储限制|64TB|有 | 有 |
|事务安全 | 支持|-|-|
|锁机制 | 行锁 | 表锁 | 表锁 |
|B+Tree 索引 | 支持 | 支持 | 支持 |
|Hash 索引|-|-|支持 |
|全文索引 | 支持（5.6 版本之后）| 支持|-|
|空间使用 | 高 | 低|N/A|
|内存使用 | 高 | 低 | 中等 |
|批量插入速度 | 低 | 高 | 高 |
|支持外键 | 支持|-|-|

## 存储引擎选择

在选择存储引擎时，应该根据应用系统的特点选择合适的存储引擎。对于复杂的应用系统，还可以根据实际情况选择多种存储引擎进行组合。

- `InnoDB`：是 `MySQL` 的默认存储引擎，支持事务、外键。如果应用对事务的完整性有比较高的要求，在并发条件下要求数据的一致性，数据操作除了插入和查询之外，还包含很多的更新、删除操作，那么 `InnoDB` 存储引擎是比较合适的选择。
- `MyISAM`：如果应用是以读操作和插入操作为主，只有很少的更新和删除操作，并且对事务的完整性、并发性要求不是很高，那么选择这个存储引擎是非常合适的。
- `MEMORY`：将所有的数据保存在内存中，访问速度快，通常用于临时表及缓存。`MEMORY` 的缺陷就是对表的大小有限制，太大的表无法缓存在内存中，而且无法保证数据的安全性。

## 索引

### 索引概述

索引（`index`）是帮助 `MySQL` 高效获取数据的数据结构（有序）。在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据，这样就可以在这些数据结构上实现高级查找算法，这种数据结构就是索引。

#### 索引优缺点

|优势 | 劣势 |
|-|-|
|提高数据检索的效率 | 降低数据库的 `IO` 成本 | 索引也要占据存储空间 |
|通过索引列对数据进行排序，降低数据排序的成本，降低 `CPU` 的消耗 | 索引提高了查询效率，同时也降低了更新表的速度，如对表进行 `INSERT`、`UPDATE`、`DELETE` 操作时效率降低|

### 索引结构

`MySQL` 的索引是在存储引擎层实现的，不同的存储引擎有不同的结构，主要包含以下几种：

|索引结构 | 描述 |
|-|-|
|`B+Tree` 索引 | 最常见的索引类型，大部分引擎都支持 B+树索引 |
|`Hash` 索引 | 底层数据结构是用哈希表实现的，只有精确匹配索引列的查询才有效，不支持范围查询 |
|`R-Tree`（空间索引）| 空间索引是 `MyISAM` 引擎的一个特殊索引类型，主要用于地理空间数据类型，通常使用较少 |
|`Full-TEXT`（全文索引）| 是一种通过建立倒排索引，快速匹配文档的方式。类似于 `Lucene`，`Solr`，`ES`|

#### B+Tree 索引结构

> `Mysql` 索引数据结构对经典的 `B+Tree` 进行了优化。在原 `B+Tree` 的基础上，增加了一个指向相邻叶子节点的链表指针，就形成了带有顺序指针的 `B+Tree`  

相对于 `B-Tree` 的区别：

- 所有的数据都会出现在叶子节点
- 叶子节点形成一个单向链表

#### Hash 索引结构

- `Hash` 索引特点
  1. `Hash` 索引只能用于对等比较（=,in），不支持范围查询（between,>,<,...）
  2. 无法利用索引完成排序操作
  3. 查询效率高，通常只需要一次检索就可以了，效率通常要高于 `B+Tree` 索引
- 存储引擎支持
  - 在 `MySQL` 中，支持 `Hash` 索引的是 `Memory` 引擎，而 `InnoDB` 中具有自适应 `Hash` 功能，`Hash` 索引是存储引擎根据 `B+Tree` 索引在指定条件下自动构建的。

#### 不同存储结构对索引结构的支持情况

|索引|InnoDB|MyISAM|Memory|
|-|-|-|-|
|`B+Tree`|支持 | 支持 | 支持 |
|`Hash`|不支持 | 不支持 | 支持 |
|`R-Tree`|不支持 | 支持 | 不支持 |
|`Full-TEXT` |5.6 版本后支持 | 支持 | 不支持|

#### 为什么 InnoDB 存储引擎选择使用 B+Tree 索引结构

- 相对于二叉树，层级更少，搜索效率高
- 对于 `B-Tree` 来说，无论是叶子节点还是非叶子节点，都会保存数据，这样导致一页中存储的键值减少，指针跟着减少，要同样保存大量数据，只能增加树的高度，导致性能降低
- 相对 `Hash` 索引，`B+Tree` 支持范围匹配及排序操作

### 索引分类

|分类 | 含义 | 特点 | 关键字 |
|-|-|-|-|
|主键索引 | 针对于表中主键创建的索引 | 默认自动创建，只能有一个|`PRIMARY`|
|唯一索引 | 避免同一个表中某数据列中的值重复 | 可以有多个|`UNIQUE`|
|常规索引 | 快速定位特定数据 | 可以有多个||
|全文索引 | 全文索引查找的是文本中的关键词，而不是比较索引中的值 | 可以有多个|`FULLTEXT`|

在 `InnoDB` 存储引擎中，根据索引的存储形式，又可以分为以下两种：
|分类 | 含义 | 特点 |
|-|-|-|
|聚集索引 (`Clustered Index`)|将数据存储与索引放到一块，索引结构的叶子节点保存了行数据 | 必须有且仅有一个 |
|二级索引 (`Secondary Index`)|将数据与索引分开存储，索引结构的叶子节点关联的是对应的主键 | 可以存在多个|

#### 聚集索引的选取规则

- 如果存在主键，主键索引就是聚集索引
- 如果不存在主键，将使用第一个唯一（`UNIQUE`）索引作为聚集索引
- 如果表没有主键，或没有合适的唯一索引，则 `InnoDB` 会自动生成一个 `rowid` 作为隐藏的聚集索引

> 回表查询指的是先走二级索引找到对应的主键值，在根据主键值在到聚集索引当中拿到这一行的行数据

### 索引语法

- 创建索引
  
  ```sql
    CREATE [UNIQUE|FULLTEXT] INDEX 索引名称 ON 表名 (字段名，...);
  ```

  > 如果不加 `UNIQUE` 或 `FULLTEXT`，代表是一个常规索引  
    一个索引可以关联多个字段，如果一个索引只关联了一个字段，称之为单列索引，如果一个索引关联了多个字段，称之为联合索引或者组合索引

- 查看索引

  ```sql
  SHOW INDEX FROM 表名[\G];
  ```

- 删除索引

  ```sql
  DROP INDEX 索引名 ON 表名;
  ```

### SQL 性能分析

#### 执行频率

`MySQL` 客户端连接成功后，通过 `SHOW [SESSION | GLOBAL] STATUS` 命令可以提供服务器状态信息。通过如下命令，可以查看当前数据库的 `INSERT`、`UPDATE`、`DELETE`、`SELECT` 的访问频次：

```sql
SHOW GLOBAL STATUS LIKE 'Com_______';
```

#### 慢查询日志

慢查询日志记录了所有执行时间超过指定参数（long_query_time，单位：秒，默认 10 秒）的所有 `SQL` 语句日志。`MySQL` 的慢查询日志默认没有开启，需要在 `MySQL` 的配置文件（`/etc/my/cnf`）中配置如下信息

```conf
# 开启 MySQL 慢查询日志
slow_query_log=1

# 设置慢查询时间为 2 秒，SQL 语句执行时间超过 2 秒，就会被视为慢查询，记录慢查询日志
long_query_time=2
```

配置完毕后，通过一下指令重启 `MySQL` 服务器进行测试，查看慢日志文件中记录的信息 `/var/lib/mysql/localhost-slow.log`

> 查看慢查询日志是否开启
>
>  ```sql
>  SHOW VARIABLES LIKE 'slow_query_log';
>  ```

#### profile 详情

`SHOW PROFILES` 能够在做 `SQL` 优化时帮助我们了解时间都耗费到哪里去了。通过 `have_profiling` 参数，能够看到当前 `MySQL` 是否支持 `PROFILE` 操作：

```sql
SELECT @@have_profile;
```

默认 `profiling` 是关闭的，可以通过 `SET` 语句在 `SESSION` 或 `GlOBAL` 级别开启 `profiling`：

```sql
SET profiling = 1;
```

> 通过 `SELECT @@PROFILING;` 查看 `profiling` 是否开启。

执行一系列的业务 `SQL` 的操作，然后通过如下指令查看 `SQL` 语句的执行耗时：

```sql
-- 查看每一条 SQL 的耗时基本情况
SHOW PROFILES;

-- 查看指定 query_id 的 SQL 语句各个阶段的耗时情况
SHOW PROFILE FOR QUERY query_id;

-- 查看指定 query_id 的 SQL 语句 CPU 的使用情况
SHOW PROFILE CPU FOR query_id;
```

#### explain 执行计划

`EXPLAIN` 或者 `DESC` 命令获取 `MySQL` 如何执行 `SELECT` 语句的信息，包括在 `SELECT` 语句执行过程中表如何连接和连接的顺序。

```sql
EXPLAIN SELECT 字段列表 FROM 表名 WHERE 条件;
```

- EXPLAIN 执行计划各字段含义
  - `id`： `SELECT` 查询的序列号，表示查询中执行 `SELECT` 子句或者是操作表的顺序（`id` 相同，执行顺序从上到下；`id` 不同，值越大，越先执行）
  - `select_type`：表示 `SELECT` 的类型，常见的取值有 `SIMPLE`（简单表，即不使用表连接或者子查询）、`PRIMARY`（主查询，即外层的查询）`UNION`（`UNION` 中的第二个或者后面的查询语句）、`SUBQUERY`（`SELECT` 或 `WHERE` 之后包含了子查询）等
  - `type`：表示连接类型，性能由好到差的连接类型为 `NULL`、`system`、`const`、`eq_ref`、`ref`、`range`、`index`、`ALL`
  - `possible_key`：显示可能应用在这张表上的索引，一个或多个
  - `key`：实际使用的索引，如果为 `NULL`，则没有使用索引。
  - `key_len`：表示索引中使用的字节数，该值为索引字段最大可能长度，并非实际使用长度，在不损失精确性的前提下，长度越短越好
  - `rows`：`MySQL` 认为必须要执行查询的次数，在 `InnoDB` 引擎的表中，是一个估值，可能并不总是准确的。
  - `filtered`：表示返回结果的行数占需读取行数的百分比，filtered 的值越大越好
  - `Extra`：额外信息。
    > `using index condition`：查找使用了索引，但是需要回表查询数据  
    > `using where`;`using index`：查找使用了索引，但是需要的数据都在索引列中能找到，所以不需要回表查询数据

### 索引使用

#### 最左前缀法则

- 如果索引了多列（联合索引），要遵受最左前缀法则。最左前缀法则指的是查询从索引的最左列开始，并且不跳过索引中的列
- 如果查询时跳过了某一列，索引将部分失效（后面的字段索引失效）

#### 索引列运算

- 不要在索引列上进行运算操作，否则索引将会失效

```sql
EXPLAIN SELECT * FROM table_name WHERE SUBSTRING(phone,10,2) = '15;
```
  
- 字符串类型字段使用时，不加引号，索引将失效
- 模糊查询：如果仅仅是尾部模糊匹配，索引不会失效。如果是头部模糊匹配，索引失效
- `or` 连接条件：用 `or` 分割开的条件，如果 `or` 前的条件中的列有索引，而后面的列中没有索引，那么涉及到的索引都不会被用到
- 数据分布影响：如果 `MySQL` 评估使用索引比全表更慢，则不使用索引

#### SQL 提示

- `SQL` 提示是优化数据库的一个重要手段，简单来说，就是在 `SQL` 语句中加入一些人为的提示来达到优化操作的目的。
  - `USE INDEX`：建议使用指定的索引
  - `ignore INDEX`：不要使用指定的索引
  - `Force INDEX`：必须使用指定索引

#### 覆盖索引

尽量使用覆盖索引（查询使用了索引，并且需要返回的列，在该索引中已经全部能找到），减少 `SELECT`

#### 前缀索引

当字段类型为字符串为（`varchar`，`text` 等）时，有时候需要索引很长的字符串，这会让索引变得很大，查询时，浪费大量的磁盘 `IO`，影响查询效率。此时可以只将字符串的一部分前缀，建立索引，这样可以大大节约索引空间，从而提高索引效率

- 语法

  ```sql
  CREATE INDEX 索引名 on 表名 (column(字符数));
  ```

- 前缀长度
  可以根据索引的选择性来决定，而选择性是指不重复的索引值（基数）和数据表的记录总数的比值，索引选择性越高则查询效率越高，唯一索引的选择是 1，这是最好的索引选择性，性能也是最好的。

  ```sql
  SELECT COUNT(DISTINCT 字段名) / count(*) FROM 表名;
  SELECT COUNT(DISTINCT SUBSTRING(字段名，1,5)) / COUNT(*) FROM 表名;
  ```

- 单列索引与联合索引
  - 单列索引：即一个索引只包含单个列
  - 联合索引：即一个索引包含了多个列
  - 在业务场景中，如果存在多个查询条件，考虑针对于查询字段建立索引时，建议建立联合索引，而非单列索引。

> 多条件联合查询时，`MySQL` 优化器会评估哪个字段的索引效率更高，会选择该索引完成本次查询

### 索引设计原则

1. 针对数据量较大，且查询指教频繁的表建立索引
2. 针对于常作为查询条件（`WHERE`）、排序（`ORDER BY`）、分组（`GROUP BY`）操作的字段建立索引
3. 尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高
4. 如果是字符串类型的字段，字段的长度较长，可以针对于字段的特点，建立前缀索引。
5. 尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率
6. 要控制索引的数量，索引并不是多多益善，索引越多，维护索引结构的代价也就越大，会影响增删改的效率
7. 如果索引列不能存储 `NULL` 值，请在创建表时使用 `NOT NULL` 约束它。当优化器知道每列是否包含 `NULL` 值时，它可以更好的确定哪个索引最有效的用于查询

## SQL 优化

### 插入数据优化

- 批量插入 (500 到 1000 条数据)
- 手动提交事务
- 主键顺序插入

> 大批量插入数据
>
>- 如果一次性需要插入大批量的数据，使用 `insert` 语句插入性能较低，此时可以使用 `MySQL` 数据库提供的 `load` 指令进行插入。操作如下
>
>```shell
># 客户端连接服务端时，加上参数 --local-infile
>mysql --local-infile -uroot -p
># 设置全局参数 local_infile 为 1，开启从本地加载文件导入数据的开关
>set global local——infile=1;
># 执行 load 指令将准备好的数据，加载到表结构中
>load data local infile '文件路径' into table `表名` fields terminated by '分隔符' lines terminated by '结束符';
>```

## 主键优化

在 `InnoDB` 存储引擎中，表数据都是根据主键顺序组织存放的，这种存储方式的表称为索引组织表（index organized table IOT）。

### 页分裂

页可以为空，也可以填充一半，也可以填充 100%。每个页包含了 2-N 行数据（如果一行数据过大，会行溢出），根据主键排列

### 页合并

当删除一行记录时，实际上并没有被物理删除，只是记录被标记为（flaged）为删除并且它的空间变得允许被其他记录声明使用  
当页中删除的记录达到 `MERGE——THRESHOLD` （默认为页的 50%），`InnoDB` 会开始寻找最靠近的页（前或后）看看是否可以将两个页合并以优化空间

### 主键设计原则

1. 满足业务需求的情况下，尽量降低主键的长度
2. 插入数据时，尽量选择顺序插入，选择使用 `AUTO_INCREMENT` 自增主键
3. 尽量不要使用 `UUID` 做主键或者是使用其他自然主键，如身份证号
4. 业务操作时，避免对主键的修改
