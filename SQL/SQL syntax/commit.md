# COMMIT

**命令**

```SQL
COMMIT [ WORK | TRANSACTION ]```

<br/>**描述**

COMMIT提交当前事务。 所有事务的更改都将被其它事务可见， 而且保证即使有崩溃发生时的数据有效性。

<br/>**参数**

WORK
TRANSACTION

可选关键字。 

<br/>**注意**

使用ROLLBACK语句退出事务。

在一个事务外部发出COMMIT不会有问题，但是将产生一个警告消息。

<br/>**示例**

提交当前事务以让所有变更永久化：

```SQL
COMMIT;```

<br/>**兼容性**

SQL标准只声明了COMMIT和COMMIT WORK两种形式。 否则这条命令与标准完全兼容。

<br/>**参见**

BEGIN, ROLLBACK