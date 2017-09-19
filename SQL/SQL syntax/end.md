# END

<br/>**命令**
```SQL
END [ WORK | TRANSACTION ]
```
<br/>**描述**

END提交当前事务。 所有当前事务做的修改都可被其它事务看到并且保证崩溃情况下的持续性。 它是一个EdsSQL的扩展，等效于COMMIT。

<br/>**参数**

WORK<br/>
TRANSACTION

可选关键字，没有作用。

<br/>**注意**

用ROLLBACK退出事务。

在一个事务块之外发出END不会有什么损害，但是它会生成一个警告消息。

<br/>**示例**

提交当前事务，令所有改变生效：
```SQL
END;
```
<br/>**兼容性**

END是EdsSQL的扩展， 提供与COMMIT相同的功能，后者是 SQL 标准声明的语句。

<br/>**参见**

BEGIN, COMMIT, ROLLBACK