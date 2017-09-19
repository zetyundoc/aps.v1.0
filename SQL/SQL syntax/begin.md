# BEGIN

**命令**

```SQL
BEGIN [ WORK | TRANSACTION ] [ transaction_mode [, ...] ]

transaction_mode可以是以下选项之一：

    ISOLATION LEVEL { SERIALIZABLE | REPEATABLE READ | READ COMMITTED | READ UNCOMMITTED }
    READ WRITE | READ ONLY
    [ NOT ] DEFERRABLE```

<br/>**描述**

BEGIN初始化一个事务块，也就是说所有BEGIN命令后的用户语句都将在一个事务里面执行直到给出一个明确的COMMIT或ROLLBACK。 缺省情况下(没有BEGIN)，EdsSQL以"autocommit"模式执行事务，也就是说，每个语句在其自身的事务中执行，并且在语句结束的时候执行了一次隐含的提交。 （如果执行成功则隐含地执行一个提交，否则执行回滚。）

在事务块里语句执行的明显快得多，因为事务开始/提交需要大量的CPU和磁盘活动。 在一个事务内部执行多条语句对于修改若干个相关的表的时候也是很有用的： 在所有相关的更新完成之前，其它会话看不到中间的状态。

如果指定了隔离级别、读/写模式或者是延迟模式，那么新事务将具有那些特征。 <!--就像执行了SET TRANSACTION一样。-->

<br/>**参数**

WORK<br/>
TRANSACTION

可选关键字。

READ COMMITTED：一条语句只能看到在它开始之前的数据。这是缺省。

REPEATABLE READ：当前事务中的所有语句只能看到在这次事务第一条查询或者修改数据的语句执行之前已经提交的数据。

SERIALIZABLE：当前事务中的所有语句只能看到在这次事务第一条查询或者修改数据的语句执行之前已经提交的数据。 如果当前并发的可串行化事务的读或写模式中任意一个事务（一次一个）执行创建不可能发生的情况， 那么其中的一个事务将会带有serialization_failure错误退出。

<!--SQL 标准还定义了另外一个级别，READ UNCOMMITTED。 在EdsSQL里READ UNCOMMITTED 被当作READ COMMITTED。

参考SET TRANSACTION获取这个语句的其它参数的含义。-->

<br/>**注意**

<!--START TRANSACTION有着和BEGIN一样的功能。-->

使用COMMIT或ROLLBACK结束一个事务块。

在一个现有事务块内部发出一个BEGIN将产生一个警告消息。 事务的状态将不会被影响。 要想在一个事务块里嵌套事务，请使用保存点功能<!--(参见SAVEPOINT)-->。

出于向下兼容考虑，在随后的transaction_modes之间的逗号可以忽略 。

<br/>**示例**

开始一个事务块：

```SQL
BEGIN;```

<br/>**兼容性**

BEGIN是EdsSQL语言的扩展。 <!--它等价于SQL标准中的START TRANSACTION命令，在其命令的资料中包含更多有关兼容性的信息。
DEFERRABLEtransaction_mode选项是EdsSQL的扩展。-->

顺便说一句，BEGIN关键字在嵌入SQL里用于不同目的。建议你在移植数据库应用时仔细检查事务的语意。

<br/>**参见**

COMMIT, ROLLBACK<!--, START TRANSACTION, SAVEPOINT-->