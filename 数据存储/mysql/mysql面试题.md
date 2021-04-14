# mysql

## 一张表，里面有 ID 自增主键，当 insert 了 17 条记录之后，删除了第 15,16,17 条记录，
再把 Mysql 重启，再 insert 一条记录，这条记录的 ID 是 18 还是 15 ？

(1)如果表的类型是 MyISAM，那么是 18
因为 MyISAM 表会把自增主键的最大 ID 记录到数据文件里，重启 MySQL 自增主键的最大
ID 也不会丢失

（2）如果表的类型是 InnoDB，那么是 15
InnoDB 表只是把自增主键的最大 ID 记录到内存中，所以重启数据库或者是对表进行
OPTIMIZE 操作，都会导致最大 ID 丢失

## Mysql 的技术特点是什么？
Mysql 数据库软件是一个客户端或服务器系统，其中包括：支持各种客户端程序和库的多
线程 SQL 服务器、不同的后端、广泛的应用程序编程接口和管理工具。

## Heap 表是什么？
HEAP 表存在于内存中，用于临时高速存储。

BLOB 或 TEXT 字段是不允许的

只能使用比较运算符=，<，>，=>，= <

HEAP 表不支持 AUTO_INCREMENT

索引不可为 NULL

## Mysql 服务器默认端口是什么？
Mysql 服务器的默认端口是 3306。

## 与 Oracle 相比，Mysql 有什么优势？
Mysql 是开源软件，随时可用，无需付费。

Mysql 是便携式的
带有命令提示符的 GUI。
使用 Mysql 查询浏览器支持管理
## 如何区分 FLOAT 和 DOUBLE？
以下是 FLOAT 和 DOUBLE 的区别：

浮点数以 8 位精度存储在 FLOAT 中，并且有四个字节。

浮点数存储在 DOUBLE 中，精度为 18 位，有八个字节。
## 区分 CHAR_LENGTH 和 LENGTH？

CHAR_LENGTH 是字符数，而 LENGTH 是字节数。Latin 字符的这两个数据是相同的，但是对
于 Unicode 和其他编码，它们是不同的。
## 请简洁描述 Mysql 中 InnoDB 支持的四种事务隔离级别名称，以及逐级之间的区别？
SQL 标准定义的四个隔离级别为：

read uncommited ：读到未提交数据

read committed：脏读，不可重复读

repeatable read：可重读

serializable ：串行事物

## 在 Mysql 中 ENUM 的用法是什么？
ENUM 是一个字符串对象，用于指定一组预定义的值，并可在创建表时使用。

Create table size(name ENUM('Smail,'Medium','Large');
## 如何定义 REGEXP？
REGEXP 是模式匹配，其中匹配模式在搜索值的任何位置。
## CHAR 和 VARCHAR 的区别？
以下是 CHAR 和 VARCHAR 的区别：

CHAR 和 VARCHAR 类型在存储和检索方面有所不同
CHAR 列长度固定为创建表时声明的长度，长度值范围是 1 到 255
当 CHAR 值被存储时，它们被用空格填充到特定长度，检索 CHAR 值时需删除尾随空格。
## 列的字符串类型可以是什么？
字符串类型是：
* SET
* BLOB
* ENUM
* CHAR
* TEXT
* VARCHAR
## 如何获取当前的 Mysql 版本？
SELECT VERSION();用于获取当前 Mysql 的版本。

## Mysql 中使用什么存储引擎？
存储引擎称为表类型，数据使用各种技术存储在文件中。
技术涉及：

Storage mechanism

Locking levels

Indexing

Capabilities and functions.

## Mysql 驱动程序是什么？
以下是 Mysql 中可用的驱动程序：

PHP 驱动程序

JDBC 驱动程序

ODBC 驱动程序

CWRAPPER

PYTHON 驱动程序

PERL 驱动程序

RUBY 驱动程序

CAP11PHP 驱动程序

Ado.net5.mxj

## TIMESTAMP 在 UPDATE CURRENT_TIMESTAMP 数据类型上做什么？
创建表时 TIMESTAMP 列用 Zero 更新。只要表中的其他字段发生更改，UPDATE
CURRENT_TIMESTAMP 修饰符就将时间戳字段更新为当前时间。

## 主键和候选键有什么区别？
表格的每一行都由主键唯一标识,一个表只有一个主键。
主键也是候选键。按照惯例，候选键可以被指定为主键，并且可以用于任何外键引用。

## 如何使用 Unix shell 登录 Mysql？
我们可以通过以下命令登录：
[mysql dir]/bin/mysql -h hostname -u

##  myisamchk 是用来做什么的？
它用来压缩 MyISAM 表，这减少了磁盘或内存使用。

## MYSQL 数据库服务器性能分析的方法命令有哪些?
## 如何控制 HEAP 表的最大尺寸？
Heal 表的大小可通过称为 max_heap_table_size 的 Mysql 配置变量来控制。

## MyISAM Static 和 MyISAM Dynamic 有什么区别？
在 MyISAM Static 上的所有字段有固定宽度。动态 MyISAM 表将具有像 TEXT，BLOB 等字
段，以适应不同长度的数据类型。点击这里有一套最全阿里面试题总结。

MyISAM Static 在受损情况下更容易恢复。

## federated 表是什么？
federated 表，允许访问位于其他服务器数据库上的表。

## 如果一个表有一列定义为 TIMESTAMP，将发生什么？
每当行被更改时，时间戳字段将获取当前时间戳。

## 列设置为 AUTO INCREMENT 时，如果在表中达到最大值，会发生什么情况？
它会停止递增，任何进一步的插入都将产生错误，因为密钥已被使用。

## 怎样才能找出最后一次插入时分配了哪个自动增量？
LAST_INSERT_ID 将返回由 Auto_increment 分配的最后一个值，并且不需要指定表名称。

## 你怎么看到为表格定义的所有索引？
索引是通过以下方式为表格定义的：
SHOW INDEX FROM

## LIKE 声明中的％和_是什么意思？
％对应于 0 个或更多字符，_只是 LIKE 语句中的一个字符。

## 如何在 Unix 和 Mysql 时间戳之间进行转换？
UNIX_TIMESTAMP 是从 Mysql 时间戳转换为 Unix 时间戳的命令
FROM_UNIXTIME 是从 Unix 时间戳转换为 Mysql 时间戳的命令

## 列对比运算符是什么？
在 SELECT 语句的列比较中使用=，<>，<=，<，> =，>，<<，>>，<=>，AND，OR 或 LIKE 运
算符。

## 我们如何得到受查询影响的行数？
行数可以通过以下代码获得：
SELECT COUNT(user_id)FROM users;

## Mysql 查询是否区分大小写？

不区分
SELECT VERSION(), CURRENT_DATE;
SeLect version(), current_date;
seleCt vErSiOn(), current_DATE;
所有这些例子都是一样的，Mysql 不区分大小写。

## LIKE 和 REGEXP 操作有什么区别？

LIKE 和 REGEXP 运算符用于表示^和％。
SELECT * FROM employee WHERE emp_name REGEXP "^b";
SELECT * FROM employee WHERE emp_name LIKE "%b";

## BLOB 和 TEXT 有什么区别？

BLOB 是一个二进制对象，可以容纳可变数量的数据。有四种类型的 BLOB -
TINYBLOB
BLOB
MEDIUMBLOB 和
LONGBLOB
它们只能在所能容纳价值的最大长度上有所不同。

TEXT 是一个不区分大小写的 BLOB。四种 TEXT 类型
TINYTEXT
TEXT
MEDIUMTEXT 和
LONGTEXT

它们对应于四种 BLOB 类型，并具有相同的最大长度和存储要求。

BLOB 和 TEXT 类型之间的唯一区别在于对 BLOB 值进行排序和比较时区分大小写，对 TEXT
值不区分大小写。

## mysql_fetch_array 和 mysql_fetch_object 的区别是什么？
以下是 mysql_fetch_array 和 mysql_fetch_object 的区别：

mysql_fetch_array（） - 将结果行作为关联数组或来自数据库的常规数组返回。

mysql_fetch_object - 从数据库返回结果行作为对象。

## 我们如何在 mysql 中运行批处理模式？
以下命令用于在批处理模式下运行：

mysql;
mysql mysql.out
## MyISAM 表格将在哪里存储，并且还提供其存储格式？
每个 MyISAM 表格以三种格式存储在磁盘上：

·“.frm”文件存储表定义

·数据文件具有“.MYD”（MYData）扩展名

索引文件具有“.MYI”（MYIndex）扩展名

## Mysql 中有哪些不同的表格？
共有 5 种类型的表格：
MyISAM

Heap

Merge

INNODB

ISAM

MyISAM 是 Mysql 的默认存储引擎。

## ISAM 是什么？
ISAM 简称为索引顺序访问方法。它是由 IBM 开发的，用于在磁带等辅助存储系统上存储和
检索数据。

## InnoDB 是什么？
lnnoDB 是一个由 Oracle 公司开发的 Innobase Oy 事务安全存储引擎。

## Mysql 如何优化 DISTINCT？
DISTINCT 在所有列上转换为 GROUP BY，并与 ORDER BY 子句结合使用。

SELECT DISTINCT t1.a FROM t1,t2 where t1.a=t2.a;

## 如何输入字符为十六进制数字？
如果想输入字符为十六进制数字，可以输入带有单引号的十六进制数字和前缀（X），或者
只用（Ox）前缀输入十六进制数字。

如果表达式上下文是字符串，则十六进制数字串将自动转换为字符串。

## 如何显示前 50 行？
在 Mysql 中，使用以下代码查询显示前 50 行：

SELECT*FROM
LIMIT 0,50;

## 可以使用多少列创建索引？
任何标准表最多可以创建 16 个索引列。

## NOW（）和 CURRENT_DATE（）有什么区别？

NOW（）命令用于显示当前年份，月份，日期，小时，分钟和秒。
CURRENT_DATE（）仅显示当前年份，月份和日期。

## 什么样的对象可以使用 CREATE 语句创建？
以下对象是使用 CREATE 语句创建的：

DATABASE

EVENT

FUNCTION

INDEX

PROCEDURE

TABLE

TRIGGER

USER

VIEW

## Mysql 表中允许有多少个 TRIGGERS？ 在 Mysql 表中允许有六个触发器，如下：

BEFORE INSERT

AFTER INSERT

BEFORE UPDATE

AFTER UPDATE

BEFORE DELETE

AFTER DELETE

## 什么是非标准字符串类型？

以下是非标准字符串类型：

TINYTEXT

TEXT

MEDIUMTEXT

LONGTEXT

## 什么是通用 SQL 函数？

CONCAT(A, B) - 连接两个字符串值以创建单个字符串输出。通常用于将两个或多个字段合

并为一个字段。

FORMAT(X, D)- 格式化数字 X 到 D 有效数字。

CURRDATE(), CURRTIME()- 返回当前日期或时间。

NOW（） - 将当前日期和时间作为一个值返回。

MONTH（），DAY（），YEAR（），WEEK（），WEEKDAY（） - 从日期值中提取给定数据。

HOUR（），MINUTE（），SECOND（） - 从时间值中提取给定数据。

DATEDIFF（A，B） - 确定两个日期之间的差异，通常用于计算年龄

SUBTIMES（A，B） - 确定两次之间的差异。

FROMDAYS（INT） - 将整数天数转换为日期值。


## 解释访问控制列表

ACL（访问控制列表）是与对象关联的权限列表。这个列表是 Mysql 服务器安全模型的基
础，它有助于排除用户无法连接的问题。

Mysql 将 ACL（也称为授权表）缓存在内存中。当用户尝试认证或运行命令时，Mysql 会按
照预定的顺序检查 ACL 的认证信息和权限。

## MYSQL 支持事务吗？

在缺省模式下，MYSQL 是 autocommit 模式的，所有的数据库更新操作都会即时提交，所
以在缺省情况下，mysql 是不支持事务的。

但是如果你的 MYSQL 表类型是使用 InnoDB Tables 或 BDB tables 的话，你的 MYSQL 就可以
使用事务处理,使用 SET AUTOCOMMIT=0 就可以使 MYSQL 允许在非 autocommit 模式，在非
autocommit 模式下，你必须使用 COMMIT 来提交你的更改，或者用 ROLLBACK 来回滚你的
更改。

示例如下：
一
START TRANSACTION;

SELECT @A:=SUM(salary) FROM table1 WHERE type=1;

UPDATE table2 SET summmary=@A WHERE type=1;

COMMIT;
## mysql 里记录货币用什么字段类型好
NUMERIC 和 DECIMAL 类型被 Mysql 实现为同样的类型，这在 SQL92 标准允许。他们被用于
保存值，该值的准确精度是极其重要的值，例如与金钱有关的数据。当声明一个类是这些
类型之一时，精度和规模的能被(并且通常是)指定；点击这里有一套最全阿里面试题总
结。

例如：
salary DECIMAL(9,2)
在这个例子中，9(precision)代表将被用于存储值的总的小数位数，而 2(scale)代表将被用于
存储小数点后的位数。

因此，在这种情况下，能被存储在 salary 列中的值的范围是从-9999999.99 到 9999999.99。 在 ANSI/ISO SQL92 中，句法 DECIMAL(p)等价于 DECIMAL(p,0)。

同样，句法 DECIMAL 等价于 DECIMAL(p,0)，这里实现被允许决定值 p。Mysql 当前不支持
DECIMAL/NUMERIC 数据类型的这些变种形式的任一种。

这一般说来不是一个严重的问题，因为这些类型的主要益处得自于明显地控制精度和规模
的能力。

DECIMAL 和 NUMERIC 值作为字符串存储，而不是作为二进制浮点数，以便保存那些值的小
数精度。

一个字符用于值的每一位、小数点(如果 scale>0)和“-”符号(对于负值)。如果 scale 是 0，
DECIMAL 和 NUMERIC 值不包含小数点或小数部分。

DECIMAL 和 NUMERIC 值得最大的范围与 DOUBLE 一样，但是对于一个给定的 DECIMAL 或
NUMERIC 列，实际的范围可由制由给定列的 precision 或 scale 限制。

当这样的列赋给了小数点后面的位超过指定 scale 所允许的位的值，该值根据 scale 四舍五
入。

当一个 DECIMAL 或 NUMERIC 列被赋给了其大小超过指定(或缺省的）precision 和 scale 隐含
的范围的值，Mysql 存储表示那个范围的相应的端点值。

我希望本文可以帮助你提升技术水平。那些，感觉学的好难，甚至会令你沮丧的人，别担
心，我认为，如果你愿意试一试本文介绍的几点，会向前迈进，克服这种感觉。这些要点
也许对你不适用，但你会明确一个重要的道理：接受自己觉得受困这个事实是摆脱这个困
境的第一步。

## MYSQL 数据表在什么情况下容易损坏？
服务器突然断电导致数据文件损坏。

强制关机，没有先关闭 mysql 服务等。

## mysql 有关权限的表都有哪几个？
Mysql 服务器通过权限表来控制用户对数据库的访问，权限表存放在 mysql 数据库里，由
mysql_install_db 脚本初始化。这些权限表分别 user，db，table_priv，columns_priv 和
host。

## Mysql 中有哪几种锁？
MyISAM 支持表锁，InnoDB 支持表锁和行锁，默认为行锁

表级锁：开销小，加锁快，不会出现死锁。锁定粒度大，发生锁冲突的概率最高，并发量
最低

行级锁：开销大，加锁慢，会出现死锁。锁力度小，发生锁冲突的概率小，并发度最高


## 数据库三范式是什么?
1. 第一范式（1NF）：字段具有原子性,不可再分。(所有关系型数据库系
统都满足第一范式数据库表中的字段都是单一属性的，不可再分)

2. 第二范式（2NF）是在第一范式（1NF）的基础上建立起来的，即满足
第二范式（2NF）必须先满足第一范式（1NF）。要求数据库表中的每
个实例或行必须可以被惟一地区分。通常需要为表加上一个列，以存储
各个实例的惟一标识。这个惟一属性列被称为主关键字或主键。

3. 满足第三范式（3NF）必须先满足第二范式（2NF）。简而言之，第三
范式（3NF）要求一个数据库表中不包含已在其它表中已包含的非主关
键字信息。 >所以第三范式具有如下特征： 
>>1. 每一列只有一个值
 >>2. 每一行都能区分。
  >>3. 每一个表都不包含其他表已经包含的非主关键字信息。

## 有哪些数据库优化方面的经验?
1. 用 PreparedStatement， 一般来说比 Statement 性能高：一个 sql
发给服务器去执行，涉及步骤：语法检查、语义分析， 编译，缓存。
2. 有外键约束会影响插入和删除性能，如果程序能够保证数据的完整性，
那在设计数据库时就去掉外键。
3. 表中允许适当冗余，譬如，主题帖的回复数量和最后回复时间等
4. UNION ALL 要比 UNION 快很多，所以，如果可以确认合并的两个结
果集中不包含重复数据且不需要排序时的话，那么就使用 UNION
ALL。 
>>UNION 和 UNION ALL 关键字都是将两个结果集合并为一
个，但这两者从使用和效率上来说都有所不同。
 >1. 对重复结果的处
理：UNION 在进行表链接后会筛选掉重复的记录，Union All 不会去除
重复记录。
 >2. 对排序的处理：Union 将会按照字段的顺序进行排
序；UNION ALL 只是简单的将两个结果合并后就返回。

## 请简述常用的索引有哪些种类?
1. 普通索引: 即针对数据库表创建索引
2. 唯一索引: 与普通索引类似，不同的就是：MySQL 数据库索引列的值
必须唯一，但允许有空值
3. 主键索引: 它是一种特殊的唯一索引，不允许有空值。一般是在建表的
时候同时创建主键索引
4. 组合索引: 为了进一步榨取 MySQL 的效率，就要考虑建立组合索引。
即将数据库表中的多个字段联合起来作为一个组合索引。

## 以及在 mysql 数据库中索引的工作机制是什么？
数据库索引，是数据库管理系统中一个排序的数据结构，以协助快速查询、更
新数据库表中数据。索引的实现通常使用 B 树及其变种 B+树

## MySQL 的基础操作命令:
1. MySQL 是否处于运行状态:Debian 上运行命令 service mysql
status，在 RedHat 上运行命令 service mysqld status

2. 开启或停止 MySQL 服务 :运行命令 service mysqld start 开启服
务；运行命令 service mysqld stop 停止服务

3. Shell 登入 MySQL: 运行命令 mysql -u root -p

4. 列出所有数据库:运行命令 show databases;

5. 切换到某个数据库并在上面工作:运行命令 use databasename; 进入
名为 databasename 的数据库

6. 列出某个数据库内所有表: show tables;

7. 获取表内所有 Field 对象的名称和类型 :describe table_name;

## mysql 的复制原理以及流程。
Mysql 内建的复制功能是构建大型，高性能应用程序的基础。将 Mysql 的数据
分布到多个系统上去，这种分布的机制，是通过将 Mysql 的某一台主机的数据
复制到其它主机（slaves）上，并重新执行一遍来实现的。 复制过程中一
个服务器充当主服务器，而一个或多个其它服务器充当从服务器。主服务器将
更新写入二进制日志文件，并维护文件的一个索引以跟踪日志循环。这些日志
可以记录发送到从服务器的更新。 当一个从服务器连接主服务器时，它通知主
服务器在日志中读取的最后一次成功更新的位置。从服务器接收从那时起发生
的任何更新，然后封锁并等待主服务器通知新的更新。 过程如下
 
 1. 主服务器把更新记录到二进制日志文件中。
 
 2. 从服务器把主服务器的二进制日志拷贝到自己的中继日志（replay log）中。 

3. 从服务器重做中继日志中的时间，把更新应用到自己的数据库上。

## mysql 支持的复制类型?
1. 基于语句的复制： 在主服务器上执行的 SQL 语句，在从服务器上执行
同样的语句。MySQL 默认采用基于语句的复制，效率比较高。 一旦发
现没法精确复制时，会自动选着基于行的复制。

2. 基于行的复制：把改变的内容复制过去，而不是把命令在从服务器上执
行一遍. 从 mysql5.0 开始支持

3. 混合类型的复制: 默认采用基于语句的复制，一旦发现基于语句的无法
精确的复制时，就会采用基于行的复制。

## mysql 中 myisam 与 innodb 的区别？
1. 事务支持 
> MyISAM：强调的是性能，每次查询具有原子性,其执行数
度比 InnoDB 类型更快，但是不提供事务支持。 
> InnoDB：提供事
务支持事务，外部键等高级数据库功能。 具有事务(commit)、回滚
(rollback)和崩溃修复能力(crash recovery capabilities)的事务安全
(transaction-safe (ACID compliant))型表。
2. InnoDB 支持行级锁，而 MyISAM 支持表级锁. >> 用户在操作
myisam 表时，select，update，delete，insert 语句都会给表自动
加锁，如果加锁以后的表满足 insert 并发的情况下，可以在表的尾部插
入新的数据。

3. InnoDB 支持 MVCC, 而 MyISAM 不支持

4. InnoDB 支持外键，而 MyISAM 不支持

5. 表主键 
> MyISAM：允许没有任何索引和主键的表存在，索引都是保
存行的地址。 
> InnoDB：如果没有设定主键或者非空唯一索引，就会
自动生成一个 6 字节的主键(用户不可见)，数据是主索引的一部分，附
加索引保存的是主索引的值。

6. InnoDB 不支持全文索引，而 MyISAM 支持。

7. 可移植性、备份及恢复
 > MyISAM：数据是以文件的形式存储，所以
在跨平台的数据转移中会很方便。在备份和恢复时可单独针对某个表进
行操作。 
> InnoDB：免费的方案可以是拷贝数据文件、备份
binlog，或者用 mysqldump，在数据量达到几十 G 的时候就相对痛
苦了

8. 存储结构 
> MyISAM：每个 MyISAM 在磁盘上存储成三个文件。第一
个文件的名字以表的名字开始，扩展名指出文件类型。.frm 文件存储表
定义。数据文件的扩展名为.MYD (MYData)。索引文件的扩展名
是.MYI (MYIndex)。 
> InnoDB：所有的表都保存在同一个数据文件
中（也可能是多个文件，或者是独立的表空间文件），InnoDB 表的大
小只受限于操作系统文件的大小，一般为 2GB。
## mysql 中 varchar 与 char 的区别以及 varchar(50)中的 50 代表的涵义？
1. varchar 与 char 的区别: char 是一种固定长度的类型，varchar 则是
一种可变长度的类型.

2. varchar(50)中 50 的涵义 : 最多存放 50 个字节

3. int（20）中 20 的涵义: int(M)中的 M indicates the maximum
display width (最大显示宽度)for integer types. The maximum
legal display width is 255.

## MySQL 中 InnoDB 支持的四种事务隔离级别名称，以及逐级之间的区别？
1. Read Uncommitted（读取未提交内容） 
>> 在该隔离级别，所有事
务都可以看到其他未提交事务的执行结果。本隔离级别很少用于实际应
用，因为它的性能也不比其他级别好多少。读取未提交的数据，也被称
之为脏读（Dirty Read）。

2. Read Committed（读取提交内容） 
>> 这是大多数数据库系统的默
认隔离级别（但不是 MySQL 默认的）。它满足了隔离的简单定义：一
个事务只能看见已经提交事务所做的改变。这种隔离级别也支持所谓的
不可重复读（Nonrepeatable Read），因为同一事务的其他实例在该
实例处理其间可能会有新的 commit，所以同一 select 可能返回不同结
果。
3. Repeatable Read（可重读） 
>> 这是 MySQL 的默认事务隔离级
别，它确保同一事务的多个实例在并发读取数据时，会看到同样的数据
行。不过理论上，这会导致另一个棘手的问题：幻读（Phantom
Read）。简单的说，幻读指当用户读取某一范围的数据行时，另一个事
务又在该范围内插入了新行，当用户再读取该范围的数据行时，会发现
有新的“幻影” 行。InnoDB 和 Falcon 存储引擎通过多版本并发控制
（MVCC，Multiversion Concurrency Control 间隙锁）机制解决了
该问题。注：其实多版本只是解决不可重复读问题，而加上间隙锁（也
就是它这里所谓的并发控制）才解决了幻读问题。
4. Serializable（可串行化） 
>> 这是最高的隔离级别，它通过强制事务
排序，使之不可能相互冲突，从而解决幻读问题。简言之，它是在每个
读的数据行上加上共享锁。在这个级别，可能导致大量的超时现象和锁
竞争。 
## 表中有大字段 X（例如：text 类型），且字段 X 不会经常更新，以读为为主，将该字段拆成子表好处是什么？
如果字段里面有大字段（text,blob)类型的，而且这些字段的访问并不多，这
时候放在一起就变成缺点了。 MYSQL 数据库的记录存储是按行存储的，数据
块大小又是固定的（16K），每条记录越小，相同的块存储的记录就越多。此
时应该把大字段拆走，这样应付大部分小字段的查询时，就能提高效率。当需
要查询大字段时，此时的关联查询是不可避免的，但也是值得的。拆分开后，
对字段的 UPDAE 就要 UPDATE 多个表了

## MySQL 中 InnoDB 引擎的行锁是通过加在什么上完成（或称实现）的？
InnoDB 行锁是通过给索引上的索引项加锁来实现的，这一点 MySQL 与
Oracle 不同，后者是通过在数据块中对相应数据行加锁来实现的。InnoDB 这
种行锁实现特点意味着：只有通过索引条件检索数据，InnoDB 才使用行级
锁，否则，InnoDB 将使用表锁！
## MySQL 中控制内存分配的全局参数，有哪些？
1. Keybuffersize： 
 >  keybuffersize 指定索引缓冲区的大小，
它决定索引处理的速度，尤其是索引读的速度。通过检查状态值
Keyreadrequests 和 Keyreads，可以知道 keybuffersize 设置是否
合理。比例 keyreads /keyreadrequests 应该尽可能的低，至少是
1:100，1:1000 更好（上述状态值可以使用 SHOW STATUS LIKE
‘keyread%'获得）。
> keybuffersize 只对 MyISAM 表起作用。
即使你不使用 MyISAM 表，但是内部的临时磁盘表是 MyISAM 表，也
要使用该值。可以使用检查状态值 createdtmpdisktables 得知详情。
对于 1G 内存的机器，如果不使用 MyISAM 表，推荐值是 16M（8-
64M）
 > keybuffersize 设置注意事项 
 
 >>>1. 单个 keybuffer 的
大小不能超过 4G，如果设置超过 4G，就有可能遇到下面 3 个
bug: >>>>> http://bugs.mysql.com/bug.php?id=29446 <br
/> >>>>> http://bugs.mysql.com/bug.php?id=29419 <br
/> >>>>> http://bugs.mysql.com/bug.php?id=5731 <br
>>>2. 建议 keybuffer 设置为物理内存的 1/4(针对 MyISAM 引 擎)，甚至是物理内存的 30%~40%，如果 keybuffersize 设置太大，
系统就会频繁的换页，降低系统性能。因为 MySQL 使用操作系统的缓
存来缓存数据，所以我们得为系统留够足够的内存；在很多情况下数据
要比索引大得多。 
>>>3. 如果机器性能优越，可以设置多个
keybuffer,分别让不同的 keybuffer 来缓存专门的索引

2. innodbbufferpool_size 
> 表示缓冲池字节大小，InnoDB 缓存
表和索引数据的内存区域。mysql 默认的值是 128M。最大值与你的
CPU 体系结构有关，在 32 位操作系统，最大值是 4294967295
(2^32-1) ，在 64 位操作系统，最大值为
18446744073709551615 (2^64-1)。 

> 在 32 位操作系统中，
CPU 和操作系统实用的最大大小低于设置的最大值。如果设定的缓冲池
的大小大于 1G，设置 innodbbufferpoolinstances 的值大于 1. 

> 数据读写在内存中非常快, innodbbufferpoolsize 减少了对磁盘的读
写。 当数据提交或满足检查点条件后才一次性将内存数据刷新到磁盘
中。然而内存还有操作系统或数据库其他进程使用, 一般设置 buffer
pool 大小为总内存的 3/4 至 4/5。 若设置不当, 内存使用可能浪费
或者使用过多。 对于繁忙的服务器, buffer pool 将划分为多个实例以
提高系统并发性, 减少线程间读写缓存的争用。buffer pool 的大小首
先受 innodbbufferpool_instances 影响, 当然影响较小。

3. querycachesize 
> 当 mysql 接收到一条 select 类型的 query
时，mysql 会对这条 query 进行 hash 计算而得到一个 hash 值，然后
通过该 hash 值到 query cache 中去匹配，如果没有匹配中，则将这个
hash 值存放在一个 hash 链表中，同时将 query 的结果集存放进
cache 中，存放 hash 值的链表的每一个 hash 节点存放了相应 query
结果集在 cache 中的地址，以及该 query 所涉及到的一些 table 的相
关信息；如果通过 hash 值匹配到了一样的 query，则直接将 cache 中
相应的 query 结果集返回给客户端。如果 mysql 任何一个表中的任何
一条数据发生了变化，便会通知 query cache 需要与该 table 相关的
query 的 cache 全部失效，并释放占用的内存地址。 

> query cache优缺点
 
>> query 语句的 hash 计算和 hash 查找带来的资源消
耗。mysql 会对每条接收到的 select 类型的 query 进行 hash 计算然
后查找该 query 的 cache 是否存在，虽然 hash 计算和查找的效率已
经足够高了，一条 query 所带来的消耗可以忽略，但一旦涉及到高并
发，有成千上万条 query 时，hash 计算和查找所带来的开销就的重视
了；
 
>> query cache 的失效问题。如果表变更比较频繁，则会造
成 query cache 的失效率非常高。表变更不仅仅指表中的数据发生变
化，还包括结构或者索引的任何变化；

>>对于不同 sql 但同一结
果集的 query 都会被缓存，这样便会造成内存资源的过渡消耗。sql 的
字符大小写、空格或者注释的不同，缓存都是认为是不同的 sql（因为
他们的 hash 值会不同）；

>>相关参数设置不合理会造成大量内
存碎片，相关的参数设置会稍后介绍。

readbuffersize
 >是 MySQL 读入缓冲区大小。对表进行顺序扫描
的请求将分配一个读入缓冲区，MySQL 会为它分配一段内存缓冲区。
readbuffersize 变量控制这一缓冲区的大小。如果对表的顺序扫描请求
非常频繁，并且你认为频繁扫描进行得太慢，可以通过增加该变量值以
及内存缓冲区大小提高其性能。

## 若一张表中只有一个字段 VARCHAR(N)类型，utf8 编码，则 N 最大值为多少(精确到数量级即可)?
由于 utf8 的每个字符最多占用 3 个字节。而 MySQL 定义行的长度不能超过
65535，因此 N 的最大值计算方法为：(65535-1-2)/3。减去 1 的原因是实
际存储从第二个字节开始，减去 2 的原因是因为要在列表长度存储实际的字符
长度，除以 3 是因为 utf8 限制：每个字符最多占用 3 个字节。

##  [SELECT *] 和[SELECT 全部字段]的 2 种写法有何优缺点?

1. 前者要解析数据字典，后者不需要

2. 结果输出顺序，前者与建表列顺序相同，后者按指定字段顺序。

3. 表字段改名，前者不需要修改，后者需要改

4. 后者可以建立索引进行优化，前者无法优化

5. 后者的可读性比前者要高

## HAVNG 子句 和 WHERE 的异同点?
1. 语法上：where 用表中列名，having 用 select 结果别名

2. 影响结果范围：where 从表读出数据的行数，having 返回客户端的行
数

3. 索引：where 可以使用索引，having 不能使用索引，只能在临时结果
集操作

4. where 后面不能使用聚集函数，having 是专门使用聚集函数的。

## MySQL 当记录不存在时 insert,当记录存在时 update，语句怎么写？
```
INSERT INTO table (a,b,c) VALUES (1,2,3) ON DUPLICATE KEY
UPDATE c=c+1;
18.MySQL 的 insert 和 update 的 select 语句语法
`SQL insert into student (stuid,stuname,deptid) select 10,'xzm',3
from student where stuid > 8;
update student a inner join student b on b.stuID=10 set
a.stuname=concat(b.stuname, b.stuID) where a.stuID=10 ; `
```

# 其他
喜欢的同学点个星星，打赏奖励一下博主！！！

 <img src="https://img-blog.csdnimg.cn/20210414173956371.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tlMzY5MDkzNDU3,size_16,color_FFFFFF,t_70" width = "200" height = "250" alt="图片名称" align=center />
 <img src="https://img-blog.csdnimg.cn/20210414174007800.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tlMzY5MDkzNDU3,size_16,color_FFFFFF,t_70" width = "200" height = "250" alt="图片名称" align=center />
 