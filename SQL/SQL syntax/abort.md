# ABORT


**命令**

```SQL
ABORT [ WORK | TRANSACTION ]
```

    
<br/>**描述**

ABORT回滚当前事务并且撤销所有当前事务中所做的更改。该命令和标准SQL命令的ROLLBACK的行为完全一致。

<br/>**参数**

WORK<BR/>
TRANSACTION

<br/>**注意**

使用COMMIT成功的结束一个事务。<br>
在事务外部发出ABORT不会造成损害，但会产生一个告警消息。

<br/>**示例**

放弃全部变更：  

     ABORT:

<br/>**兼容性**

该命令等价于标准SQL命令的ROLLBACK命令。