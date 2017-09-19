# SELECT
<br/>**命令**
```SQL
[ WITH [ RECURSIVE ] with_query [, ...] ]
SELECT [ ALL | DISTINCT [ ON ( expression [, ...] ) ] ]
    * | expression [ [ AS ] output_name ] [, ...]
    [ FROM from_item [, ...] ]
    [ WHERE condition ]
    [ GROUP BY expression [, ...] ]
    [ HAVING condition [, ...] ]
    [ WINDOW window_name AS ( window_definition ) [, ...] ]
    [ { UNION | INTERSECT | EXCEPT } [ ALL | DISTINCT ] select ]
    [ ORDER BY expression [ ASC | DESC | USING operator ] [ NULLS { FIRST | LAST } ] [, ...] ]
    [ LIMIT { count | ALL } ]
    [ OFFSET start [ ROW | ROWS ] ]
    [ FETCH { FIRST | NEXT } [ count ] { ROW | ROWS } ONLY ]
    [ OF table_name [, ...] ] [ NOWAIT ] [...] ]

这里的from_item 可以是:

    [ ONLY ] table_name [ * ] [ [ AS ] alias [ ( column_alias [, ...] ) ] ]
    [ LATERAL ] ( select ) [ AS ] alias [ ( column_alias [, ...] ) ]
    with_query_name [ [ AS ] alias [ ( column_alias [, ...] ) ] ]
    [ LATERAL ] function_name ( [ argument [, ...] ] ) [ AS ] alias [ ( column_alias [, ...] | column_definition [, ...] ) ]
    [ LATERAL ] function_name ( [ argument [, ...] ] ) AS ( column_definition [, ...] )
    from_item [ NATURAL ] join_type from_item [ ON join_condition | USING ( join_column [, ...] ) ]

with_query是:

    with_query_name [ ( column_name [, ...] ) ] AS ( select | values | insert )

TABLE [ ONLY ] table_name [ * ]
```
<br/>**描述**

SELECT将从零个或更多表中返回记录行。SELECT通常的处理如下：
<ol>
<li>计算列出在WITH中的所有查询。这些有效的充当可以在 FROM列表中引用的临时表。一个在FROM 中多次引用的WITH查询只计算一次。（参阅下面的 WITH 子句。）</li>

<li>计算列出在FROM中的所有元素(FROM 列表中的每个元素都是一个实际的或虚拟的表)。如果在FROM 列表里声明了多个元素，那么他们就交叉连接在一起(参见下面的 FROM 子句)。

<li>如果声明了WHERE子句，那么在输出中消除所有不满足条件的行。 （参见下面的WHERE 子句。）

<li>如果声明了GROUP BY子句，输出就组合成匹配一个或多个数值的不同组里。 如果出现了HAVING子句，那么它消除那些不满足给出条件的组。 （参见下面的GROUP BY 子句 和 HAVING 子句。）

<li>实际输出行将使用SELECT输出表达式针对每一个选中的行或行组进行计算。 （参见下面的SELECT 列表。）

<li>SELECT DISTINCT从结果中消除重复的行。SELECT DISTINCT ON 消除匹配所有指定表达式的行。SELECT ALL（缺省）返回所有的行， 包括重复的行。（参阅下面的DISTINCT 子句。）

<li>使用UNION, INTERSECT, EXCEPT 可以把多个SELECT语句的输出合并成一个结果集。UNION 操作符返回两个结果集的并集。INTERSECT操作符返回两个结果集的交集。 EXCEPT操作符返回在第一个结果集对第二个结果集的差集。不管哪种情况， 重复的行都被删除，除非声明了ALL。噪声字DISTINCT 可以用来明确的声明消除重复的行。请注意，DISTINCT在这里是缺省的行为， 即使ALL是SELECT本身的缺省。 （参阅下面的UNION 子句、INTERSECT 子句、 EXCEPT 子句。）

<li>如果声明了ORDER BY子句，那么返回的行将按照指定的顺序排序。 如果没有给出ORDER BY，那么数据行是按照系统认为可以最快生成的顺序给出的。 （参阅下面的ORDER BY 子句。）

<li>如果给出了LIMIT(或 FETCH FIRST)或 OFFSET子句，那么SELECT语句只返回结果行的一个子集。 （参阅下面的LIMIT 子句。）</ol>

<!--<li>如果声明了FOR UPDATE, FOR NO KEY UPDATE,FOR SHARE 或FOR KEY SHARE子句，那么SELECT语句对并发的更新锁住选定的行。 （参阅下面的锁定子句子句。）

你必须对每个在SELECT命令中使用的字段有SELECT权限。= 使用FOR NO KEY UPDATE, FOR UPDATE, FOR SHARE 或FOR KEY SHARE还要求UPDATE权限 （至少选择每个表的一列）。-->

<br/>**参数**

<a href="select_lists.md">**SELECT 列表**</a>

<!--SELECT列表(在SELECT和FROM关键字之间的部分) 声明组成SELECT语句的输出行的表达式。这些表达式可以 (并且通常也会)引用在FROM子句里面计算出来的字段。

就像在一个表中，一个SELECT的每个输出列都有一个名称。 在一个简单的SELECT中，该名称仅用于标记显示的列， 但当SELECT是一个较大查询的子查询时，名称被较大查询视为子查询产生的虚表的字段名。 为了指定用于输出列的名称，要在列表达式后写AS *output_name*。为了防止将来可能的关键字添加， 建议您要么写AS要么用双引号引起输出名称。）如果你不指定一个字段名， EdsSQL会自动选择一个名称。如果字段的表达式是一个简单的列引用， 那么选择的名称与字段名相同；在更复杂的情况下，可能会使用一个函数或类型名， 或系统会依赖于一个类似?column?的生成名。

一个输出列的名称可以用来参考ORDER BY和GROUP BY子句中的字段的值， 而不是在WHERE或者HAVING子句中的；反而您必须在那里写出表达式。

除了表达式，也可以在输出列表中使用表示所有字段。 还可以用*table_name*. 作为来自该表的所有字段的缩写。这些情况下用AS指定新名称是不可能的； 输出列的名称将会与表列的名称相同。-->

<a href="with.md">**WITH 子句**</a>

<!--WITH子句允许声明一个或多个可以在主查询中通过名字引用的子查询。 子查询在主查询期间有效的充当临时表或视图。每个子查询可以是SELECT, VALUES, INSERT, UPDATE 或 DELETE语句。当在WITH中写一个数据修改语句时 （INSERT--, UPDATE 或 DELETE）， 通常包含一个RETURNING子句。RETURNING的输出而不是 语句修改的底层表的输出形成被主查询读取的临时表。如果省略了RETURNING， 该语句仍然执行，但是不会产生输出，所以不能作为一个表被主查询引用。

必须为每个WITH查询声明一个名字（没有模式修饰）。 可选的，可以指定字段名的列表；如果省略了，那么字段名从子查询中推断出。

如果声明了RECURSIVE，那么允许SELECT子查询通过名字引用它自己。 比如一个有下面形式的子查询
```SQL
non_recursive_term UNION [ ALL | DISTINCT ] recursive_term
```
递归的自我引用必须在UNION的右边出现。每个查询只允许一个递归的自我引用。 不支持递归的数据修改语句，但是可以在数据修改语句中使用递归的SELECT 查询的结果。

RECURSIVE的另外一个作用是WITH查询不需要排序： 一个查询可以引用另外一个稍后出现在列表中的查询。（但是，循环引用或相互递归没有实现。） 没有RECURSIVE，WITH查询只能引用 更早出现在WITH列表中的同层级的WITH查询。

WITH查询的主要特性是他们只在主查询的每次执行中评估一次， 即使主查询引用了他们多次也是如此。特别的，保证数据修改语句只被执行一次， 不管主查询读取他们的所有或任意的输出。

主查询和WITH查询（理论上）同时执行。这意味着WITH 中的数据修改语句的影响不能从查询的其他部分看到，除非读取它的RETURNING 输出。如果两个这样的数据修改语句尝试修改相同的行，那么结果是未知的。-->


<a href="from.md">**FROM 子句**</a>

<!--FROM子句为SELECT声明一个或者多个源表。如果声明了多个源表， 那么结果就是所有源表的笛卡儿积(交叉连接)。但是通常会添加一些条件（通过WHERE）， 把返回行限制成笛卡儿积的一个小的子集。

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

字段源表必须INNER或LEFT连接LATERAL条目， 其他为LATERAL条目计算每个行集的行集将不会是明确定义的行集。 因此，尽管一个构造（如X RIGHT JOIN LATERAL Y） 在语法结构上合法，并不实际上允许Y引用X。-->

<a href="where.md">**WHERE 子句**</a>

<!--可选的WHERE条件有如下常见的形式：
```SQL
WHERE condition
```
这里condition可以是任意生成类型为 boolean的表达式。任何不满足这个条件的行都会从输出中删除。 如果一个行的数值代入到条件中计算出来的结果为真，那么该行就算满足条件。-->

<a href="group_by.md">**GROUP BY 子句**</a>

<!--可选的GROUP BY子句的一般形式
```SQL
GROUP BY expression [, ...]
```
它将把所有在组合表达式上拥有相同值的行压缩成一行。 *expression*可以是一个输入字段名字， 或者是一个输出字段(SELECT列表项)的名字或序号， 或者也可以是任意输入字段组成的表达式。在有歧义的情况下， 一个GROUP BY的名字将被解释成输入字段的名字，而不是输出字段的名字。

如果使用了聚合函数，那么就会对每组中的所有行进行计算并生成一个单独的值 (而如果没有GROUP BY，那么聚合将对选出来的所有行计算出一个单独的值)。 如果出现了GROUP BY，那么SELECT 列表表达式中再引用那些没有分组的字段就是非法的，除非放在聚合函数里， 或未分组的字段函数上依赖于分组的字段，因为对于未分组的字段，可能会返回多个数值。 如果分组的字段（或它们的一个子集）是包含未分组字段的表的主键，那么存在一个函数依赖。-->

<a href="having.md">**HAVING 子句**</a>

<!--可选的HAVING子句有如下形式：
```SQL
HAVING condition
```
这里*condition*与为 WHERE子句里声明的相同。

HAVING去除了一些不满足条件的组行。它与WHERE 不同：WHERE在使用GROUP BY之前过滤出单独的行， 而HAVING过滤由GROUP BY创建的行。 在condition 里引用的每个字段都必须无歧义地引用一个分组的行，除非引用出现在一个聚合函数里。

HAVING的出现把查询变成一个分组的查询，即使没有GROUP BY 子句也这样。这一点和那些包含聚合函数但没有GROUP BY子句的查询里发生的事情是一样的。 所有选取的行都被认为会形成一个单一的组，而SELECT 列表和HAVING子句只能从聚合函数里面引用表的字段。 这样的查询在HAVING条件为真的时候将发出一个行，如果为非真，则返回零行。-->

<a href="window.md">**WINDOW 子句**</a>

<!--WINDOW子句的一般形式是
```SQL
WINDOW window_name AS ( window_definition ) [, ...]
```
这里的window_name是可以在OVER 子句中引用的名字或随后的窗口定义，这里的*window_definition*是
```SQL
[ existing_window_name ]
[ PARTITION BY expression [, ...] ]
[ ORDER BY expression [ ASC | DESC | USING operator ] [ NULLS { FIRST | LAST } ] [, ...] ]
[ frame_clause ]
```
如果声明了*existing_window_name*， 那么必须引用一个在WINDOW列表中出现的更早的项；新的窗口从这个项中拷贝分区子句， 和排序子句（如果有）。在这种情况下，这个新的窗口不能声明他自己的PARTITION BY子句， 但是如果拷贝的窗口没有排序子句的话他可以声明ORDER BY子句。 新窗口总是使用他自己的框架子句；拷贝的窗口必须不能声明框架子句。

PARTITION BY列表的元素的以与GROUP BY 子句 的元素相同的方式来解释，除了他们总是简单的表达式并且从不是一个输出列的名称或者编号。 另外一个差异是这些表达式可以包含聚合函数调用，而在常规的GROUP BY子句中这是不允许的。 在这里允许是因为开窗在分组和聚合之后发生。

同样的，ORDER BY列表的元素以与ORDER BY 子句 的元素相同的方式来解释，除了这个表达式总是作为简单的表达式并且从不是一个输出列的名称或者编号。

可选的*frame_clause*为依赖于框架的窗口函数（不是所有）定义 窗口框架。窗口框架是查询中的每行（称为当前行）的一组相关行。 *frame_clause*可以是下列之一
```SQL
[ RANGE | ROWS ] frame_start
[ RANGE | ROWS ] BETWEEN frame_start AND frame_end
```
这里的frame_start和frame_end可以是下列之一
```
UNBOUNDED PRECEDING
value PRECEDING
CURRENT ROW
value FOLLOWING
UNBOUNDED FOLLOWING
```
如果省略了*frame_end*，那么它的缺省是CURRENT ROW。 限制是*frame_start*不能是UNBOUNDED FOLLOWING， *frame_end*不能是UNBOUNDED PRECEDING， *frame_end*选项在上面的列表中不能比*frame_start* 选项出现的早，例如RANGE BETWEEN CURRENT ROW AND *value* PRECEDING是不允许的。

缺省框架选项是RANGE UNBOUNDED PRECEDING，这与RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW相同；它将框架设置为分区中的所有行，在ORDER BY 序列中是从当前行的最后一个元素开始（这意味着若无ORDER BY则是所有行）。 通常，UNBOUNDED PRECEDING表示框架从分区的第一行开始，类似的 UNBOUNDED FOLLOWING表示框架以分区的最后一行结束（不管是RANGE 还是ROWS模式）。在ROWS模式，CURRENT ROW 意味着框架以当前行开始或结束；但是在RANGE模式，意味着框架以当前行在 ORDER BY序列中的第一个或最后一个元素开始或结束。*value* PRECEDING 和*value* FOLLOWING子句目前只允许在ROWS模式。 他们表明框架以当前行之前或者之后许多行开始或者结束。*value* 必须是一个不包含任何变量、聚合函数或者窗口函数的整型表达式。该值不能为空或者负值； 但可以为0，并且这时选择当前行本身。

注意如果ORDER BY排序不能唯一地排列行，那么ROWS 选项可能产生不可预测的结果。RANGE选项是为了确保ORDER BY 序列中的对等的行能得到同等对待；任何两个对等行将会都在或者都不在框架中。

一个WINDOW语句的目的是指定出现在查询的SELECT 列表 或者ORDER BY 子句中的window 函数的行为。 这些函数可以在其OVER子句中通过名称引用WINDOW子句条目。 一个WINDOW子句条目不需要在任何地方都引用；若它不在查询中使用，它将被忽略。 可以使用窗口程序而根本不需要任何WINDOW子句， 因为一个窗口函数调用可以直接在其OVER子句中指定其窗口定义。然而， WINDOW子句会在多个窗口函数需要相同窗口定义时保存输入。-->


<a href="distinct.md">**DISTINCT 子句**</a>

<!--如果声明了SELECT DISTINCT，那么就从结果集中删除所有重复的行 (每个有重复的组都保留一行)。SELECT ALL声明相反的作用：所有行都被保留(这是缺省)。

SELECT DISTINCT ON ( *expression* [, ...] ) 只保留那些在给出的表达式上运算出相同结果的行集合中的第一行。DISTINCT ON 表达式是使用与ORDER BY相同的规则进行解释的（见上文）。请注意， 除非使用了ORDER BY来保证需要的行首先出现，否则，"第一行" 是不可预测的。比如：
```SQL
SELECT DISTINCT ON (location) location, time, report
    FROM weather_reports
    ORDER BY location, time DESC;
```
为每个地点检索最近的天气报告。但是如果没有使用ORDER BY 来强制对每个地点的时间值进行降序排序，那么就会得到每个地点的不知道什么时候的报告。

DISTINCT ON表达式必须匹配最左边的ORDER BY表达式。 ORDER BY子句将通常包含额外的表达式来判断每个DISTINCT ON 组里面需要的行的优先级。-->

<a href="union.md">**UNION 子句**</a>

<!--UNION子句的一般形式是：
```SQL
select_statement UNION [ ALL | DISTINCT ] select_statement
```
这里的*select_statement*是任意没有 ORDER BY, LIMIT, FOR NO KEY UPDATE, FOR UPDATE, FOR SHARE, 或 FOR KEY SHARE子句的SELECT语句。 如果用圆括弧包围，ORDER BY和LIMIT可以附着在子表达式里。 如果没有圆括弧，这些子句将交给UNION的结果使用，而不是给它们右边的输入表达式。

UNION操作符计算那些涉及到的所有SELECT 语句返回的行的结果联合。一个行如果至少在两个结果集中的一个里面出现， 那么它就会在这两个结果集的集合联合中。两个作为UNION 直接操作数的SELECT必须生成相同数目的字段， 并且对应的字段必须有兼容的数据类型。

缺省的UNION结果不包含任何重复的行，除非声明了ALL选项。 ALL制止了消除重复的动作。因此，UNION ALL通常比 UNION明显要快，可能的情况下尽量使用ALL。 DISTINCT可以明确的指定消除重复行的缺省行为。

同一个SELECT语句中的多个UNION操作符是从左向右计算的， 除非用圆括弧进行了标识。

目前，FOR NO KEY UPDATE, FOR UPDATE, FOR SHARE 和 FOR KEY SHARE不能在UNION的结果或输入中声明。-->

<a href="intersect.md">**INTERSECT 子句**</a>

<!--INTERSECT子句的一般形式是：
```SQL
select_statement INTERSECT [ ALL | DISTINCT ] select_statement
```
*select_statement*是任何不带 ORDER BY, LIMIT, FOR NO KEY UPDATE, FOR UPDATE-, FOR SHARE, 或 FOR KEY SHARE子句的SELECT语句。

INTERSECT计算涉及的SELECT语句返回的行集合的交集。 如果一个行在两个结果集中都出现，那么它就在两个结果集的交集中。

INTERSECT的结果不包含任何重复行，除非你声明了ALL选项。 用了ALL以后，一个在左边的表里有m个重复而在右边表里有 n个重复的行将在结果集中出现min(*m,n*)次。 DISTINCT可以明确的指定消除重复行的缺省行为。

除非用圆括号指明顺序，同一个SELECT语句中的多个 INTERSECT操作符是从左向右计算的。INTERSECT 比UNION绑定得更紧，也就是说A UNION B INTERSECT C 将理解成 A UNION (B INTERSECT C)。

目前，不能给INTERSECT的结果或者任何INTERSECT的输入声明 FOR NO KEY UPDATE, FOR UPDATE, FOR SHARE 和 FOR KEY SHARE。-->

<a href="except.md">**EXCEPT 子句**</a>

<!--EXCEPT子句有如下的通用形式：
```SQL
select_statement EXCEPT [ ALL | DISTINCT ] select_statement
```
select_statement是任何没有 ORDER BY, LIMIT, FOR NO KEY UPDATE, FOR UPDATE, FOR SHARE, 或 FOR KEY SHARE子句的SELECT表达式。

EXCEPT操作符计算存在于左边SELECT 语句的输出而不存在于右边SELECT语句输出的行。

EXCEPT的结果不包含任何重复的行，除非声明了ALL选项。 使用ALL时，一个在左边表中有m个重复而在右边表中有 n个重复的行将在结果中出现max(m-n,0)次。 DISTINCT可以明确的指定消除重复行的缺省行为。

除非用圆括弧指明顺序，否则同一个SELECT语句中的多个 EXCEPT操作符是从左向右计算的。EXCEPT 和UNION的绑定级别相同。

目前，不能给EXCEPT的结果或者任何EXCEPT的输入声明 FOR NO KEY UPDATE, FOR UPDATE, FOR SHARE 和 FOR KEY SHARE子句。-->

<a href="order_by.md">**ORDER BY 子句**</a>

<!--可选的ORDER BY子句有下面的一般形式：
```SQL
ORDER BY expression [ ASC | DESC | USING operator ] [ NULLS { FIRST | LAST } ] [, ...]
```
ORDER BY子句导致结果行根据指定的表达式进行排序。 如果根据最左边的表达式，两行的结果相同，那么就根据下一个表达式进行比较， 依此类推。如果对于所有声明的表达式他们都相同，那么按随机顺序返回。

每个expression可以是一个输出字段 (SELECT列表项)的名字或者序号，或者也可以是用输入字段的数值组成的任意表达式。

序数指的是输出字段按顺序(从左到右)的位置。这个特性可以对没有唯一名称的字段进行排序。 这不是必须的，因为总是可以通过AS子句给一个要输出的字段赋予一个名称。

在ORDER BY里还可以使用任意表达式，包括那些没有出现在 SELECT输出列表里面的字段。因此下面的语句现在是合法的：
```SQL
SELECT name FROM distributors ORDER BY code;
```
这个特性的一个局限就是应用于UNION,INTERSECT,EXCEPT 子句的结果的ORDER BY子句只能在一个输出字段名或者数字上声明， 而不能在一个表达式上声明。

如果一个ORDER BY表达式是一个简单名称，同时匹配结果字段和输入字段， ORDER BY将把它解释成结果字段名称。这和GROUP BY 在同样情况下做的选择正相反。这样的不一致是用来和 SQL 标准兼容的。

可以给ORDER BY子句里每个字段加一个可选的ASC(升序，缺省) 或DESC(降序)关键字。还可以在USING 子句里声明一个排序操作符来实现排序。排序操作符必须小于或大于某些B-tree操作符族的成员。 ASC等效于使用USING <而DESC等效于使用USING >。 但是一个用户定义类型的创建者可以明确定义缺省的排序顺序，并且可以使用其他名称的操作符。

如果指定NULLS LAST，空值会在所有非空值之后排序；如果指定 NULLS FIRST，空值会在所有非空值之前排序。如果两者均未指定， 当指定ASC或缺省时，默认反应时是NULLS LAST， 并且当指定DESC时，默认反应时是NULLS FIRST（因此， 默认地认为空是大于非空的）。当指定USING时， 默认空排序依赖于操作符是小于还是大于操作符。

请注意排序选项仅适用于他们遵循的表达式；例如ORDER BY x, y DESC 不意味着与ORDER BY x DESC, y DESC相同。

字符类型的数据是按照应用于被排序的字段的排序规则排序的。可以在需要时通过在 *expression*中包含COLLATE 子句覆盖，例如ORDER BY mycolumn COLLATE "en_US"。-->

<a href="limit.md">**LIMIT 子句**</a>

<!--LIMIT子句由两个独立的子句组成：
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

如果没有一个ORDER BY来强制选择一个确定性子集，那么重复执行相同的 LIMIT查询返回不同的表行的子集甚至都是可能的。同样， 这不是一个漏洞；结果的确定在这种情况下没法保证。-->

<!--**锁定子句**

FOR UPDATE, FOR NO KEY UPDATE, FOR SHARE 和 FOR KEY SHARE 是 锁定子句;他们影响 SELECT如何从表中锁定行作为获得的行。

锁定子句的一般形式：
```SQL
FOR lock_strength [ OF table_name [, ...] ] [ NOWAIT ]
```
这里的lock_strength可以是下列之一：

```SQL
UPDATE
NO KEY UPDATE
SHARE
KEY SHARE
```
FOR UPDATE令那些被SELECT检索出来的行被锁住， 就像要更新一样。这样就避免它们在当前事务结束前被其它事务修改或者删除；也就是说， 其它企图UPDATE, DELETE, SELECT FOR UPDATE, SELECT FOR NO KEY UPDATE, SELECT FOR SHARE 或 SELECT FOR KEY SHARE这些行的事务将被阻塞，直到当前事务结束。 FOR UPDATE锁模式也可以通过在一个行上DELETE或在特定的字段上修改值的 UPDATE获得。目前，为UPDATE情况考虑的字段设置是那些有唯一索引并且可以用于外键的 （所以不考虑局部索引和表达式索引），但是这个可能会在将来改变。同样， 如果一个来自其它事务的UPDATE, DELETE, SELECT FOR UPDATE已经锁住了某个或某些选定的行，SELECT FOR UPDATE 将等到那些事务结束，并且将随后锁住并返回更新的行(或者不返回行，如果行已经被删除)。 但是，在REPEATABLE READ或SERIALIZABLE事务内部， 如果在事务开始时要被锁定的行已经改变了，那么将抛出一个错误。

FOR NO KEY UPDATE的行为类似，只是获得的锁比较弱：这个锁将不锁定 尝试在相同的行上获得锁的SELECT FOR KEY SHARE命令。 这个锁模式也可以通过任何不争取FOR UPDATE锁的UPDATE获得。

FOR SHARE的行为类似，只是它在每个检索出来的行上要求一个共享锁， 而不是一个排它锁。一个共享锁阻塞其它事务在这些行上执行UPDATE, DELETE, SELECT FOR UPDATE 或 SELECT FOR NO KEY UPDATE，却不阻止他们执行SELECT FOR SHARE 或 SELECT FOR KEY SHARE。

FOR KEY SHARE的行为类似于FOR SHARE，只是锁比较弱： 阻塞SELECT FOR UPDATE但不阻塞SELECT FOR NO KEY UPDATE。 一个共享键块阻塞其他事务执行DELETE或任意改变键值的UPDATE， 但是不阻塞其他UPDATE，也不阻止SELECT FOR NO KEY UPDATE, SELECT FOR SHARE, 或 SELECT FOR KEY SHARE。

为了避免操作等待其它事务提交，使用NOWAIT选项。如果被选择的行不能立即被锁住， 那么语句将会立即报告一个错误，而不是等待。请注意，NOWAIT只适用于行级别的锁， 要求的表级锁ROW SHARE仍然以通常的方法进行。 如果需要申请表级别的锁同时又不等待，那么你可以使用LOCK的 NOWAIT选项。

如果在锁定子句中明确指定了表名字，那么将只有这些指定的表被锁定，其他在 SELECT中使用的表将不会被锁定。 一个其后不带表列表的锁定子句将锁定该声明中所有使用的表。 如果锁定子句应用于一个视图或者子查询，它同样将锁定所有该视图或子查询中使用到的表。 但是这些子句不适用于被主查询引用的WITH查询。 如果你想要行锁定发生在WITH查询中，那么在WITH查询中指定锁定子句。

多个锁定子句可以用于为不同的表指定不同的锁定模式。如果一个表出同时出现 (或隐含同时出现)在多个锁定子句中，那么将看做只被最强的那个声明了处理。 类似的，如果影响一个表的任意子句中出现了NOWAIT， 那么该表将按照NOWAIT处理。

锁定子句不能在那些无法使用独立的表行清晰标识返回行的环境里使用；比如，它不能和聚合一起使用。

当锁定子句出现在SELECT查询的顶层时，锁定的行恰好是被查询返回的行； 在连接查询的情况下，锁定的行是那些有助于返回连接的行。另外， 满足查询条件的行作为查询快照将被锁定，尽管如果在快照后他们被更新不再满足查询条件 就不再被返回。如果使用了LIMIT，那么一旦返回足够的行满足限制锁定将停止 （但是请注意，通过OFFSET跳过的行将被锁定）。相似的，如果锁定子句用于游标查询， 只有实际抓取到的行或通过游标跳过的行将被锁定。

当锁定子句出现在子SELECT中时，锁定的行是那些通过子查询返回到外查询的行。 这些包含的行可能比单独检查子查询时给出的行更少，因为外查询的条件可能会用来优化子查询的执行。 例如：
```SQL
SELECT * FROM (SELECT * FROM mytable FOR UPDATE) ss WHERE col1 = 5;
```
将只锁定col1 = 5的行，即使那个条件不是子查询中的原文。

以前的版本未能保持锁，通过一个稍后的保存点来改善。例如，这段代码：
```SQL
BEGIN;
SELECT * FROM mytable WHERE key = 1 FOR UPDATE;
SAVEPOINT s;
UPDATE mytable SET ... WHERE key = 1;
ROLLBACK TO s;
```
FOR UPDATE锁将会在ROLLBACK TO之后无法保持。这在版本9.3中已经修复了。



<p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >注意：一个SELECT命令运行在READ COMMITTED事务隔离级别和使用 ORDER BY和一个锁定子句返回顺序混乱的行是可能的。这是因为ORDER BY 先生效。命令排序结果，但是可能会在其中一行或多行上获取锁的时候被阻塞。一旦SELECT 的阻塞被解除后，某些顺序字段值可能被修改，导致这些行混乱了（尽管他们还是原先字段值的顺序）。 可以在需要的时候通过在子查询中放置FOR UPDATE/SHARE子句来避开，例如<br/><br/>

SELECT * FROM (SELECT * FROM mytable FOR UPDATE) ss ORDER BY column1;<br/><br/>

请注意这将导致锁定mytable的所有行，而顶层的FOR UPDATE 将会实际上仅锁住返回行。这可能会产生一个显著的性能差异，尤其是如果ORDER BY 与LIMIT或者其他限制结合。仅当顺序列的并发更新是预期的并且需要一个严格的排序结果时， 该技术才是建议使用的。<br/><br/>

在REPEATABLE READ或SERIALIZABLE事务隔离级别， 这可能会导致一个序列化失败（SQLSTATE为'40001'）， 所以在这些隔离级别下接收次序混乱的行是不可能的。</p>-->

<a href="table_ming_ling.md">**TABLE 命令**</a>

<!--命令：
```SQL
TABLE name
```
完全等价于：
```SQL
SELECT * FROM name
```
它可以用作复杂查询中的一部分的一个顶级的命令或者一个节省空间的语法变体。-->

<br/>**命令**

将表films和表distributors连接在一起：
```SQL
SELECT f.title, f.did, d.name, f.date_prod, f.kind
    FROM distributors d, films f
    WHERE f.did = d.did

       title       | did |     name     | date_prod  |   kind
-------------------+-----+--------------+------------+----------
 The Third Man     | 101 | British Lion | 1949-12-23 | Drama
 The African Queen | 101 | British Lion | 1951-08-11 | Romantic
 ...
```
统计用kind分组的每组电影的长度len总和：
```SQL
SELECT kind, sum(len) AS total FROM films GROUP BY kind;

   kind   | total
----------+-------
 Action   | 07:34
 Comedy   | 02:58
 Drama    | 14:28
 Musical  | 06:42
 Romantic | 04:38
```
统计用kind分组的每组电影的长度len总和不足五小时的组：
```SQL
SELECT kind, sum(len) AS total
    FROM films
    GROUP BY kind
    HAVING sum(len) < interval '5 hours';

   kind   | total
----------+-------
 Comedy   | 02:58
 Romantic | 04:38
```
下面两个例子是根据第二列(name)的内容对单独的结果排序的相同的方法：
```SQL
SELECT * FROM distributors ORDER BY name;
SELECT * FROM distributors ORDER BY 2;

 did |       name
-----+------------------
 109 | 20th Century Fox
 110 | Bavaria Atelier
 101 | British Lion
 107 | Columbia
 102 | Jean Luc Godard
 113 | Luso films
 104 | Mosfilm
 103 | Paramount
 106 | Toho
 105 | United Artists
 111 | Walt Disney
 112 | Warner Bros.
 108 | Westward
```
下面这个例子演示如何获得表distributors和actors的连接， 只将每个表中以字母 W 开头的取出来。因为只取了不重复的行，所以关键字ALL被省略了：
```SQL
distributors:               actors:
 did |     name              id |     name
-----+--------------        ----+----------------
 108 | Westward               1 | Woody Allen
 111 | Walt Disney            2 | Warren Beatty
 112 | Warner Bros.           3 | Walter Matthau
 ...                         ...

SELECT distributors.name
    FROM distributors
    WHERE distributors.name LIKE 'W%'
UNION
SELECT actors.name
    FROM actors
    WHERE actors.name LIKE 'W%';

      name
----------------
 Walt Disney
 Walter Matthau
 Warner Bros.
 Warren Beatty
 Westward
 Woody Allen
```
<!--这个例子显示了如何在FROM子句中使用函数，包括带有和不带字段定义列表的。
```SQL
CREATE FUNCTION distributors(int) RETURNS SETOF distributors AS $$
    SELECT * FROM distributors WHERE did = $1;
$$ LANGUAGE SQL;

SELECT * FROM distributors(111);
 did |    name
-----+-------------
 111 | Walt Disney

CREATE FUNCTION distributors_2(int) RETURNS SETOF record AS $$
    SELECT * FROM distributors WHERE did = $1;
$$ LANGUAGE SQL;

SELECT * FROM distributors_2(111) AS (f1 int, f2 text);
 f1  |     f2
-----+-------------
 111 | Walt Disney
```-->
<!--这个例子显示了如何使用一个简单的WITH子句：
```SQL
WITH t AS (
    SELECT random() as x FROM generate_series(1, 3)
  )
SELECT * FROM t
UNION ALL
SELECT * FROM t

         x          
--------------------
  0.534150459803641
  0.520092216785997
 0.0735620250925422
  0.534150459803641
  0.520092216785997
 0.0735620250925422
```
请注意WITH查询只评估一次，所以我们获得两组相同的三个随机值。

这个例子使用WITH RECURSIVE 从一个只显示直接属下的表中找到所有职员Mary的属下（直接或间接），和他们的间接级别，
```SQL
WITH RECURSIVE employee_recursive(distance, employee_name, manager_name) AS (
    SELECT 1, employee_name, manager_name
    FROM employee
    WHERE manager_name = 'Mary'
  UNION ALL
    SELECT er.distance + 1, e.employee_name, e.manager_name
    FROM employee_recursive er, employee e
    WHERE er.employee_name = e.manager_name
  )
SELECT distance, employee_name FROM employee_recursive;
```
请注意递归查询的典型形式：一个初始条件，紧接着是UNION， 然后是查询的递归部分。确定查询的递归部分最终将不会返回元组，否则查询将无限循环下去。 -->

这个例子使用LATERAL为manufacturers表的每行应用一个设置返回函数 get_product_names()：
```SQL
SELECT m.name AS mname, pname
FROM manufacturers m, LATERAL get_product_names(m.id) pname;
```
当前没有任何产品的制造商将不会出现在结果中，因为这是一个内连接。 如果我们希望在结果中包括这种制造商的名字，我们可以这样做：
```SQL
SELECT m.name AS mname, pname
FROM manufacturers m LEFT JOIN LATERAL get_product_names(m.id) pname ON true;
```
<br/>**兼容性**

SELECT语句和 SQL 标准兼容。但是还有一些扩展和一些缺少的特性。

**省略 FROM 子句**

EdsSQL允许在一个查询里省略FROM子句。 它的最直接用途就是计算简单的常量表达式的结果：
```SQL
SELECT 2+2;

 ?column?
----------
        4
```
其它有些SQL数据库不能这么做，除非引入一个单行的伪表做为 SELECT的数据源。

请注意，如果没有声明FROM子句，那么查询不能引用任何数据库表。 比如，下面的查询是非法的：
```SQL
SELECT distributors.* WHERE distributors.name = 'Westward';
```
<!--PostgreSQL 8.1 之前的版本支持这种形式的查询， 为查询里引用的每个表都增加一个隐含的条目到FROM子句中。 现在这个不再是允许的了。-->

**省略 AS 关键字**

在SQL标准中，每当新列名称是一个有效的列名时（也就是，与任意保留关键字都不同）， 可选的关键字AS可以在输出列名之前省略。EdsSQL 限制略多一些：不管是保留还是不保留，如果新列名匹配任何关键字，AS是必要的。 建议的做法是使用AS或者双括号括起输出列名称，以阻止与将来补充的关键字有任何可能的冲突。

在FROM项中，标准和EdsSQL都允许AS 在一个无限制关键字别名之前省略。但是这对输出列名是不切实际的，因为语法的歧义。

**ONLY和继承**

SQL标准需要在写ONLY时括号括起表名，例如 SELECT * FROM ONLY (tab1), ONLY (tab2) WHERE ...。 EdsSQL认为括号是可选的。

EdsSQL允许写一个尾随的*以明确指定包括子表的非 ONLY行为。标准不允许这样。

（该点同样适用于所有支持ONLY选项的SQL命令。）

<!--**FROM中的函数调用**

EdsSQL允许把函数调用直接写作FROM列表的一个成员。 在SQL标准中应该有必要在子SELECT中包含这样一个函数；也就是， 该语法FROM func(...) alias等价于 FROM LATERAL (SELECT func(...)) alias。 请注意LATERAL被认为是隐含的；这是因为标准要求LATERAL语义为 一个在FROM中的UNNEST()条目。EdsSQL 对待UNNEST()和其他设置返回函数相同。-->

<!--**GROUP BY 和 ORDER BY里可用的命名空间**

在 SQL-92 标准里，ORDER BY子句只能使用输出字段名或者编号， 而GROUP BY子句只能用基于输入字段名的表达式。EdsSQL 对这两个子句都进行了扩展，允许另外一种选择(但是如果存在歧义，则使用标准的解释)。 EdsSQL还允许两个子句声明任意的表达式。 请注意在表达式中出现的名字总是被当作输入字段名，而不是输出字段名。

SQL:1999 以及之后的一个略微不同的定义并不能和 SQL-92 完全向前兼容。不过， 在大多数情况下，EdsSQL将把一个ORDER BY 或GROUP BY表达式解析成为 SQL:1999 制定的那样。-->

<!--**函数依赖**

EdsSQL只在表的主键包含在GROUP BY 列表中时识别函数依赖（允许在GROUP BY中省略字段）。 SQL标准指定应该被识别的附加条件。-->

<!--**WINDOW 子句限制**

SQL标准为窗口frame_clause提供了附加选项。 EdsSQL目前仅支持上面列出的选项。-->

<!--**LIMIT 和 OFFSET**

子句LIMIT和OFFSET是EdsSQL 特定的语法，也是MySQL使用的。SQL:2008标准引入了 OFFSET ... FETCH {FIRST|NEXT} ...获取相同的功能， 如上面的LIMIT 子句所示。 该语法也被IBM DB2使用。（为Oracle 所写的应用程序通常使用一个涉及自动生成的rownum列的工作区， 来实现这些子句的效果，这在EdsSQL中是不可用的。）-->

<!--** FOR SHARE, FOR KEY SHARE**

EdsSQL允许它出现在任意SELECT 查询和子SELECT查询中，但是这是一个扩展。FOR SHARE, FOR KEY SHARE变体和NOWAIT选项， 没有出现在标准中。-->

<!--**WITH中的数据修改语句**

EdsSQL允许INSERT, DELETE 用作WITH查询。这在SQL标准中是没有的。-->

**非标准的子句**

子句DISTINCT ON在SQL标准中未定义。