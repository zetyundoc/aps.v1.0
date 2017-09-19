# FROM

FROM子句为SELECT声明一个或者多个源表。如果声明了多个源表， 那么结果就是所有源表的笛卡儿积(交叉连接)。但是通常会添加一些条件（通过WHERE）， 把返回行限制成笛卡儿积的一个小的子集。

FROM子句可以包括下列元素：

*table_name*：一个现存的表或视图的名字(可以有模式修饰)。如果声明了ONLY，则只扫描该表； 否则，该表和所有其派生表(如果有的话)都被扫描。可以在表名后面跟一个* 表示扫描所有其后代表。

*alias*：为那些包含别名的FROM项目取的别名。别名用于缩写或者在自连接中消除歧义 (自连接中同一个表将扫描多次)。如果提供了别名，那么它就会完全隐藏表或者函数的实际名字； 比如，如果给出FROM foo AS f，那么SELECT 剩下的东西必须把这个FROM项按照f而不是foo引用。 如果写了别名，也可以提供一个字段别名列表，这样可以替换表中一个或者多个字段的名字。

*select*：可以在FROM子句里出现一个子SELECT。 它的输出作用好像是为这条SELECT命令在其生存期里创建一个临时表。 请注意这个子SELECT必须用园括弧包围。并且必须给它一个别名。 当然，VALUES同样也可以在这里使用。

*with_query_name*：WITH查询通过写它自己的名字来引用，就像查询的名字就是一个表的名字。 （实际上，WITH查询为主查询隐藏了相同名字的任意实际表。 如果需要，可以通过模式限定表的名字来引用相同名字的实际表。）别名可以用相同的方式提供给表。

*function_name*：函数(特别是那些返回结果集的函数)调用可以出现在FROM子句里。 这么做就好像在这个SELECT命令的生命期中，把函数的输出创建为一个临时表一样。 当然也可以使用别名。如果写了别名，还可以写一个字段别名列表， 为函数返回的复合类型的一个或多个属性提供名字替换。如果函数定义为返回record类型， 那么必须出现一个AS关键字或者别名，后面跟着一个形如 ( column_name data_type [, ... ] )的字段定义列表。这个字段定义列表必须匹配函数返回的字段的实际数目和类型。

*join_type*

下列之一：

<ul>
<li>[ INNER ] JOIN

<li>LEFT [ OUTER ] JOIN

<li>RIGHT [ OUTER ] JOIN

<li>FULL [ OUTER ] JOIN

<li>CROSS JOIN

必须为INNER和OUTER连接类型声明一个连接条件， 也就是NATURAL, ON join_condition, or USING (join_column [, ...]) 之一。 它们的含义见下文，对于CROSS JOIN而言，这些子句都不能出现。

一个JOIN子句组合两个FROM项，为了方便我们将其作为"tables" 引用，尽管实际上他们可以是任意FROM条目的类型。必要时使用圆括弧以决定嵌套的顺序。 如果没有圆括弧，JOIN从左向右嵌套。在任何情况下，JOIN 都比逗号分隔的FROM列表绑定得更紧。

CROSS JOIN和INNER JOIN生成一个简单的笛卡儿积， 和你在FROM的顶层列出两个表的结果相同，但是受到连接条件（如果有）的限制。 CROSS JOIN等效于INNER JOIN ON (TRUE)，也就是说， 没有被条件删除的行。这种连接类型只是符号上的方便，因为它们和你用简单的 FROM和WHERE的效果一样。

LEFT OUTER JOIN返回笛卡儿积中所有符合连接条件的行， 再加上左表中通过连接条件没有匹配右表行的那些行。这样，左边的行将扩展成生成表的全长， 方法是在那些右表对应的字段位置填上 NULL 。请注意，只在计算匹配的时候， 才使用JOIN子句的条件，外层的条件是在计算完毕之后施加的。

相应的，RIGHT OUTER JOIN返回所有内连接的结果行， 加上每个不匹配的右边行(左边用 NULL 扩展)。这只是一个符号上的便利， 因为总是可以把它转换成一个LEFT OUTER JOIN，只要把左边和右边的输入对掉一下即可。

FULL OUTER JOIN返回所有内连接的结果行，加上每个不匹配的左边行(右边用 NULL 扩展)， 再加上每个不匹配的右边行(左边用 NULL 扩展)。

*ON join_condition*：一个生成boolean类型结果的表达式(类似WHERE子句)， 限定连接中那些行是匹配的。

USING ( *join_column* [, ...] )：一个形如USING ( a, b, ... ) 的子句，是ON left_table.a = right_table.a AND left_table.b = right_table.b ...的缩写。 同样，USING蕴涵着每对等效字段中只有一个包含在连接输出中，而不是两个都输出的意思。

NATURAL：是一个USING列表的缩写，这个列表说的是两个表中同名的字段。

LATERAL

LATERAL关键字可以放在一个子SELECT FROM项目的前面。 这允许子SELECT引用出现在FROM列表之前的FROM条目中的字段。 （没有LATERAL，每个子SELECT独立评估并且以此不能交叉引用任何其他 FROM条目。）

LATERAL也可以放在一个函数调用FROM条目的前面， 但是这种情况下它是一个噪声字，因为函数表达式可以在任何情况下引用前面的FROM条目。

LATERAL可以出现在FROM列表的顶层，或出现在JOIN树中。 在后面当前情况下，它也可以引用JOIN左侧和右侧的任意条目。

当FROM条目包含LATERAL交叉引用时，评估收益如下： 对于提供交叉引用字段的FROM条目的每一行，或提供字段的多个FROM 条目的行集，使用该字段的行或行集值评估LATERAL条目。 结果行像往常一样和计算他们的行连接。这是从字段源表重复每行或行集。

字段源表必须INNER或LEFT连接LATERAL条目， 其他为LATERAL条目计算每个行集的行集将不会是明确定义的行集。 因此，尽管一个构造（如X RIGHT JOIN LATERAL Y） 在语法结构上合法，并不实际上允许Y引用X。

<br/>**示例**
```SQL
select * from t;

SELECT column_name(s)
FROM table1
INNER JOIN table2
ON table1.column_name=table2.column_name;

SELECT column_name(s)
FROM table1
LEFT JOIN table2
ON table1.column_name=table2.column_name;

SELECT column_name(s)
FROM table1
RIGHT JOIN table2
ON table1.column_name=table2.column_name;

SELECT *
FROM table1,table2
WHERE table1.column_name=table2.column_name;
```

<br/>**兼容性**


**FROM中的函数调用**

EdsSQL允许把函数调用直接写作FROM列表的一个成员。 在SQL标准中应该有必要在子SELECT中包含这样一个函数；也就是， 该语法FROM func(...) alias等价于 FROM LATERAL (SELECT func(...)) alias。 请注意LATERAL被认为是隐含的；这是因为标准要求LATERAL语义为 一个在FROM中的UNNEST()条目。EdsSQL 对待UNNEST()和其他设置返回函数相同。

<br/>**参见**

SELECT