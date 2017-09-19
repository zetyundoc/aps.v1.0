# DROP TABLE（企业版支持）
<br/>**命令**
```SQL
DROP TABLE [ IF EXISTS ] name [, ...] [ CASCADE | RESTRICT ]
```
<br/>**描述**

DROP TABLE从数据库中删除表或视图。只有表的者、 模式所有者和超级用户才能删除一个表。要清空而不是删除表，请使用<!-- DELETE或-->TRUNCATE。

DROP TABLE总是删除目标表上现有的任何索引、规则、触发器、约束。 但是，要删除一个有视图或者其它表用外键约束引用的表，必须声明CASCADE。 CASCADE将删除引用的视图，但是如果是外键约束，那么就只删除外键约束， 而不是另外一个表。

<br/>**参数**

IF EXISTS：如果指定的表不存在，那么发出一个 notice 而不是抛出一个错误。

*name*：要删除的现存表的名字(可以有模式修饰)。

CASCADE：级联删除依赖于表的对象(比如视图)。

RESTRICT：如果存在依赖对象，则拒绝删除该表。这个是缺省。

<br/>**示例**

删除films和distributors表：
```SQL
DROP TABLE films, distributors;
```
<br/>**兼容性**

此命令兼容 SQL 标准。只不过标准只允许一条命令删除一个表。此外， IF EXISTS选项是EdsSQL的扩展。

<br/>**参见**

ALTER TABLE, CREATE TABLE