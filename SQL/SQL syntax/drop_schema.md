# DROP SCHEMA
<br/>**命令**
```SQL
DROP SCHEMA [ IF EXISTS ] name [, ...] [ CASCADE | RESTRICT ]
```
<br/>**描述**

DROP SCHEMA从数据库中删除模式。

模式只能被它的所有者或者超级用户删除。请注意， 所有者即使没有拥有模式中任何对象也可以删除模式(以及模式中的所有对象)。

<br/>**参数**

IF EXISTS：如果指定的模式不存在，那么发出一个 notice 而不是抛出一个错误。

*name*：模式的名字。

CASCADE：自动删除包含在模式中的对象(表、函数等等)。

RESTRICT：如果模式包含任何对象，则拒绝删除它。这个是缺省。

<br/>**示例**

从数据库中删除模式mystuff以及它包含的所有东西：
```SQL
DROP SCHEMA mystuff CASCADE;
```
<br/>**兼容性**

DROP SCHEMA和 SQL 标准完全兼容，只不过标准只允许每条命令删除一个模式。 另外，IF EXISTS选项是EdsSQL扩展。

<br/>**参见**

<!--ALTER SCHEMA, -->CREATE SCHEMA