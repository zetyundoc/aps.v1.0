# SET
<br/>**命令**
```SQL
SET [ SESSION | LOCAL ] configuration_parameter { TO | = } { value | 'value' | DEFAULT }
SET [ SESSION | LOCAL ] TIME ZONE { timezone | LOCAL | DEFAULT }
```
<br/>**描述**

SET命令修改运行时配置参数。许多参数都可以用SET在运行时设置。但是有些要求使用超级用户权限来修改， 而其它有些则在服务器或者会话开始之后不能修改。请注意SET只影响当前会话使用的数值。

如果SET（或相等的SET SESSION）是在一个稍后退出的事务里发出的， 那么SET命令的效果将在事务回滚之后消失。一旦包围它的事务提交， 那么其效果将持续到会话结束，除非被另外一个SET覆盖。

不管是否提交，SET LOCAL的效果只持续到当前事务结束。 一个特例是在一个事务里面的SET后面跟着一个SET LOCAL： 在事务结束之前只能看到SET LOCAL的数值，但是之后(如果事务提交)， 则是SET的值生效。

SET或SET LOCAL的影响也可以通过回滚到一个命令之前的保存点取消。

如果SET LOCAL在一个对相同变量有SET选项的函数内使用 (请参阅CREATE FUNCTION)，SET LOCAL 命令的影响在函数退出时消失；也就是说，函数调用时起作用的值不论如何都会还原。 这允许SET LOCAL用来对函数内的一个参数进行动态和反复修改， 尽管仍然方便使用SET选项来保存和存储调用程序的值。然而， 规则的SET命令重写任何函数内的SET选项；其影响将会持续，除非回滚。

<!--<p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >注意：在PostgreSQL 8.0到8.2版本中，一个SET LOCAL 的影响将通过释放一个较早的保存点被取消，或者通过从一个PL/pgSQL 异常块成功退出。该选项已被更改，因为它被认为是非直观的。</p>-->

<br/>**参数**

SESSION：声明这个命令只对当前会话起作用。如果SESSION或LOCAL都没出现， 那么这个是缺省。

LOCAL：声明该命令只在当前事务中有效。在COMMIT或者ROLLBACK之后， 会话级别的设置将再次生效。请注意如果在BEGIN块之外运行， 那么SET LOCAL将表现出没有作用， 因为事务将立即结束。

*configuration_parameter*：可设置的运行时参数的名字。

*value*：参数的新值。值可以声明为字符串常量、标识符、数字，或者逗号分隔的上面这些东西的列表， 视特定变量而定。 对于特定参数是恰当的。可以写出DEFAULT 把这些参数设置为它们的缺省值。（也就是说，如果在当前会话中没有SET执行， 已有的任何数值都会设置。）

SCHEMA：SET SCHEMA 'value'是SET search_path TO value的别名。 使用该语法只可指定一个模式。

NAMES：SET NAMES value是SET client_encoding TO value的别名。

SEED：

为随机数生成器(函数random)设置内部的种子。 允许的值是介于 -1 和 1 之间的浮点数，然后它会被乘以 2<sup>31-1</sup>。

也可以通过调用setseed函数来设置种子：
```SQL
SELECT setseed(value);
```
TIME ZONE：

SET TIME ZONE value是SET timezone TO value 的一个别名。语法SET TIME ZONE允许为时区设置特殊的语法。下面是有效值的例子：

'PST8PDT' —— 加州伯克利的时区。

'Europe/Rome' —— 意大利时区。

-7 —— UTC 以西 7 小时的时区(等效于 PDT)。正值为UTC以东。

INTERVAL '-08:00' HOUR TO MINUTE —— UTC 以西 8 小时的时区(等效于 PST)。

LOCAL<br/>
DEFAULT：

将时区设置为你的本地时区(也就是，服务器的timezone缺省值)。


<!--<br/>**注意**

函数set_config提供了等效的功能。 同时，可以UPDATE这个pg_settings 系统视图来执行SET的等价操作。-->

<br/>**示例**

设置模式搜索路径：
```SQL
SET search_path TO my_schema, public;
```
<!--把日期时间风格设置为传统的POSTGRES风格(日在月前)：
```SQL
SET datestyle TO postgres, dmy;
```-->
把时区设置为加州伯克利：
```SQL
SET TIME ZONE 'PST8PDT';
```
为意大利设置时区：
```SQL
SET TIME ZONE 'Europe/Rome';
```
<br/>**兼容性**

SET TIME ZONE扩展了在 SQL 标准里定义的语法。标准只允许有一个数字时区偏移， 而EdsSQL还允许完整更灵活的时区声明。 所有其它的SET特性都是EdsSQL扩展。

<br/>**参见**

<!--RESET, -->SHOW