---
layout: post
title: 数据库课本知识总结
date: 2018-5-12
categories: study
tags: 
  - 数据库
description: Database
---

# 第1章 数据库系统概述

数据库是数据的集合，用于描述一个或多个相关组织的活动。

**数据库管理系统(Database management system,  DBMS)**是辅助用户管理和利用大数据集的软件。

## 1.1 管理数据

- 数据库设计和应用开发
- 数据分析
- 并发性和鲁棒性
- 有效性和可伸缩性

其它课题：并行和分布数据库管理、因特网数据库、数据库仓库和决策支持的复杂查询、数据挖掘、对象数据库、空间数据库管理和面向规则的DBMS扩展。

## 1.2 历史回顾

第一个通用的DBMS于20世纪60年代早期在通用电器(General Electric)公司设计的，称为集成数据存储(Integrated Data Store)，它奠定了网状数据模型的基础。(网状数据模型由数据系统语言协会CODASYL标准化)

20世纪60年代末期，IBM开发出了信息管理系统(IMS)DBMS，其奠定了层次数据模型的基础。

1970年IBM的San Jose研究实验室推出关系数据模型的数据表达框架。

20世纪80年代，关系模型巩固了它作为主导DBMS模型的地位。作为IBM的System R项目的一部分而开发的关系数据库SQL查询语言，成为了标准查询语言。

随着企业资源规划(ERP)和管理资源规划(MRP)软件包的出现，DBMS之上出现了一层面向应用的特征。

## 1.3 文件系统和数据库管理系统

- 数据必须存储在诸如磁盘或者磁带的存储设备中，在需要时调入主存
- 必须保护数据的一致性，支持并发
- 确保系统崩溃时数据能恢复到一致的状态

## 1.4 数据库管理系统的优点

- 数据独立性：代码和数据细节分开
- 有效的数据存取
- 数据完整性和安全性：数据总是通过DBMS存取
- 数据管理：尽可能减少冗余
- 并发存储和故障恢复
- 减少应用程序的开发时间

DBMS不适用于具有严格实时约束的应用程序，或带有一些定义明确的关键操作并且为这些操作必须编写有效的客户代码的应用程序。并且，某些应用可能需要以查询语言不能支持的形式来操纵数据。

## 1.5 数据库管理系统中数据的描述和存储

**数据模型(data model)**是隐藏了许多低级存储细节的高级数据描述结构的集合。

**语义数据模型(semantic data model)**是一种更抽象的高级数据模型，它使用户更容易为企业数据提出一个好的初始描述。一个广泛使用的语义数据模型是实体-联系(ER)模型，它允许形象地定义实体及它们之间的联系。

`age`字段是一个拙劣设计的例子，因为它的值是不断变化的，应该使用`date of birth`，然后计算出`age`。

### 1.5.1 关系模型

关系模型的核心数据描述结构是关系(relation)，它可以被认为是**记录(record)**的集合。

基于数据模型的数据描述称为**模式(model)**，其说明了这个关系的名字、每个字段(属性或列)的名字及每个字段的类型。

```
Students(sid: string, name: string, login: string, age: integer, gpa: real)
```

完整性约束是关系中的记录必须满足的条件，比如，`sid`唯一性。

数据模型：关系数据模型、层次数据模型、网状数据模型、面向对象数据模型、对象-关系数据模型。

### 1.5.2 数据库管理系统的抽象级别

数据库描述由三级抽象模式组成：概念模式、物理模式、外模式。

**数据定义语言(Data Definition Language，DDL)**用于定义外模式和概念模式，SQL命令支持描述物理模式，但不是SQL-92语言标准的一部分。

- 概念模式(逻辑模式)以DBMS数据模型的形式描述存储的数据
- 物理模式描述存储细节
- 外模式常常称为DBMS的数据模型，允许为单个用户和用户组定制(和授权)数据存取

### 1.5.3 数据独立性

**逻辑数据独立性**：用户能够免于因数据逻辑结构变化或关系的存选择变化的影响。

**物理数据独立性**：概念模式把用户与数据物理存储的变化分开。

## 1.6 数据库管理系统中的查询

关系演算是基于数理逻辑形式的查询语言，关系代数是基于能操纵关系的操作集合的查询语言。

利用**数据操纵语言(Data Manipulation Language，DML)**，用户可以创建、修改和查询数据。如果DML和DDL嵌入到宿主语言(如C或者COBOL)，则称为**数据子语言**。

## 1.7 事务管理

### 1.7.1 事务的并发执行

DBMS的一个重要任务就是进行数据的并发存取调度。

**加锁协议(locking protocol)**是每一个事务需要遵守的规则集合，以确保即使有几个事务在交叉工作，但它们产生的结果等同于所有事务按照某种串行顺序执行的结果。锁是一种用来控制对数据库对象进行存取的机制，包含**共享锁(shared lock)**、**互斥锁(exclusive lock)**。

一个对象的共享锁可以由两个不同事务同时获取，而对象上的互斥锁则确保没有其他事务能获得该对象的任何锁。

### 1.7.2 未完成的事务和系统崩溃

DBMS要维护一个记录所有数据库写操作的日志，先写日志后操作，这叫做**先写日志(Write-Ahead Log, WAL)**特性。

通过周期性地、强制地将一些信息写入磁盘，可以减少系统崩溃恢复时所需要的时间，这个周期性操作称为**检查点(checkpoint)**。

## 1.8 数据库管理系统的结构

经过语法分析的查询被送至**查询优化器**，查询优化器借助数据存储的信息为计算查询生成高效的执行计划。**执行计划**是计算查询的蓝图，经常表示为关系操作树(带有使用何种存取方法等细节信息的注释)。

关系操作在计算查询中起到构造块的作用，其实现代码位于文件和存取方法层之上，这一层支持文件存储结构。DBMS中，文件是页或记录的集合。这一层通常是支持堆文件或无顺序页文件，也支持索引。文件和存取方法层代码位于缓冲区管理器之上，缓冲区管理器的责任是把页从磁盘取入主存以响应读请求。

DBMS的最底层处理存储着数据的磁盘空间管理，其上层通过这层(提供的例程)分配、回收、读和写页面，该层称为磁盘空间管理器。

与并发控制和恢复有关的DBMS构件：事务管理程序、锁管理器、恢复管理程序。

## 1.9 与数据库打交道的人

终端用户、数据库实现者、应用开发者、数据库管理员(DBA)。

DBA负责的任务：

- 概念和物理模式的设计
- 安全性和授权
- 数据可用性和故障恢复
- 数据库调整

# 第2章 实体联系模型

## 2.1 数据库设计与ER图

数据库设计的过程(ER模型与前3步关系最为密切)：

1. 需求分析
2. 概念数据库设计
3. 逻辑数据库设计
4. 模式的细化
5. 物理数据库设计
6. 应用与需求设计

## 2.2 实体、属性和实体集

实体是客观世界的一个对象，它可区别于其他对象。将相似的实体聚集在一起是很有用的，这种集合称为实体集。实体集之间是可以相交的。

实体是通过一组属性来描述的。在给定的实体集中，所有实体都具有相同的属性，对于属性的选择反映了希望描述实体信息的细节。对于每个于实体集相关的属性，必须确定该属性的值域。对于每个实体集，我们都选取一个码，码是一组最小的属性集合，它们的值能够唯一地确定实体集中的每个实体。有可能存在多个候选码，这时我们在其中指定一个作为主码。

## 2.3 联系和联系集

联系就是两个或多个实体之间的一种关联。与实体一样，我们希望把相似的联系放到一起，组成一个联系集。联系集具有描述性属性，用来记录有关联系自身的一些信息，而不是记录包含在该联系集中实体集的信息。

## 2.4 ER模型的其他特征

“每个部门最多只有一个经理”的约束，就是**码约束**的一个示例。

假设每个部门都要求有一个经理，那么这个要求就是一个**参与约束**的示例。

一个弱实体可以通过它的一些属性与其他实体的主码结合起来形成惟一标识，成为**识别属主**。

ISA的结构可以指定两种约束：交迭约束(overlap)、覆盖约束(covering)。交迭约束决定是否允许两个子集含有相同的实体；覆盖约束决定子类中的实体是否包括超类中的所有实体。

**聚合(aggregation)**允许一个联系集(由一个虚线框表示)参与另一个联系集。

# 第3章 关系模型

关系模型简单、精巧：数据库由一个或多个关系共同组成，每个关系是行和列的组成的表。

在数据模型中，需要提供一系列的结构来表达数据必须满足的约束条件，这些条件称为**完整性约束(Integrity Constraint, IC)**。

## 3.1 关系模型简介

关系模型中用于描述数据的主要结构是关系。一个关系由**关系模式(relation schema)**和**关系实例(relation instance)**共同组成。关系实例就是一个表，而关系模式则对表的每个列进行描述。

关系模式需要给出关系名，每个字段(称为列或者属性)的名称，以及每个字段的域。关系的**度(degree或arity)**是指字段的数目，关系实例的**基(cardinality)**是指该关系实例具有记录的数目。

创建关系Students：

```sql
CREATE TABLE Students(sid CHAR(20), name CHAR(30), login CHAR(20), age INTEGER, gpa REAL)
```

往Students表中插入一条记录：

```sql
INSERT INTO Students(sid, name, login, age, gpa) VALUES (12345678, 'Mega', 'mega@se', 20, 1.0)
```

对于`INTO`子句中的字段名列表，在字段值的次序正确的情况下，可以省略。

删除所有name为Mega的记录：

```sql
DELETE FROM Students S WHERE S.name = 'Mega'
```

修改已有数据的字段值：

```sql
UPDATE Students S SET S.gpa = S.gpa + 1, S.age = S.age + 1 WHERE S.sid = 12345678
```

## 3.2 关系的完整性约束

约束组成：取值范围约束、主码约束、外码约束、一般约束。

 能够惟一确定每条记录的关系的字段集合称为关系的**候选码(candidate key)**，通常简称为码。一个关系可以有若干个候选码。

用户可以根据**主码(primary key)**的值来访问一条记录。

码约束指定sid是主码，name和age组合成候选码：

```sql
CREATE TABLE Students(sid CHAR(20), name CHAR(30), login CHAR(20), age INTEGER, gpa REAL,
                      UNIQUE (name, age),
                      CONSTRAINT StudentsKey PRIMARY KEY(sid))
```

使用SQL指定外码约束：

```sql
CREATE TABLE Enrolled(sid CHAR(20), cid CHAR(20), grade CHAR(10),
                      PRIMARY KEY(sid, cid),
                      FOREIGN KEY(sid) REFERENCES Students)
```

## 3.3 完整性约束的强制执行

```sql
CREATE TABLE Enrolled(sid CHAR(20), cid CHAR(20), grade CHAR(10),
                      PRIMARY KEY(sid, cid),
                      FOREIGN KEY(sid) REFERENCES Students,
                      ON DELETE CASCADE,
                      ON UPDATE NO ACTION)
```

SQL允许一个约束被设定为推迟(`DEFERRED`)或者立即执行(`IMMEDIATE`)。

## 3.4 查询关系数据

```sql
SELECT * FROM Students S WHERE S.age < 18
SELECT S.name, S.login FROM Students S WHERE S.age < 18
SELECT S.name, E.cid FROM Students S, Enrolled E WHERE S.sid = E.sid AND E.grade = 'A'
```

## 3.5 逻辑数据库设计：从ER模型到关系模型

从实体集到关系表：

```sql
CREATE TABLE Employees(ssn CHAR(11), name CHAR(30), lot INTEGER,
                       PRIMARY KEY(ssn))
```

从联系集(不包括约束)到关系表：

```sql
CREATE TABLE Work_in(ssn CHAR(11), did INTEGER, address CHAR(20), since DATE,
                     PRIMARY KEY(ssn, did, address),
                     FOREIGN KEY(ssn) REFERENCES Employees,
                     FOREIGN KEY(address) REFERENCES Locations,
                     FOREIGN KEY(did) REFERENCES Departments)
```

当字段名和被引用的字段的名称不同时，需要明确给出被引用的字段名：

```sql
CREATE TABLE Report_to(supervisor_ssn CHAR(11), subordinate_ssn CHAR(11)
                       PRIMARY KEY(supervisor_ssn, subordinate_ssn),
                       FOREIGN KEY(supervisor_ssn) REFERENCES Employees(ssn),
                       FOREIGN KEY(subordinate_ssn) REFERENCES Employees(ssn))
```

部门和经理的关系实现，我们可以用联系集实现，也可以直接在部门里面存储经理信息。后者惟一的缺点是，当部门没有经理的时候会导致空间浪费。

SQL提供了表约束和断言功能。

## 3.6 视图简介

视图可以看成一个关系表，但是其中的记录没有直接存储在数据库中，而是在需要时根据视图定义计算出来。

```sql
CREATE VIEW B-Students(name, login, course)
  AS SELECT S.sname, S.sid, E.cid FROM Students S, Enrolled E
    WHERE S.sid = E.sid AND E.grade = 'B'
```

利用视图机制可以对关系模型中的逻辑数据独立性提供支持。

视图：**可更新视图(updatable views)**、**可插入视图(insertable-into views)**。

## 3.7 删除/修改关系表和视图

在没有视图或者完整性约束引用关系时，可删除表：

```sql
DROP TABLE Students RESTRICT
```

可以修改已有表结构，如下添加字段：

```sql
ALTER TABLE Students
  ADD COLUMN maiden-name CHAR(10)
```

# 第4章 关系代数和演算

## 4.1 预备知识

本书用位置表示法来形式化地定义关系代数和关系演算。

## 4.2 关系代数

代数查询是由操作符的集合组成，它的基本特征是代数中的每个操作符都有一个或两个关系实例作为输入参数，而返回的是一个关系实例。

关系代数包括从关系中选择行的操作符$\sigma$和投影列的操作符$\pi$。

- $\sigma$：选择行
- $\pi$：投影列
- $\cup$：并，$R \cup S$中R和S必须是并相容的，并且结果模式与R相同
- $\cap$：交，$R \cap S$中R和S必须是并相容的，并且结果模式与R相同，$R \cap S = R-(R-S)$
- $-$：差，$R-S$返回所有在R中出现但不在S中出现的元组
- $\times$：叉积，笛卡儿积
- $\rho$：重命名操作符，$\rho(R(\overline{F}),E)$表示将关系代数表达式E变换成新的关系实例R，其中$\overline{F}$是重命名列表
- $\bowtie$：连接，分为条件连接、相等连接、自然连接；$R\bowtie_cS=\sigma_c(R \times S)$；
- $/$：除，$A/B = \pi_x(A)-\pi_x((\pi_x(A) \times B)-A)$

## 4.3 关系演算

关系演算是关系代数的另一种形式。与关系代数相比，关系演算是 非过程化的，或是说明性的，用户只需要描述答案的集合，而不用考虑它们是如何计算出来的。

关系演算的变形时**元组关系演算(Tuple Relational Calculus，简称TRC)**，TRC的变量以关系的元组为值。在**域关系演算(Domain Relational Calculus，DRC)**的变形中，参与运算的变量以关系中的字段的取值范围为值。

**TRC查询的语法**：

令Rel时一个关系名字，R和S时元组变量，a是R的一个属性，b是S的一个属性。Op表示集合{$<,>,=,\le,\ge,\ne$}中的一个操作符。原子公式如下：

- $R \in Rel$
- R.a op S.b
- R.a op constant, or constant op R.a

公式可递归地定义成如下形式之一，其中p和q本身也是公式，p(R)表示包含变量R的公式：

- 任何原子公式
- $~p, p \land q, p \lor q, p \Rightarrow q$
- $\exists R(p(R)), R是元组变量$
- $\forall R(p(R)), R是元组变量$

**DRC中的一个原子公式如下**：

- $<x_1,x_2,...,x_n> \in Rel$，这里的Rel是有n个属性的关系，每个$x_i(1 \le i \le n)$是一个变量或者是常量
- X op Y
- X op 常量，或者常量 op X

公式可以递归定义：

- 任何原子公式
- $~p, p \land q, p \lor q, p \Rightarrow q$
- $\exists R(p(R)), R是域变量$
- $\forall R(p(R)), R是域变量$

## 4.4 代数与演算的表达能力

如果查询语言可以表达所有可以用关系代数表示的查询，就认为是**关系完整性**。

# 第5章 SQL：查询、约束与触发器

## 5.1 概述

- 数据操作语言(The Data Manipulation Language, DML)：SQL查询、插入、删除、修改
- 数据定义语言(The Data Definition Language, DDL)：表和视图的创建、删除、修改
- 触发器和高级完整性约束
- 嵌入式和动态SQL：从宿主语言(C或COBOL)中调用SQL代码
- 客户-服务器执行和远程数据库存取
- 事务管理
- 安全保证
- 高级特性：面向对象、递归查询、决策支持查询、数据挖掘、空间数据、文本和XML数据管理

## 5.2 基本SQL查询的形式

```sql
SELECT [DISTINCT] select_list
FROM from_list
WHERE qualification
```

每个查询都必须有一个SELECT子句和一个FROM子句，前者指定在结果中保留的列，后者说明表的叉积，可选的WHERE子句说明了在FROM子句指定的表上的选择条件。

只有当FROM语句中一个关系出现多次的时候，才需要引入范围变量(关键词`AS`)。

SQL支持一个针对字符集的**整序(collation)**的概念，或者称为排序顺序。

SQL对模式匹配的支持是通过`LIKE`操作符与通配符`%`(代表零个或者多个任意字符)和`_`(只代表一个任意字符)一起使用来实现的。

## 5.3 UNION、INTERSECT和EXCEPT

SQL提供了并(`UNION`)、交(`INTERSECT`)、差(`EXCEPT`)操作符，还提供了集合操作符`IN`、`op ANY`、`op ALL`(将一个值与给定集合中的元素用op比较)、`EXISTS`(检查一个集合是否为空)。`IN`和`EXISTS`可以有前缀`NOT`。

## 5.4 嵌套查询

嵌套查询时将其他查询嵌套在一个查询里面的查询，嵌套在查询中的查询称为子查询。

聚集函数：包括一些统计函数，如标准偏差、协方差、百分比等。

## 5.5 聚集操作符

- `COUNT ([DISTINCT] A)`：A列中所有(不同)值的数目
- `SUM ([DISTINCT] A)`：A列中所有(不同)值的总和
- `AVG ([DISTINCT] A)`：A列中所有(不同)值的平均数
- `MAX(A)`：A列中的最大值
- `MIN(A)`：A列中的最小值

如果在SELECT子句中使用聚集操作，那么就只能使用聚集操作符，除非查询包括GROUP BY子句！

```sql 
/* 扩展SQL查询 */
SELECT [DISTINCT] select_list
FROM from_list
WHERE qualification
GROUP BY grouping_list
HAVING group_qualification
```

`AS`可以用来改变输出列的列名，由聚集函数产生的输出列没有列名，因此用`AS`指定列名是非常有用的。

SQL:1999引入两个新的集合函数：`EVERY`和`ANY`。

## 5.6 空值

SQL提供了一个特殊的列值，称为**空值(null)**。当列值是未知的或者是不适用的时候，就使用null。

特殊的比较符`IS NULL`来检查一个列值是否为空。

AND、OR、NOT三值逻辑中表达式的求值结果是true、false、unknown。

```sql
NOT unknown = unknown

true OR unknown = true
false OR unknown = unknown

true AND unknown = unknown
```

通过在定义字段时指定`NOT NULL`可以达到禁止使用空值的目的，主码字段不允许为空值，因此`PRIMARY KEY`隐含了`NOT NULL`约束。

## 5.7 SQL中的复杂完整性约束

可以使用table constraints来指定单个表上的复杂约束，形式为`CHECK conditional-expression`。

用户可以使用带有CHECK约束的CREATE DOMAIN语句来定义新的域：

```sql
CREATE DOMAIN ratingval INTEGER DEFAULT 0
  CHECK (VALUE > 1 AND VALUE <= 10)
  
CREATE TYPE ratingtype AS INTEGER
```

断言：`ASSERTION`

## 5.8 触发器和主动数据库

触发器是一个过程，它对数据库的特定改变进行响应。具有关联触发器集的数据库称为主动数据库。

一个触发器的描述包含以下三个部分：

- 事件：激活触发器的数据库的改变
- 条件：当触发器被激活时运行的查询或检测
- 动作：当触发器被激活且条件为真时，DBMS要执行的过程

## 5.9 设计主动数据库

执行一个触发器的动作部分又触发同一个触发器，这样的触发器称为递归触发器。

------

# 第6章 数据库应用开发

## 6.1 从应用程序中访问数据库

SQL中可以被识别的数据类型，在宿主语言中可能不被识别，反之亦然。对于这种不匹配，必须做适当地转换数据值。面向集合的SQL，可以用游标来解决。

SQL语句可以引用在宿主程序中定义的变量，必须加上冒号`:`作为前缀，并在命令`EXEC SQL BEGIN DELARE SECTION`与`EXEC SQL END DELARE SECTION`之间声明。

SQL-92标准识别两种用来报告出错的特殊变量：`SQLCODE`和`SQLSTATE`。SQLCODE是比较老的一种，返回某个负数，但不能进一步说明某个负数代表哪个错误。SQLSTATE将一些预定义的值与一些常见错误相关联，引入一些报错的规范。`SQLCODE`对应`long`，`SQLSTATE`对应`char[6]`。

```c
EXEC SQL
INSERT INTO Sailors VALUES(:c_sname, :c_sid, :c_rating, :c_cage);
```

检查`SQLSTATE`：

```c
EXEC SQL WHENEVER [SQLERROR | NOT FOUND] [CONTINUE | GOTO stmt]
```

游标机制：解决**阻抗失配(impedance mismatch)**问题。

```c
OPEN sinfo;
FETCH sinfo INTO :c_sname, :c_age;
CLOSE sinfo
```

游标的声明：

```c
DECLARE cursorname [INSENSITIVE] [SCROLL] CURSOR
	[WITH HOLD]
	FOR some query
	[ORDER BY order_item_list]
	[FOR READ ONLY | FOR UPDATE]
```

## 6.2 JDBC简介

ODBC和JDBC，分别是**开发数据库连接(Open Database Connectivity)**和**Java数据库连接(Java Database Connectivity)**，它们通过应用编程接口将数据库的功能以标准的形式呈现给应用程序开发人员。

ODBC和JDBC通过引入一个额外的非直接层来实现可执行码级的可移植性，所有和某个特定的DBMS的直接交互都通过一个特定的DBMS驱动器。

JDBC的体系结构包括4个主要部件：应用程序、驱动器管理器、若干特定数据源的驱动器以及相应的数据源。

JDBC的驱动器按照应用程序和数据源之间的关系被分为以下4类：

- 桥
- 直接通过非Java驱动器将JDBC函数调用转化为数据源本身的API
- 网桥
- 直接通过Java驱动器将JDBC函数调用转化为数据源本身的API

## 6.3 JDBC类和接口

JDBC是一系列Java类与接口的集合，包括了连接远程数据源、执行SQL语句、检查SQL语句的执行结果集、事务管理以及异常处理等方法。JDBC类和接口是`java.sql`包的一部分，JDBC 2.0中还包括`javax.sql`包，即JDBC可选包。

JDBC中数据源驱动器由`DriverManager`类来管理，通过registerDriver、deregisterDriver、getDriver来动态添加删除驱动器。

载入JDBC驱动器：

```java
Class.forName("oracle/jdbc.driver.OracleDriver");
```

也可直接在命令行中`-Djdbc.driver=oracle/jdbc.driver`。

连接数据源：

```java
String url = "jdbc:oracle:www.bookstore.com:3083";
Connection connection;
try {
  connection = DriverManager.getConnection(url, userId, password);
} catch (SQLException e) {
  System.out.println(e.getMessage());
}
```

## 6.4 SQLJ

SQLJ(**SQL_Java**)是为了实现用静态的模型去动态生成JDBC中的查询而开发的，非常类似于嵌入式SQL。

与JDBC不同，其半静态的SQL查询允许编译器在编译时执行SQL语法检查，对宿主变量和对应的SQL属性的匹配进行强类型检查，以及查询和数据库模式的一致性检查——表、属性、视图和存储过程。

## 6.5 存储过程

直接在数据库上运行应用逻辑的好处在于，在数据库服务器和提交SQL语句的客户端之间，使大量的数据传输将得以最小化，同时也充分利用了数据库服务器的计算能力。

# 第7章 Internet应用

## 7.1 引言

## 7.2 Internet的一些概念

统一资源标识符URI是Internet上惟一标识资源的字符串，由以下三部分组成：

- 用于访问资源的协议
- 资源所在的主机
- 资源在其主机上的路径

超文本传输协议HTTP是Internet上最常见的通信协议，其一个变种是SSL(Secure Sockets Layer)协议。

**简单的HTTP请求消息**：

```
GET index.html HTTP/1.1
User-agent: Mozilla/4.0
Accept: text/html, image/gif, image/jpeg
```

第一行位请求行，有三个域，HTTP方法域、URI域、HTTP版本域；第二行为用户代理，表明客户的类型；第三行以Accept开头，表明客户愿意接受什么类型的文件。

**HTTP响应**：

```
HTTP/1.1 200 OK
Date: Mon, 04 Mar 2002 12:00:00 GMT
Content-Length: 1024
Content-Type: text/html
Last-Modified: Mon, 22 Jun 1998 09:23:24 GMT
<html>
  <head>
...
```

HTTP响应由三部分组成：一个状态行，若干消息标题行、消息体(包含客户端请求的对象)行。状态行有三个域，HTTP版本域、状态码、相关的服务器消息。

HTTP协议是无状态协议。

## 7.3 HTML文档

HTML是用于描述一个文档的简单语言，也称为标记语言。

## 7.4 XML文档

**可扩展标记语言(Extensible Markup Language，XML)**是用于解决HTML的缺点而开发的一种标记语言。

XML源于SGML和HTML两种技术的融合，**标准通用标记语言(Standard Generalized Markup Language，SGML)**是一种元语言，它可以用来定义数据和文档交换语言，例如HTML。有一种专用于XML的样式语言，称为**扩展样式语言(Extensible Style Language，XSL)**，是用来描述依附于一组标签的XML文档应该如何显示的一种标准方法。

- 元素：也称为标签，是XML文档的主要组成部分
- 属性：一个元素可以具有描述性属性，用来为元素提供附加信息
- 实体参照：实体是一部分公用文本或外部文件的快捷方式
- 注释
- 文档类型声明：Document Type Declarations，DTD

## 7.5 三层应用体系结构

- 单层体系结构
- 两层体系结构：客户-服务器体系结构
- 三层体系结构：展示层、中间层、数据管理层

## 7.6 展示层

## 7.7 中间层

```perl
#! /usr/bin/perl
use CGI;

$dataIn =  new CGI;
$dataIn->header();
$authorName = $dataIn->param('authorName');

print("<html><title>Argument passing test</title>");
print("The user passed the followed argument:");
print("authorName:", $authorName);

print("</html>");
exit;
```

中间层实现：

- CGI
- Java Servlet
- Java Server Pages

------

# 第8章 存储与索引概述

**文件组织**：当文件存储在磁盘上时，组织文件中记录的方法。

## 8.1 外部存储上的数据

在磁盘上读写信息的单位是页，页的大小是DBMS的一个参数，典型的值是4KB或8KB。

## 8.2 文件组织与索引

**记录文件**是DBMS中的一个重要抽象，并且被文件及访问方法层的代码实现。最简单的文件结构是无序文件，或称堆文件。一个堆文件中的数据在文件页中以任意的顺序排列，索引是在磁盘上组织数据记录的一种数据结构，它用于优化某类数据检索的操作，一个给定的数据记录集合上可以创建多个索引。

数据项存储：

- 数据项`k*`是一个真正的数据记录
- 数据项是一个`<k, rid>`对，其中rid是搜索码值为k的数据记录的记录rid
- 数据项是一个`<k, rid-list>`对，其中rid-list是搜索码值为k的数据记录的记录id的列表

如果一个文件组织成如下形式：数据记录的顺序与某一索引的数据项顺序相同或类似，我们就称这一索引是**聚簇索引**，否则就是**非聚簇索引**。

建立在包含主码的字段集合上的索引称为**主索引**，其他的索引就被称为**次索引**。如果两个数据项有相同的索引搜索码，它们就称为**重复**。主索引可以保证不含有重复，但是次索引不能。

## 8.3 索引数据结构

组织数据项的方法：

- 按照搜索码对数据项进行哈希
- 建立树状的数据结构，将搜索定位到数据项

 文件中的记录被分组存放在不同的桶中，其中一个桶由一个主页构成，或者是由一个主页和多个页构成的链组成。一个记录属于哪个桶可以由一个特殊的函数用于搜索码来决定，这个函数称为哈希函数。

B+树是一种保证在一棵给定树中从根到叶所有路径都等长的索引结构，即，这种结构的高度总是平衡的。非叶节点的平均孩子数称为树的**扇出(fan-out)**。

## 8.4 不同文件组织的比较

文件组织考虑的操作：

- 扫描
- 等值选择型的搜索
- 范围选择型的搜索
- 插入一个记录
- 删除一个记录

`B`-页码数量，`D`-每页所花费的读取时间

|    文件类型    |      扫描       |      等值搜索       |               范围搜索               |        插入         |     删除      |
| :------------: | :-------------: | :-----------------: | :----------------------------------: | :-----------------: | :-----------: |
|       堆       |      $BD$       |       $0.5BD$       |                 $BD$                 |        $2D$         | $Search + D$  |
|    排序索引    |      $BD$       |      $Dlog_2R$      |     $Dlog_2B + \# MatchingPages$     |    $Search + BD$    | $Search + BD$ |
|    聚簇索引    |     $1.5BD$     |    $Dlog_F1.5B$     |     $Dlog_FB + \# MatchingPages$     |    $Search + D$     | $Search + D$  |
|  非聚簇树索引  | $BD(R + 0.15)$  | $D(1 + log_F0.15B)$ | $D(log_F0.15B + \# MatchingRecords)$ | $D(3 + log_F0.15B)$ | $Search + 2D$ |
| 非聚簇哈希索引 | $BD(R + 0.125)$ |        $2D$         |                 $BD$                 |        $4D$         | $Search + 2D$ |

## 8.5 索引和性能调整

基于哈希的索引技术只对等值选择有较好的效果，而对范围选择性能较差，其性能还不如扫描整个文件。基于树的索引技术能有效地支持两种选择条件。树索引和哈希索引都能十分有效地支持插入、删除和更新操作。

树索引的一个相对缺陷是：排序文件中的页在磁盘上可以按照物理顺序分配，使得检索若干连续的页十分快。一个B+树的变种，称为**索引顺序访问方法(Indexed Sequential Access Method，ISAM)**，既有顺序分配叶子页的优点，又有快速搜索的优点。

**聚簇索引的设计举例**：

```sql
SELECT E.dno
FROM Employees E
WHERE E.age > 40
```

**改进**：

```sql
SELECT E.dno, COUNT(*)
FROM Employees E
WHERE E.age > 10
GROUP BY E.dno
```

创建索引：

```sql
CREATE INDEX IndAgeRating ON Students
  WITH STRUCTURE = BTREE,
    KEY = (age, gpa)
```

# 第9章 存储数据：磁盘和文件

## 9.1 存储层次

- 主存储器：高速缓存器、主存，易失性
- 第二级存储器：磁盘等较慢的设备，不易失
- 第三级存储器：光盘、磁带，不易失

## 9.2 廉价冗余磁盘阵列(RAID)

**磁盘阵列**是把几个磁盘组织在一起的一种形式，以提高性能和改善存储系统的可靠性。可靠性是通过增加冗余信息而不是通过保存数据的简单拷贝来改善的。实现数据划分和冗余相结合的磁盘阵列称为**独立磁盘冗余阵列**，简称为**RAID**。

**冗余模式**：校验模式包括基于汉明码和Reed-Solomon码两种模式。汉明码除了能从单个磁盘故障中恢复外，还能确定是哪一个磁盘出现故障。Reed-Solomon码能在两个磁盘同时出现故障时恢复数据。

在**数据划分**中，数据被分成相等大小的段，并分布到多个磁盘中。段的大小被称为**划分单位**。数据段通常使用循环算法分布：如果磁盘阵列有D个磁盘，那么数据段i被写在i mod D磁盘上。

在RAID系统中，磁盘阵列被划分为不同的**可靠组**，可靠组由一组数据盘和校验盘组成。

**冗余层次**：(以四个磁盘组成RAID为例子)

| 级别  |      方法      |                    备注                    | 空间利用率 |
| :---: | :------------: | :----------------------------------------: | :--------: |
|  0级  |     不冗余     |                                            |    100%    |
|  1级  |      镜像      | 在两个不同的磁盘上维护数据的两份相同的拷贝 |    50%     |
| 0+1级 |   划分和镜像   |                                            |    50%     |
|  2级  |   错误校验码   |            多个校验盘存储汉明码            |    57%     |
|  3级  |   位交叉校验   |            一个校验盘存储汉明码            |    80%     |
|  4级  |   块交叉校验   |                 一个校验盘                 |    80%     |
|  5级  | 块交叉分布校验 |  在所有磁盘均匀分布校验块，性能仅次于0级   |            |
|  6级  |    P+Q冗余     |               Reed-Solomon码               |    66%     |

## 9.3 磁盘空间管理

数据库管理系统结构的最底层软件称为磁盘空间管理器，它管理磁盘上的空间。

跟踪磁盘利用情况的一个方法是维护一个空闲块的列表。

很多数据库系统不依赖于OS的文件系统，而是另起炉灶或通过扩展OS功能来实现自己的磁盘空间管理。

## 9.4 缓冲区管理器

**缓冲区管理器**是负责在必要时把页面从磁盘取到主存的软件层。它通过把缓冲区划分为页集来管理可获得的主存，这些页集通常称为缓冲池。缓冲池中的主存页称为**帧**，即存放页(通常驻留在磁盘或其他二级存储上的页)的槽。

著名的替换策略是**最近最少使用策略LRU(last recently used)**，它通过在缓冲区中管理一个指向`pin-count`为0的帧的指针队列来实现。

**时钟替换**是LRU的变体，它使用变量`current`以环形顺序选择替换页。每个帧有个相关的`referenced`位，可以使得一个最近引用页将不可能被替换。

DBMS经常能预测引用模式是因为大多数页引用是由了解页存取模式的高层操作(如顺序扫描或各种关系代数操作的特殊实现)产生的。

## 9.5 记录文件

堆文件页中的数据不以任何顺序排序，唯一的保证是通过重复请求下一个记录的方式搜索文件中的所有记录。

- 页的链表：把堆文件维护为页的双向链表，缺点是维护变长记录时可能大量页有少量空闲字节。
- 页目录：维护页目录，记录每一个堆文件的第一目录页的位置。

## 9.6 页格式

页是由一组槽组成，每一个槽对应一个记录，记录由`<page id, slot number>`对标识。

**跟踪空槽的方法**：

- 选择在前面N个槽存放记录，如果删除，把页最后的记录移到前面。缺点：如果移动的记录被外部引用，则不能使用这种方法。
- 通过使用一个比特位的数组来处理记录的删除，每个槽对应一个比特位，用于跟踪空闲槽信息。

对变长记录最灵活的组织方式是为每一页维护一个槽目录，每个槽由`<record offset, record length>`对组成。

## 9.7 记录格式

定长记录中，每一个字段有固定长度，并且字段数也是固定的。

变长记录要么连续存放字段，并通过分隔符分隔；要么在记录开始处预留一些空间存放一个整数偏移量数组。

# 第10章 树结构索引

## 10.1 树索引介绍

我们称形如`<码, 指针>`的对为索引项，或者当上下文清楚的时候称为项。

## 10.2 索引顺序存取方法

**索引顺序存取方法(ISAM)**的数据项存放在树的叶子页和串链到叶子页的其他溢出页中。

大量插入操作作用于同一叶子，会产生很长的溢出页链。所以，树在开始创建时为每一页保留了20%的空闲时间。

不对索引级页加锁是ISAM优于B+树动态结构的一个重要优点。当数据分布和大小相对稳定而使得溢出链很少时，ISAM将优于B+树。

## 10.3 B+树：一种动态索引结构

B+树是一个平衡树，它的内节点用于指导搜索，叶子节点包含数据项。

对于一个每个节点包含m个项的B+树，其中$d \le m \le 2d$，d是B+树的一个参数，称为树的**秩**，它是树节点容量的度量。根节点是唯一的不需要满足项数目要求的例外节点，它只需满足$1 \le m \le 2d$。

## 10.4 搜索

在`tree_search`中查找i需要在节点内进行搜索，其方法可以是线性搜索或二分法搜索(依赖于节点内项的数目)。

在讨论B+树的搜索、插入和删除算法时，前提是不存在重复。当搜索码不包含候选码时就会产生重复。

## 10.5 插入

插入算法的基本思想时通过在合适的孩子节点调用插入算法而递归地插入项。

如果碰巧一个节点是满的，则它必须被分裂。当节点被分裂时，一个指向分裂产生的节点的项必须插入到它的父节点，指针变量`newchildentry`指向该项。如果根节点被分裂，将创建一个新的根节点，树的高度增加1。

## 10.6 删除

删除算法的基本思想是通过在合适的孩子节点上调用删除算法而递归删除项。

如果一个节点在删除前刚好在最小占有情况，则删除将引起该节点的占有率降到占有率阈值之下。当这种情况发送时，或者与邻近的兄弟一起重分布项，或者与兄弟合并，以保证最小的占有率。

## 10.7 重复

- B+树可以利用溢出页来处理重复。
- 可以像处理其他项一样处理重复项，在搜索时搜索给定码值的最左边的数据项，然后搜索多个叶子页(使用叶子顺序指针)。

## 10.8 实际的B+树

B+树的高度依赖于数据项的数目和索引项的大小。索引项的大小决定一页能存放索引项的数目，即树的**扇出数**。

索引项包含一个搜索码值和页的指针，所以其大小主要依赖于搜索码值的大小。

**前缀码压缩**可以很大程度上增加树的扇出数。

加载B+树的方法：从空树开始插入、块加载B+树。

在已有的记录集上创建索引的操作:

1. 创建要插入索引的数据项
2. 排序数据项
3. 从排序的项创建索引

实际上秩的概念通常被淡化，并用物理空间标准所代替。这样做的原因是叶子节点和非叶子节点通常能存放的项数不同，第二个原因是搜索码可能包含一个变长字符串字段。即使索引是基于定长字段建立，多个记录可能有相同的搜索码值，这种情况也可能引起变长的叶子项。

# 第11章 基于哈希的索引

## 11.1 静态哈希

包含数据的页被看作**桶**的集合，每个桶中有一个主页，还可能有一些溢出页。

为了搜索一个数据项，需要利用一个**哈希函数**h确定项所在的桶，然后搜索这个桶。为了加速桶内的搜索，可以按照搜索码值的排列顺序维护数据项。

如果现在有N个桶，桶号从0到N-1，则`h(value) = (a * value + b)`形式的哈希函数h在实际中很适用，其中常数a和b能用于调节哈希函数。

静态哈希的主要问题是桶数是固定的。

## 11.2 可扩展哈希

可以使用一个指向桶的指针**目录**，同时仅仅通过目录加倍和分裂溢出桶来实现桶数的加倍。

可扩展哈希法的基本技术是使用哈希函数h得到二进制数，然后解释后d位数作为目录的偏移量，这里的d依赖于目录的大小。数字d被称为**全局深度**，并作为文件头的一部分保存起来。桶分裂不是一定需要进行目录加倍，所以我们需要为每一个桶维护一个**局部深度**。

## 11.3 线性哈希

线性哈希和可扩展哈希一样，是一种动态哈希技术，可以有效地支持插入和删除操作。与可扩展哈希相比，它不需要目录，能自然地处理冲突，并且对桶分裂地时机提供很多灵活性。

线性哈希地模式是利用哈希函数$h_0, h_1, h_2 ...$的家族，其特性是每个函数的区间都是它前辈的两倍。

分裂时在插入新的数据项而引起溢出页的创建时被“触发的”，无论何时分裂被触发，Next指向的桶将被分裂。

## 11.4 可扩展哈希与线性哈希的关系

实际上，线性哈希从$h_i$到$h_{i+1}$的转换就对应与可扩展哈希的目录加倍。

# 第12章 查询求解概述

## 12.1 系统目录

一个关系型DBMS维护关于其所有表和索引的信息，该描述信息本身存储在一个称为**目录表(catalog tables)**的特殊表的集合中。目录表也被称为数据字典(data dictionary)、系统目录(system catalog)，或简称目录(catalog)。

系统目录需要存储缓冲池的大小、页的大小，以及关于单个表、索引和视图的信息。此外，关于表和索引的统计信息也存储在系统目录中，并定期更新。系统目录也包含关于用户的信息，例如记账信息和授权信息。

目录表描述数据库中的所有表，包括目录表本身。

系统目录本身是一个表集合，可以和其他表一样使用DBMS的查询语言来查询。

## 12.2 操作符求解概述

以下三个简单的技术常用于实现各个操作符的算法：

- **索引(Indexing)**：如果指定了一个选择或连接条件，使用一个索引去检查仅满足条件的元组。
- **迭代(Iteration)**：逐个检查输入表中的所有元组。
- **划分(Partitioning)**：通过在一个排序码上划分元组。(排序或哈希两种划分技术)

访问路径就是从表中获取元组的方法，它由文件扫描或索引加上匹配选择的条件所构成。一个简单的选择由若干形如`attr op value`的条件组成，其中`op`是下列比较操作符之一：$<、\le、=、\ne、\ge、>$。这样的选择称为**合取范式(conjunctive normal form, CNF)**，并且每一个条件称为**合取体(conjunct)**。我们将索引所匹配的合取体称为选择中的主合取体(primary conjuncts)。

## 12.3 关系型操作的算法

- 选择($\sigma$)：如果R上有一个或多个索引和选择条件相匹配，可以使用索引来获取匹配的元组，并将使用余下的选择条件来进一步约束结果集。
- 投影($\pi$)：可以简单地通过表上的迭代来获取域的子集，代价在于是否需要删除重复。
- 连接($\bowtie$)：排序合并连接、索引嵌套循环连接。前者代价小，后者有个逐渐增加的好特性。
- 聚集：代价在于删除重复，与投影类似。
- 分组：通常是通过排序实现的。

作为一个经验规律，如果要获取超过5%的元组，直接扫描整个表可能代价更低。

## 12.4 查询优化概述

关系查询语言的优点之一是，用户可以用多种方式来表达查询，系统也可以以多种方式执行查询。

通常查询优化器把查询语句看作一个基本的$\sigma - \pi - \bowtie$代数表达式，及在$\sigma - \pi - \bowtie$表达式的运算结果之上的其他关系操作，并以此确定查询的执行计划空间。优化有两个基本步骤：

- 枚举求解表达式的各个执行计划，由于所有可能的执行计划的数目非常大，通常查询优化器只考虑其中的一个子集。
- 估算每个枚举的执行计划的开销，选取其中最小的开销的执行计划。

**查询求解计划**(或者简称**执行计划**)由一个扩展的关系代数树和节点上表示关系存取路径及关系操作的实现方法的标记组成。

当一个查询包括多个关系运算时，可以通过管道将操作的运算结果传递给下一个操作，而不需要将查询执行的中间结果保存在临时的关系中。(**流水线**)

为了简化协调查询求解计划运行的代码，组成执行计划树节点(采用流水线技术求解)的关系运算通常支持统一的**迭代操作**接口，隐藏关系运算 的实现细节。关系运算的迭代操作接口包括函数`open`、`get_next`和`close`。`open`对迭代操作进行初始化，`get_next`调用每个输入节点的`get_next`函数，然后调用关系运算特定的实现代码处理输入元组，`close`释放状态信息。

## 12.5 可选计划：研究这一问题冬季的示例

连接操作是非常费时的操作，因此优化连接操作的一个启发性的规则是尽可能减少连接关系的大小。

**下推选择**：提前进行选择操作，如果选择操作出现在连接操作之后，应该检查是否可以将选择操作“下推”到连接操作之前。类似于选择操作的下推，对执行计划的进一步优化可以下推投影操作。

使用索引来优化查询的执行计划。

## 12.6 一个典型的优化器做些什么

**左深(left-deep)计划**选择的原因：

- 随着连接的数量增加，可选计划的数量飞快地增加，因此有必要削减可选计划的空间。
- 左深树允许我们生成所有的全流水线(fully pipeline)计划。

# 第13章 外排序

## 13.1 什么时候DBMS需要对数据进行排序

- 用户需要按一定的次序返回查询结果
- 在块载入树索引时，排序时第一个步骤
- 消除重复元组时需要排序操作
- 重要的关系代数操作——连接的方法，需要进行排序

## 13.2 简单的两路归并排序算法

两路归并排序算法的简单实现，需要三个主存页。缺点：即使可以使用的主存再多，简单排序算法也不能有效的利用这些主存。

## 13.3 外归并排序

外归并排序对两路归并排序的改进：

1. 减少了第0遍处理生成的有序段的数目，为N1=[N/B]，而两路归并排序是N。
2. 采用(B-1)路归并，数据处理的遍数大大大减少。

多路归并比两路归并的CPU开销要大得多，但通常情况下，I/O开销才是更主要的。在进行(B-1)路归并的过程中，需要不断地从(B-1)个有序段中找出“最小”的记录，然后写到输出缓冲区。

采用**替换排序**算法，平均的段长可能达到近似 2*B。但是因为元组的长度不同，商业数据库主存进行替换排序是非常困难的，索引没有被商业数据库采用该改进。

## 13.4 最小化I/O开销和I/O次数

采用块读写和双缓冲技术可以改进外排序算法。

快读写技术可以减少读写单个数据页的平均开销，我们尽量增大同时归并的路，而只保留一个输入缓冲区和一个输出缓冲区。

双排序技术可以通过为每个输入缓冲区分配额外的缓冲区页，来实现CPU操作和I/O操作的重叠。

## 13.5 使用B+树来排序

如果查询码上存在B+树索引，那么我们可以使用B+树按照查询码的次序遍历顺序集合读取记录，而不必采用排序算法。

如果B+树是聚簇索引，那么遍历顺序集合的效率就很高，唯一的开销就是读取顺序集合中的数据页。

如果B+树是非聚簇索引，页节点数据页中的每个rid都可能指向不同的数据页，这时候读取使用数据记录的I/O开销可能与数据记录的数目相等。

即使最合适的文件大小，采用非聚簇B+树索引也不如采用外排序算法。

# 第14章 关系操作求解

## 14.1 选择操作

对于形如$\sigma_{R.attr <op> value}(R)$的查询，如果不存在属性域R.attr上的索引，并且关系对于R.attr无序，就只能扫描整个关系。

对于形如$\sigma_{R.attr <op> value}(R)$的查询，如果不存在属性域R.attr上的索引，但关系按照属性域R.attr顺序存储，那么就可以利用这种顺序，采用二分查找算法来定位满足查询条件的第一个元组。

如果在属性R.attr上存在B+树索引，并且op不是相等操作，那么处理选择条件$\sigma_{R.attr <op> value}(R)$的最佳策略就是采用索引。对相等的选择条件来说，虽然哈希索引更好一些，但这种策略也是一种比较好的存取路径。

如果在属性R.attr上存在哈希索引，并且op是相等操作，实现查询$\sigma_{R.attr <op> value}(R)$的最佳策略显然是采用哈希索引读取满足条件的元组。

## 14.2 一般的选择条件

一般的查询条件由一系列形如attribute **op** constant或者attribute1 **op** attribute2选择项连接成的布尔表达式(即使用逻辑链接符$\land$和$\lor$的表达式)。

处理一般的选择操作，首先要把查询条件表示成合取范式(CNF)，即表示成通过逻辑符$\land$连接起来的合取式的集合。每个合取式由一个或多个用逻辑符$\lor$连接起来的查询项组成。

求解有析取的选择时，可以在文件扫描的过程中检查别的条件。大多数数据库系统都不能有效的处理含析取式的查询条件，而只是将注意力集中在对无析取式的查询条件的优化。

## 14.3 投影操作

实现投影操作需要完成：

1. 清除不需要的属性值(即在投影中未指定的属性域)。
2. 清除重复的元组。

清除重复元组有两个基本的算法：一个基于排序的算法，另一个是采用哈希函数的算法。

**基于排序的投影操作算法**：

1. 扫描关系R，生成只包含所需属性域的元组集合。
2. 以元组中的所有属性域作为码，对元组集合排序。
3. 顺序扫描集合同时比较相邻的元素以消除重复的元组。

**基于哈希函数的投影**：划分和消除重复元组。

如果重复元组的数目很多，或者元组的分布很不均匀，那么采用基于排序的投影算法要比用哈希函数好。

如果索引的查询码包括投影操作指定的所有属性域，那么就可以利用已有的索引。在这种情况下，可以简单的从索引中读取码值，不需要存取实际数据，同时应用上面的投影技术，这种方法称为**唯索引扫描技术**。

## 14.4 连接操作

实现连接操作的算法：**简单的嵌套循环算法**、**块嵌套循环算法**。这两种算法属于迭代技术，其他的连接算法都避而不谈叉积，属于索引和划分技术，比如**索引嵌套循环连接算法**。

**排序归并连接算法**的基本思想是在连接属性域上对两个关系进行排序，然后通过查找匹配的元组$r \in R$和$s \in S$来归并两个有序关系。**块存取**和**双缓存技术**不仅仅可以用于对关系排序，还可以用来对算法的归并阶段进行优化。

与排序归并算法类似，**哈希连接算法**首先在算法的划分阶段对关系R和S进行划分，在随后的连接阶段中，比较R部分的元组和关系S相应部分的元组，检查等价条件。

## 14.5 集合操作

对关系的并和差运算有两种实现算法，它们分别基于排序和哈希函数。

要实现$R \cup S$：(**排序**)

1. 在所有属性域上对关系R和S排序。
2. 并行的扫描关系R和S，进行归并，并消除重复元组。

要实现$R \cup S$：(**哈希**)

1. 利用哈希函数h划分关系R和S。
2. 对每个划分l作如下处理：在内存中创建$S_l$部分的哈希表；扫描$R_l$，对部分中的每个元组，查找$S_l$的哈希表，如果元组在哈希表中就丢弃元组，否则就添加到哈希表中；输出哈希表中的元组，并清除哈希表处理下一个部分。

## 14.6 聚集操作

聚集操作包括：`AVG`、`MIN`、`MAX`、`SUM`、`COUNT`。聚集操作也可以与`GROUP BY`语句联合使用。

对于带有分组的查询，有两种不需依赖现有索引导出求解算法，分别基于排序和哈希函数。

基于排序的算法很简单，在分组属性域上对关系排序，再扫描计算每个组的聚集操作的结果。

基于哈希函数的算法再分组属性域上创建关系的哈希表。

## 14.7 缓冲的影响

- 如果有多个操作并发执行，那么它们共享缓冲池。这会减少每个操作可用的缓冲区页的数目。
- 如果利用索引，尤其是聚簇索引访问元组时，再缓冲池中找到多次请求的数据页的可能性依赖于缓冲池的大小和缓冲区页的替换策略。
- 如果某个操作对数据页的重复访问具有一定的模式，我们可以通过选择替换策略，或者为这种操作保留足够数量的缓冲区页，以增加在内存中找到数据页的可能性。

# 第15章 典型的关系查询优化器

对关系代数表达式进行优化，有两个基本的步骤：

- 列举出若干求解表达式的执行计划。
- 估计每个列举出的执行计划的开销，选择开销最小的执行计划。

## 15.1 将SQL查询转换成关系代数表达式

查询优化的第一步是将SQL查询转换成叫做查询块的单元，典型的查询优化器每次只优化一个查询块。

查询块是一个无嵌套的SQL语句，它只有一个SELECT语句和FROM语句，而且最多有一个WHERE、GROUP BY和HAVING语句。对查询块进行优化的第一步是将查询块表示成关系代数表达式。SELECT语句对应于投影操作，WHERE语句对应于选择操作，FROM语句对应于关系的叉积。为了保证GROUP BY和HAVING语句能够正确的执行，把语句中涉及到的关系域添加到投影操作的属性域列表中。对$\sigma \pi \times$表达式的优化从本质上忽略了聚合操作。

## 15.2 估算执行计划的开销

查询块的执行开销估计包括以下两个部分：

- 对查询树的每个节点，估算执行对应操作的开销。
- 对查询树的每个节点，估算操作结果的规模，以及结果是否有序。

查询结果元组的最大数目(不消除重复元组)是FROM语句中所有关系的叉积，但WHERE语句中的条件会消除其中的一些候选元组。我们为每个查询条件确定一个约简因子，来表示WHERE语句对 查询结果大小的影响。

直方图是DBMS用来近似数据分布的一种数据结构，分为**等宽直方图**、**等高直方图**。对部分频率很高的属性值单独维护其频率，而对其余的属性值采用等高直方图，这种直方图叫做**压缩的直方图**。

## 15.3 关系代数的等价

- **选择**

选择操作的级联：$\sigma_{c1 \land c2 \land ... \land cn}(R) \equiv \sigma_{c1}(\sigma_{c2}(...(\sigma_{cn}(R))))$

互换：$\sigma_{c1}(\sigma_{c2}(R)) \equiv \sigma_{c2}(\sigma_{c1}(R))$

- **投影**

$\pi_{a1}(R) \equiv \pi_{a1}(\pi_{a2}(...(\pi_{an}(R))))$，对于a，有$a_i \in a_{i+1}$

- **叉积和连接**

互换：$R \times S \equiv S \times R$，$R \bowtie S \equiv S \bowtie R$

结合律：$R \times (S \times T)  \equiv (R \times S) \times T$，$R \bowtie (S \bowtie T) \equiv (R \bowtie S) \bowtie T$

- **选择、投影和连接**

选择和投影可以互换：$\pi_a(\sigma_c(R)) \equiv \sigma_c(\pi_a(R))$

选择和叉积可以组合形成连接：$R \bowtie_{c} S \equiv \sigma_c(R \times S)$

选择可以和叉积或连接互换：$\sigma_c(R \times S) \equiv \sigma_c(R) \times S$，$\sigma_c(R \bowtie S) \equiv \sigma_c(R) \bowtie S$

投影可以和叉积或连接互换：$\pi_a(R \times S) \equiv \pi_a(R) \times \pi_a(S)$，$\pi_a(R \bowtie_c S) \equiv \pi_{a1}(R) \bowtie_c \pi_{a2}(S)$

## 15.4 列举可选的执行计划

不使用索引的查询计划算法开销：

- 扫描文件读取元组，进行选择和投影操作。
- 将选择和投影操作的结果元组写回磁盘。
- 对元组排序，实现GROUP BY语句。

使用索引的查询计划：

- 单索引存取路径
- 多索引存取路径
- 有序索引存取路径
- 唯索引存取路径

左深度树执行计划：

- System R类型的查询优化器列举出所有的左线性树，并且尽可能的提前进行选择和投影操作，执行计划的列举可以理解成一个需要进行多遍处理的算法。

## 15.5 嵌套子查询

嵌套查询常常可以转换成非嵌套的等价查询，关联查询也常常可以转换成非关联的等价查询。

## 15.6 System R优化器

- 使用关于数据库示例的统计数据来预测一个查询计划的代价。
- 当内关系是一个基关系(即，不是一个临时关系)的时候，只考虑二元连接。
- 将优化集中在无嵌套的查询上，对于有嵌套的查询采用相对特别的方法。
- 对投影不进行重复消去(除了在查询估价的最后一步需要一个DISTINCT子句的时候)。
- 采用一个关于CPU代价以及I/O代价的代价模型。

## 15.7 查询优化的其他方法

基于规则的优化器：采用一系列规则指导执行计划的生成。

随机生成执行计划的方法：采用退火模拟等随机算法迅速搜索庞大的执行计划空间。

# 第16章 事务管理概述

DBMS中的事务的主要特点是并发执行，并且在系统发生故障时能够进行恢复。

## 16.1 ACID属性

为确保DBMS能够在并发访问和系统发生故障时对数据进行维护，事务应该具有下面四个主要特性：

- **原子性**：所有操作要么全部都被执行，要么都不被执行。
- **一致性**：每个单独执行的事务，必须能够保证数据库的一致性。
- **隔离性**：即使DBMS将若干事务的操作交叉执行，用户也不需要考虑并发执行的其他事务的影响。
- **持久性**：一旦DBMS通知用户事务已经成功执行，那么即使数据更新存入磁盘之前发生故障，数据也能被永久报错。

## 16.2 事务和调度

DBMS把事务看作一个操作系列或者队列，调度时某个事务集合对应的一个操作(读、写、提交、中止)列表。

如果调度中包括每个事务的中止或者提交操作，该调度称为**完全调度**。如果来自不同事务的操作没有交叉执行，也就是说事务一个接着一个地从开始执行一直到结束，这样的调度称为**串行调度**。

## 16.3 事务的并发执行

将I/O操作和CPU交叉执行可以减少磁盘和CPU的空闲等待时间，并增加系统的**吞吐量**(给定时间内完成事务的平均数目)。

提交事务集合S的一个可串行化调度在任何处于一致状态数据库上的执行结果必须与某个完全串行调度相同。

DBMS有时可能会以不可串行化的方式执行事务，原因有二：DBMS使用的并发控制方法采用的调度可能本身不是可串行化的，但等价于某种可串行化调度；SQL允许某些应用程序指示DBMS选择不可串行化的调度。

交叉执行带来的异常：

- 读未提交的数据(WR冲突)
- 不可重复的读(RW冲突)
- 重写未提交的数据(WW冲突)

一个可恢复的调度要求如下：如果一些事务读了由另一些事务修改了的数据，那么它们的提交必须放在另一些事务的提交之后。

## 16.4 基于加锁的并发控制

严格的两阶段加锁(2PL)的规则：

- 如果事务T希望读或写某个数据对象，那么应该首先申请一个该数据对象的共享锁或互斥锁(排他锁)。
- 事务结束时，其拥有的所有锁都将被释放。

等待锁释放的事务循环被称为**死锁**，发现死锁的简单方法是超时机制。

## 16.5 加锁的性能

基于锁的机制是用来解决事务之间的冲突的，它使用两种机制：**阻塞(blocking)**、**中止(aborting)**。

## 16.6 SQL对事务的支持

`COMMIT`命令用于提交，`ROLLBACK`命令用于中止，`SAVEPOINT`命令允许声明检查点。

`AND CHAIN`是链事务的命令，可以在提交和回滚一个事务之后立即初始化另一个事务。

为了让程序员能够控制因为他们的事务引起的锁的开销，SQL允许他们指定一个事务的三种模式：访问模式(access mode)、诊断尺度(diagnostics size)、隔离级别(isolation level)。诊断尺度记录错误情况的个数，访问模式可以是只读、读写，隔离级别控制了某一给定的事务暴露在其他并发执行的事务动作中的程度。 

## 16.7 崩溃恢复简介

保证在数据库的更新之前将描述这种更新的日志条目写入稳定的存储是非常重要的，否则，会发生先写记录。

ARIES是使用偷帧、非强制方法的一种恢复算法。

# 第17章 并发控制

## 17.1 2PL、可串行性和可恢复性

如果两个调度包括相同的事务操作，并且它们发生冲突的操作对在调度中的次序相同，就可以称这两个调度是**冲突等价**的。

当一个调度冲突等价于某个串行调度时，我们称该调度为**冲突可串行**。

**优先图**对于获得调度中事务间的所有冲突很有用，优先图也被称为串行化图。一个调度S的优先图包括：

- 对于S中的每个提交事务用一个节点来表示。
- 如果$T_i$先执行，并且和$T_j$的某个操作发生冲突，那么应存在一条从$T_i$到$T_j$的边。

对于某个调度，如果一个事务写入的值在其提交或者中止之前没有其他事务读或者重写，那么称这个调度是**严格**的。当一个调度观测等价于某个可串行化调度时，就称该调度是**观测可串行化的**。

## 17.2 加锁管理简介

DBMS中处理事务加锁事宜的那部分称为锁管理器，锁管理器维护着一个锁表，这是一个以数据对象标识为码的哈希表。DBMS也在事务表中维护每个事务的描述信息项，该记录中包含一个指向事务拥有的锁列表的指针。

加锁和解锁命令必须实现成原子操作，当锁管理器并发执行时，需要使用操作系统的同步机制来访问锁表。

## 17.3 锁转换

事务可能需要对已经获得了共享锁的对象再请求排他锁。

如果没有其他事务拥有该对象的共享锁，可以立即对事务的锁进行升级满足互斥锁请求，否则将该加锁请求加在等待队列的最前面。

一个更好的方法时不进行锁升级，而是在开始时首先获得排它锁。

## 17.4 死锁处理

锁管理器维护一个称为**等待图**的结构来检测死锁循环。通过中止循环中的一个事务并释放其拥有的锁，可以解决死锁问题。

基于优先级预防的方案中，锁管理器使用下列的两条策略之一：

- 等待-死亡策略：如果$T_i$的优先权高，则等待$T_j$结束，否则中止$T_i$。
- 受伤-等待策略：如果$T_i$优先权高，中止$T_j$，否则$T_i$等待。

在等待-死亡策略中，低优先权的事务永远都不会等待高优先权的事务；在受伤-等待策略中，高优先权的事务永远不会等待低优先权的事务。任一种情况都不会产生死锁循环。

## 17.5 特殊的加锁技术

树结构索引有下面两种现象：

- 树的高层只用于直接搜索，所有真正的数据都存在叶子层中。
- 插入时，如果更新的叶子节点发生分裂并传播到上层节点，那么对于该节点也必须加锁。

另一种特殊的加锁策略被称为**多粒度锁**，它能够有效地对包含其他对象的对象加锁。多粒度锁协议还使用两种新类型的锁，称为**准备共享锁(IS)**和**准备互斥锁(IX)**。IS锁仅和互斥锁有冲突，IX锁和S和X锁有冲突。

## 17.6 不加锁的并发控制

加锁协议对于处理事务间的冲突是一种悲观的方法，主要是通过中止或者阻塞事务来解决冲突。

乐观的并发控制方法的基本前提是大多数事务不会与其他事务发生冲突。事务执行分为以上三个阶段：

1. 读：事务开始执行。
2. 验证：事务决定提交时验证。
3. 写：事务没有冲突时写。

对于乐观的并发控制方法，将时间戳按顺序强加给事务，并验证所有的冲突操作是按照同样的顺序发生的。