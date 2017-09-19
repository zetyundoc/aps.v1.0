# LIMIT

LIMIT子句由两个独立的子句组成：
```SQL
LIMIT { count | ALL }
OFFSET start
```
*count*声明返回的最大行数， 而start声明开始返回行之前忽略的行数。 如果两个都指定了，那么在开始计算*count* 个返回行之前将先跳过*start*行。

如果*count*表达式评估为NULL， 它被当做LIMIT ALL，也就是，没有限制。如果 *start*评估为NULL，他与 OFFSET 0相同对待。

SQL:2008引入了一个不同的语法来达到相同的效果，这也是EdsSQL支持的。这是：
```SQL
OFFSET start { ROW | ROWS }
FETCH { FIRST | NEXT } [ count ] { ROW | ROWS } ONLY
```
在该语法中，为start或count 提供除简单整型常量之外的东西时，你必须写圆括号。如果count 在FETCH子句中省略了，它默认为1。ROW和ROWS 以及FIRST和NEXT是不影响这些子句的效果的干扰词。 根据该标准，若两个都存在则OFFSET子句必须在FETCH子句之前出现； 但是EdsSQL的要求更为宽松并且允许任意一种顺序。

使用LIMIT的一个好习惯是使用一个ORDER BY子句把结果行限制成一个唯一的顺序。 否则你会得到无法预料的结果子集，你可能想要第十行到第二十行，但是是以什么顺序的第十行到第二十行？ 除非你声明ORDER BY ，否则你不知道什么顺序。

查询优化器在生成查询规划时会把LIMIT考虑进去，所以你很有可能因给出的 LIMIT和OFFSET值不同而得到不同的规划(生成不同的行序)。 因此用不同的LIMIT/OFFSET值选择不同的查询结果的子集 将不会产生一致的结果，除非你用ORDER BY 强制生成一个可预计的结果顺序。这可不是 bug ；这是 SQL 生来的特点， 因为除非用了ORDER BY约束顺序，SQL 不保证查询生成的结果有任何特定的顺序。

如果没有一个ORDER BY来强制选择一个确定性子集，那么重复执行相同的 LIMIT查询返回不同的表行的子集甚至都是可能的。同样， 这不是一个漏洞；结果的确定在这种情况下没法保证。

<br/>**兼容性**

**LIMIT 和 OFFSET**

子句LIMIT和OFFSET是EdsSQL 特定的语法，也是MySQL使用的。SQL:2008标准引入了 OFFSET ... FETCH {FIRST|NEXT} ...获取相同的功能， 如上面的LIMIT 子句所示。 该语法也被IBM DB2使用。（为Oracle 所写的应用程序通常使用一个涉及自动生成的rownum列的工作区， 来实现这些子句的效果，这在EdsSQL中是不可用的。）

<br/>**参见**

SELECT