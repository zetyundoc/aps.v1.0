# VACUUM（企业版支持）
<br/>**命令**
```SQL
VACUUM [ ( { FULL | FREEZE | VERBOSE | ANALYZE } [, ...] ) ] [ table_name [ (column_name [, ...] ) ] ]
VACUUM [ FULL ] [ FREEZE ] [ VERBOSE ] [ table_name ]
VACUUM [ FULL ] [ FREEZE ] [ VERBOSE ] ANALYZE [ table_name [ (column_name [, ...] ) ] ]
```
<br/>**描述**

VACUUM回收死行占据的存储空间。<!--在一般的EdsSQL 操作里，那些已经 DELETE 的行或者被 UPDATE 过后过时的行并没有从它们所属的表中物理删除； 在完成VACUUM之前它们仍然存在。因此有必要周期地运行VACUUM， 特别是在经常更新的表上。-->

如果没有参数，VACUUM处理当前用户有权限vacuum的当前数据库里的每个表， 如果有参数，VACUUM只处理那个表。

VACUUM ANALYZE先执行一个VACUUM 然后是给每个选定的表执行一个ANALYZE。对于日常维护脚本而言， 这是一个很方便的组合。参阅ANALYZE获取更多有关其处理的细节。

简单的VACUUM(没有FULL)只是简单地回收空间并且令其可以再次使用。 这种形式的命令可以和对表的普通读写并发操作，因为没有请求排他锁。然而， 额外的空间并不返回到操作系统（在大多数情况下）；仅保持在相同的表中可重用。 VACUUM FULL将表的全部内容重写到一个没有任何多余空间的新磁盘文件中， 允许未使用的空间返回到操作系统中。这种形式要慢许多并且在处理的时候需要在表上施加一个排它锁。

<!--当选项列表被括号括起来时，该选项可以任意顺序来写。没有圆括号，选项必须按以上显示的顺序指定。 加圆括号的语法在postgreSQL 9.0中添加；不加括号的语法已经废弃了。-->

<br/>**参数**

FULL：选择"完全"清理，这样可以恢复更多的空间，但是花的时间更多并且在表上施加了排它锁。 该方法也需要额外的磁盘空间，因为它写了一个表的新拷贝并且不释放旧的拷贝，直到操作完成。 通常这应该只用于当一个大量的空间需要在这个表中回收时。

FREEZE：选择激进的行"冻结"。指定FREEZE相当于执行 VACUUM时将vacuum_freeze_min_age参数设为零。

VERBOSE：为每个表打印一份详细的清理工作报告。

ANALYZE：更新用于优化器的统计信息，以决定执行查询的最有效方法。

*table_name*：要清理的表的名称(可以有模式修饰)。缺省时是当前数据库中的所有表。

*column_name*：要分析的具体的字段名称。缺省是所有字段。若指定一个字段列表，就暗含ANALYZE。

<br/>**输出**

如果指定了VERBOSE，那么VACUUM将发出处理过程中的信息， 以表明当前正在处理哪个表。各种有关这些表的统计也会打印出来。

<br/>**注意**

要清空一个表，一个人必须通常是表的所有者或是一个超级用户。然而， 数据库所有者允许清空他们的数据库中的所有表，除了共享目录。 （对共享目录的限制意味着一个真正的数据库范围的VACUUM仅能由超级用户执行。） VACUUM将跳过调用用户没有权限清空的任何表。

VACUUM不能在事务块内执行。

对于有GIN索引的表，VACUUM（以任何形式） 也完成任何挂起索引插入内容，通过移动挂起索引条目到主GIN索引结构中的相应位置。

建议生产数据库经常清理(至少每晚一次)，以保证不断地删除死行。尤其是在增删了大量记录之后， 对受影响的表执行VACUUM ANALYZE命令是一个很好的习惯。 这样做将更新系统目录为最近的更改，并且允许EdsSQL 查询优化器在规划用户查询时有更好的选择。

不建议日常使用FULL选项，但是可以在特殊情况下使用。 一个例子就是在你删除或更新了一个表的大部分行之后， 希望从物理上缩小该表以减少磁盘空间占用并且允许更快的表扫描。VACUUM FULL 通常要比单纯的VACUUM收缩更多的表尺寸。

VACUUM导致 I/O 流量增加，可能会导致其它活动会话的性能恶劣。因此， 有时候会建议使用基于开销的 vacuum 延迟特性。 

EdsSQL包含一个"autovacuum"设施， 它可以自动进行日常的 vacuum 维护。

<br/>**示例**

下面是一个在蜕变(regression)数据库里某个表上执行VACUUM的一个例子：
```SQL
regression=# VACUUM (VERBOSE, ANALYZE) onek;
INFO:  vacuuming "public.onek"
INFO:  index "onek_unique1" now contains 1000 tuples in 14 pages
DETAIL:  3000 index tuples were removed.
0 index pages have been deleted, 0 are currently reusable.
CPU 0.01s/0.08u sec elapsed 0.18 sec.
INFO:  index "onek_unique2" now contains 1000 tuples in 16 pages
DETAIL:  3000 index tuples were removed.
0 index pages have been deleted, 0 are currently reusable.
CPU 0.00s/0.07u sec elapsed 0.23 sec.
INFO:  index "onek_hundred" now contains 1000 tuples in 13 pages
DETAIL:  3000 index tuples were removed.
0 index pages have been deleted, 0 are currently reusable.
CPU 0.01s/0.08u sec elapsed 0.17 sec.
INFO:  index "onek_stringu1" now contains 1000 tuples in 48 pages
DETAIL:  3000 index tuples were removed.
0 index pages have been deleted, 0 are currently reusable.
CPU 0.01s/0.09u sec elapsed 0.59 sec.
INFO:  "onek": removed 3000 tuples in 108 pages
DETAIL:  CPU 0.01s/0.06u sec elapsed 0.07 sec.
INFO:  "onek": found 3000 removable, 1000 nonremovable tuples in 143 pages
DETAIL:  0 dead tuples cannot be removed yet.
There were 0 unused item pointers.
0 pages are entirely empty.
CPU 0.07s/0.39u sec elapsed 1.56 sec.
INFO:  analyzing "public.onek"
INFO:  "onek": 36 pages, 1000 rows sampled, 1000 estimated total rows
VACUUM
```
<br/>**兼容性**

SQL 标准里没有VACUUM语句。

