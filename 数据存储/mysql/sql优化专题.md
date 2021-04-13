# sql优化

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
````
read uncommited ：读到未提交数据
read committed：脏读，不可重复读
repeatable read：可重读
serializable ：串行事物
````
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
````
SET
BLOB
ENUM
CHAR
TEXT
VARCHAR
````
## 如何获取当前的 Mysql 版本？
SELECT VERSION();用于获取当前 Mysql 的版本。

## Mysql 中使用什么存储引擎？
存储引擎称为表类型，数据使用各种技术存储在文件中。
技术涉及：
````
Storage mechanism
Locking levels
Indexing
Capabilities and functions.
````
## Mysql 驱动程序是什么？
以下是 Mysql 中可用的驱动程序：
````
PHP 驱动程序
JDBC 驱动程序
ODBC 驱动程序
CWRAPPER
PYTHON 驱动程序
PERL 驱动程序
RUBY 驱动程序
CAP11PHP 驱动程序
````
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
段，以适应不同长度的数据类型
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
每个 MyISAM 表格以三种格式存储在磁盘上

·“.frm”文件存储表定义

·数据文件具有“.MYD”（MYData）扩展名

索引文件具有“.MYI”（MYIndex）扩展名

## Mysql 中有哪些不同的表格？
共有 5 种类型的表格：
````
MyISAM
Heap
Merge
INNODB
ISAM
````
MyISAM 是 Mysql 的默认存储引擎。

## ISAM 是什么？
ISAM 简称为索引顺序访问方法。它是由 IBM 开发的，用于在磁带等辅助存储系统上存储和
检索数据。

## InnoDB 是什么？
lnnoDB 是一个由 Oracle 公司开发的 Innobase Oy 事务安全存储引擎。

## Mysql 如何优化 DISTINCT？
DISTINCT 在所有列上转换为 GROUP BY，并与 ORDER BY 子句结合使用。
1
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
````
DATABASE
EVENT
FUNCTION
INDEX
PROCEDURE
TABLE
TRIGGER
USER
VIEW
````
## Mysql 表中允许有多少个 TRIGGERS？ 在 Mysql 表中允许有六个触发器，如下：
````
BEFORE INSERT
AFTER INSERT
BEFORE UPDATE
AFTER UPDATE
BEFORE DELETE
AFTER DELETE
````
## 什么是非标准字符串类型？
以下是非标准字符串类型：
````
TINYTEXT
TEXT
MEDIUMTEXT
LONGTEXT
````
## 什么是通用 SQL 函数？
CONCAT(A, B) - 连接两个字符串值以创建单个字符串输出。通常用于将两个或多个字段合
并为一个字段。
````
FORMAT(X, D)- 格式化数字 X 到 D 有效数字。
CURRDATE(), CURRTIME()- 返回当前日期或时间。
NOW（） - 将当前日期和时间作为一个值返回。
MONTH（），DAY（），YEAR（），WEEK（），WEEKDAY（） - 从日期值中提取给定数据。
HOUR（），MINUTE（），SECOND（） - 从时间值中提取给定数据。
DATEDIFF（A，B） - 确定两个日期之间的差异，通常用于计算年龄
SUBTIMES（A，B） - 确定两次之间的差异。
FROMDAYS（INT） - 将整数天数转换为日期值。
````
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

````
START TRANSACTION;
SELECT @A:=SUM(salary) FROM table1 WHERE type=1;
UPDATE table2 SET summmary=@A WHERE type=1;
COMMIT;
````
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