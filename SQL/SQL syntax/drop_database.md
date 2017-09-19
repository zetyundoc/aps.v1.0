# DROP DATABASE
<br/>**命令**
```SQL
DROP DATABASE [ IF EXISTS ] name
```
<br/>**描述**

DROP DATABASE删除一个数据库。删除一个现存数据库的目录入口并且删除包含数据的目录。 只有数据库所有者能够执行这条命令。还有，如果你或者任何其他人正在与目标数据库连接， 那么就不能执行这条命令。所以要与EDS或者任何其它数据库连接，再发出这条命令。

DROP DATABASE不能撤销，小心使用！

<br/>**参数**

IF EXISTS：如果指定的数据库不存在，那么发出一个 notice 而不是抛出一个错误。

*name*：要被删除的现有数据库名。

<br/>**注意**

DROP DATABASE不能在事务块中执行。

这条命令在和目标数据库连接时不能执行。通常更好的做法是用dropdb 程序代替，该程序是此命令的一个封装。

<br/>**兼容性**

SQL 标准里没有DROP DATABASE语句。

<br/>**参见**

CREATE DATABASE