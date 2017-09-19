# ROLLBACK PREPARED（企业版支持）
<br/>**命令**
```SQL
ROLLBACK PREPARED transaction_id
```
<br/>**描述**

ROLLBACK PREPARED回滚一个处于准备好状态的事务。

<br/>**参数**

*transaction_id*：要回滚掉的事务的事务标识符。

<br/>**注意**

要想回滚掉一个预备事务，你必须要么是最初发起事务的用户，要么是超级用户。 但你不必在执行事务的同一个会话里。

这条命令不能在事务块里执行。预备事务将被马上回滚。

所有当前可用的预备事务都在系统视图 eds_prepared_xacts中列出。

<br/>**示例**

回滚一个使用事务标识符foobar标识的事务：
```SQL
ROLLBACK PREPARED 'foobar';
```
<br/>**兼容性**

ROLLBACK PREPARED是EdsSQL的扩展。 是为外部事务管理系统设计的，一些被标准(比如 X/Open XA)所覆盖， 但是SQL方面的这些系统是非标准化的。

<br/>**参见**

PREPARE TRANSACTION<!--, COMMIT PREPARED-->