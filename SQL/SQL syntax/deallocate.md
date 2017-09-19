# DEALLOCATE（企业版支持）
<br/>**命令**
```SQL
DEALLOCATE [ PREPARE ] { name | ALL }
```
<br/>**描述**

DEALLOCATE用于删除前面编写的预备语句。 如果你没有明确删除一个预备语句，那么它将在会话结束的时候被删除。

<!--有关预备语句的更多信息。参阅PREPARE。-->

<br/>**参数**

PREPARE：这个关键字总被忽略。

*name*：将要删除的预备语句。

ALL：删除所有预备语句。

<br/>**兼容性**

SQL 包括一个DEALLOCATE语句，但它只是用于嵌入式 SQL 。

<br/>**参见**

EXECUTE<!--, PREPARE-->