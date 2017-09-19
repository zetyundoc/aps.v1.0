# CREATE SCHEMA
<br/>**命令**
```SQL
CREATE SCHEMA schema_name [ AUTHORIZATION user_name ] [ schema_element [ ... ] ]
CREATE SCHEMA AUTHORIZATION user_name [ schema_element [ ... ] ]
CREATE SCHEMA IF NOT EXISTS schema_name [ AUTHORIZATION user_name ]
CREATE SCHEMA IF NOT EXISTS AUTHORIZATION user_name
```
<br/>**描述**

CREATE SCHEMA在当前数据库里输入一个新模式。 该模式名将在当前数据库里现存的所有模式名中唯一。

模式实际上是一个名字空间：它包含命名对象(表、数据类型、函数、操作符) 这些名字可以和其它模式里存在的其它对象重名。命名对象要么是通过用模式名作为前缀 "修饰"进行访问，要么是通过设置一个搜索路径包含所需要的模式。 一条带着无修饰对象名的CREATE命令都是在当前模式中创建创建对象的 (在搜索路径最前面的模式；可以用current_schema函数来判断)。

另外，CREATE SCHEMA可以包括在新模式中创建对象的子命令。 这些子命令和那些在创建完模式后发出的命令没有任何区别， 只不过是如果使用了AUTHORIZATION子句，那么所有创建的对象都将被该用户拥有。

<br/>**参数**

*schema_name*：要创建的模式名字。如果省略，则使用 *user_name*作为模式名。 这个名字不能以pg_开头，因为这样的名字保留给系统模式使用。

*user_name*：将拥有该模式的用户的角色名。如果省略，缺省为执行该命令的用户名。 要想创建一个属于其他角色的模式，你必须是那个角色的直接或非直接成员，或是超级用户。

*schema_element*：一个 SQL 语句，定义一个要在模式里创建的对象。目前，只有CREATE TABLE、 CREATE VIEW<!--、CREATE INDEX、 CREATE TRIGGER-->、CREATE SEQUENCE和GRANT是可以接受的子句。 其它类型的对象可以在创建完模式之后的独立命令里创建。

IF NOT EXISTS：如果相同名字的模式已经存在，那么什么也不要做（除了发出一个通知）。 当使用此选项时，不能包括*schema_element*子命令。

<br/>**注意**

要创建模式，调用该命令的用户必需在当前数据库上有CREATE权限。 （超级用户可以绕开这个检查。）

<br/>**示例**

创建一个模式：
```SQL
CREATE SCHEMA myschema;
```
为用户joe创建模式，模式名也叫joe：
```SQL
CREATE SCHEMA AUTHORIZATION joe;
```
创建一个名为test的模式，该模式被用户joe所拥有， 除非已经有一个名为test的模式。 （这与joe是否拥有早已存在的模式无关。）
```SQL
CREATE SCHEMA IF NOT EXISTS test AUTHORIZATION joe;
```
创建一个模式并且在里面创建一个表：
```SQL
CREATE SCHEMA hollywood
    CREATE TABLE films (title text, release date, awards text[])
    CREATE VIEW winners AS
        SELECT title, release FROM films WHERE awards IS NOT NULL;
```
请注意上面独立的子命令不是由分号结尾的

下面的命令是实现同样结果的等效语句：
```SQL
CREATE SCHEMA hollywood;
CREATE TABLE hollywood.films (title text, release date, awards text[]);
CREATE VIEW hollywood.winners AS
    SELECT title, release FROM hollywood.films WHERE awards IS NOT NULL;
```
<br/>**兼容性**

SQL 标准允许在CREATE SCHEMA里面有一个 DEFAULT CHARACTER SET子句以及比目前EdsSQL 可以接受的更多的子命令。

SQL 标准声明在CREATE SCHEMA里的子命令可以以任意顺序出现。 目前EdsSQL里的实现还不能处理所有子命令里前向引用的情况； 有时候可能需要重排一下子命令的顺序以避免前向引用。

在 SQL 标准里，模式的所有者总是拥有其中的所有对象。 EdsSQL允许模式包含非模式所有者拥有的对象。 只有在模式所有者将自己模式的CREATE权限给了其他人， 或者超级用户选择在该模式中创建对象时，才可能出现。

IF NOT EXISTS选项是一个EdsSQL扩展。

<br/>**参见**

<!--ALTER SCHEMA, -->DROP SCHEMA