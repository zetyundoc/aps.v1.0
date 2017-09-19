# TRUNCATE（企业版支持）
<br/>**命令**
```SQL
TRUNCATE [ TABLE ] [ ONLY ] name [ * ] [, ... ]
    [ RESTART IDENTITY | CONTINUE IDENTITY ] [ CASCADE | RESTRICT ]
```
<br/>**描述**

TRUNCATE快速地从一堆表中删除所有行。<!--它和在每个表上进行无条件的 DELETE有同样的效果，不过因为它不做表扫描，因而快得多。另外，--> 它快速回收磁盘空间，而不是请求一个随后的VACUUM操作。 在大表上最有用。

<br/>**参数**

*name*：要清空的表名字(可以有模式修饰)。如果在表名前声明了ONLY， 那么只有那个表会被清空。如果没有声明ONLY，这个表以及其所有子表 （若有）会被清空。可选地,可以在表名后声明*以明确指定包括子表。

RESTART IDENTITY：截断表中的序列字段的序列值将被重置。

CONTINUE IDENTITY：不要改变序列的值。此为缺省。

CASCADE：级联清空所有在该表上有外键引用的表，或者由于CASCADE而被添加到组中的表。

RESTRICT：如果有外键引用数据并且引用表没有在命令行中列出，则拒绝清空。这是缺省。

<br/>**注意**

你必须对表有TRUNCATE权限以清空它。

TRUNCATE在在操作的每个表上请求一个ACCESS EXCLUSIVE锁， 这种锁会阻塞表上的所有其他并发操作。当声明了RESTART IDENTITY时， 任何被重启的序列也会被排他锁锁住。<!--如果需要对一个表并发访问， 那么应该使用DELETE命令。-->

如果从其它表有到某个表的外键引用，那么就不能对该表使用TRUNCATE， 除非这些表在同一个命令中也被清空。在这种情况下检查有效性要求进行表扫描， 而TRUNCATE并不做这样的事情。CASCADE 选项可以用于级联包含所有依赖表，但是使用此选项必须十分小心，否则可能丢失原本不想丢失的数据。

<!--TRUNCATE不会触发任何在该表上的ON DELETE触发器。 但它会触发ON TRUNCATE触发器。-->如果任何表上定义有ON TRUNCATE 触发器，则所有的BEFORE TRUNCATE 触发器会在任何截断发生之前触发，而所有AFTER TRUNCATE 触发器会在最后一个截断执行后和任何序列重设后被触发。该触发器会以表执行的顺序触发 （首先是命令中列出的，然后是任何基于级联添加的）。

<p style="color: #c09853;background: #fcf8e3 none repeat scroll 0% 0%; font-size:13px;" >注意：TRUNCATE不是mvcc 安全的操作（参考mvcc的文档）。在截断之后，所有并发的事务看到的该表都是空的，即使他们在截断发生前使用快照。 这将是一个针对截断发生之前没有访问截断表的事务的问题，在事务中对截断表的任何访问至少产生一个 ACCESS SHARE锁，这将阻塞TRUNCATE直到事务完成。 所以截断不会导致在相同表上连续查询时表内容上的明显不一致， 但会导致截断表和数据库中其他表在内容上可见的不一致性。</p>

TRUNCATE在事务中断表数据是安全可的：只要开启的事物不提交，则事物会安全回滚。

当声明了RESTART IDENTITY时，隐含的ALTER SEQUENCE RESTART 操作也会事务性的完成；也就是，如果包围的事务没有提交，它们将回滚。 这不同于ALTER SEQUENCE RESTART的正常行为。要知道， 如果在序列重启之后与事务回滚之前执行了额外的序列操作， 那么这些操作的影响也会回滚，但是它们在currval()上的影响不会回滚； 也就是，在事务之后，currval()将继续反映在失败事务中获得的最后序列值， 即使序列本身可能与其不再一致。这类似于currval()在一个失败事务后的普通行为。

<br/>**示例**

清空bigtable和fattable表：
```SQL
TRUNCATE bigtable, fattable;
```
同样地，也重置任何相关的序列生成器:
```SQL
TRUNCATE bigtable, fattable RESTART IDENTITY;
```
清空othertable表，并且级联清空所有通过外键约束引用 othertable的表：
```SQL
TRUNCATE othertable CASCADE;
```
<br/>**兼容性**

SQL:2008标准包括一个有语法TRUNCATE TABLE tablename 的TRUNCATE命令。CONTINUE IDENTITY/RESTART IDENTITY 也出现在那个标准中，但是有轻微的不同和相关的含义。该命令的一些并发行为通过标准定义实现的， 所以，如果必要，应该考虑上面的说明并与其他实现比较。