# MySQL进阶

## MySQL体系结构

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
|存储限制|64TB|有|有|
|事务安全|支持|-|-|
|锁机制|行锁|表锁|表锁|
|B+Tree索引|支持|支持|支持|
|Hash索引|-|-|支持|
|全文索引|支持（5.6版本之后）|支持|-|
|空间使用|高|低|N/A|
|内存使用|高|低|中等|
|批量插入速度|低|高|高|
|支持外键|支持|-|-|

## 存储引擎选择

在选择存储引擎时，应该根据应用系统的特点选择合适的存储引擎。对于复杂的应用系统，还可以根据实际情况选择多种存储引擎进行组合。

- `InnoDB`：是 `MySQL` 的默认存储引擎，支持事务、外键。如果应用对事务的完整性有比较高的要求，在并发条件下要求数据的一致性，数据操作除了插入和查询之外，还包含很多的更新、删除操作，那么 `InnoDB` 存储引擎是比较合适的选择。
- `MyISAM`：如果应用是以读操作和插入操作为主，只有很少的更新和删除操作，并且对事务的完整性、并发性要求不是很高，那么选择这个存储引擎是非常合适的。
- `MEMORY`：将所有的数据保存在内存中，访问速度快，通常用于临时表及缓存。`MEMORY` 的缺陷就是对表的大小有限制，太大的表无法缓存在内存中，而且无法保证数据的安全性。

## 索引

### 索引概述

索引（`index`）是帮助 `MySQL` 高效获取数据的数据结构（有序）。在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据，这样就可以在这些数据结构上实现高级查找算法，这种数据结构就是索引。

#### 索引优缺点

|优势|劣势|
|-|-|
|提高数据检索的效率|降低数据库的 `IO` 成本|索引也要占据存储空间|
|通过索引列对数据进行排序，降低数据排序的成本，降低 `CPU` 的消耗|索引提高了查询效率，同时也降低了更新表的速度，如对表进行 `INSERT`、`UPDATE`、`DELETE` 操作时效率降低|

### 索引结构

`MySQL` 的索引是在存储引擎层实现的，不同的存储引擎有不同的结构，主要包含以下几种：

|索引结构|描述|
|-|-|
|`B+Tree` 索引|最常见的索引类型，大部分引擎都支持B+树索引|
|`Hash` 索引|底层数据结构是用哈希表实现的，只有精确匹配索引列的查询才有效，不支持范围查询|
|`R-Tree`（空间索引）|空间索引是 `MyISAM` 引擎的一个特殊索引类型，主要用于地理空间数据类型，通常使用较少|
|`Full-TEXT`（全文索引）|是一种通过建立倒排索引，快速匹配文档的方式。类似于 `Lucene`，`Solr`，`ES`|

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
|`B+Tree`|支持|支持|支持|
|`Hash`|不支持|不支持|支持|
|`R-Tree`|不支持|支持|不支持|
|`Full-TEXT` |5.6版本后支持|支持|不支持|

#### 为什么 InnoDB 存储引擎选择使用 B+Tree 索引结构

- 相对于二叉树，层级更少，搜索效率高
- 对于 `B-Tree` 来说，无论是叶子节点还是非叶子节点，都会保存数据，这样导致一页中存储的键值减少，指针跟着减少，要同样保存大量数据，只能增加树的高度，导致性能降低
- 相对 `Hash` 索引，`B+Tree` 支持范围匹配及排序操作

### 索引分类

|分类|含义|特点|关键字|
|-|-|-|-|
|主键索引|针对于表中主键创建的索引|默认自动创建，只能有一个|`PRIMARY`|
|唯一索引|避免同一个表中某数据列中的值重复|可以有多个|`UNIQUE`|
|常规索引|快速定位特定数据|可以有多个||
|全文索引|全文索引查找的是文本中的关键词，而不是比较索引中的值|可以有多个|`FULLTEXT`|

在 `InnoDB` 存储引擎中，根据索引的存储形式，又可以分为以下两种：
|分类|含义|特点|
|-|-|-|
|聚集索引(`Clustered Index`)|将数据存储与索引放到一块，索引结构的叶子节点保存了行数据|必须有且仅有一个|
|二级索引(`Secondary Index`)|将数据与索引分开存储，索引结构的叶子节点关联的是对应的主键|可以存在多个|

#### 聚集索引的选取规则

- 如果存在主键，主键索引就是聚集索引
- 如果不存在主键，将使用第一个唯一（`UNIQUE`）索引作为聚集索引
- 如果表没有主键，或没有合适的唯一索引，则 `InnoDB` 会自动生成一个 `rowid` 作为隐藏的聚集索引

> 回表查询指的是先走二级索引找到对应的主键值，在根据主键值在到聚集索引当中拿到这一行的行数据

### 索引语法

- 创建索引
  
  ```sql
    CREATE [UNIQUE|FULLTEXT] INDEX 索引名称 ON 表名 (字段名,...);
  ```

- 查看索引
- 删除索引

### SQL性能分析

### 索引使用

### 索引设计原则
