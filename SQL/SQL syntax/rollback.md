# ROLLBACK
<br/>**命令**
```SQL
ROLLBACK [ WORK | TRANSACTION ]
```
<br/>**描述**

ROLLBACK回滚当前事务并取消当前事务中的所有更新。

<br/>**参数**

WORK<br/>
TRANSACTION

可选的关键字。没有作用。

<br/>**注意**

使用COMMIT语句将对事务进行提交。

如果不在一个事务内部发出ROLLBACK不会有问题，但是将抛出一个警告消息。

<br/>**示例**

取消所有更改：
```SQL
ROLLBACK;
```
<br/>**兼容性**

SQL 标准只声明了两种形式ROLLBACK和ROLLBACK WORK。 否则完全兼容。

<br/>**参见**

BEGIN, COMMIT<!--, ROLLBACK TO SAVEPOINT-->
