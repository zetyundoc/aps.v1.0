# ORDER BY

可选的ORDER BY子句有下面的一般形式：
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

字符类型的数据是按照应用于被排序的字段的排序规则排序的。可以在需要时通过在 *expression*中包含COLLATE 子句覆盖，例如ORDER BY mycolumn COLLATE "en_US"。

<br/>**示例**
```SQL
select * from t order by a asc, b desc;
```

<br/>**兼容性**

**GROUP BY 和 ORDER BY里可用的命名空间**

在 SQL-92 标准里，ORDER BY子句只能使用输出字段名或者编号， 而GROUP BY子句只能用基于输入字段名的表达式。EdsSQL 对这两个子句都进行了扩展，允许另外一种选择(但是如果存在歧义，则使用标准的解释)。 EdsSQL还允许两个子句声明任意的表达式。 请注意在表达式中出现的名字总是被当作输入字段名，而不是输出字段名。

SQL:1999 以及之后的一个略微不同的定义并不能和 SQL-92 完全向前兼容。不过， 在大多数情况下，EdsSQL将把一个ORDER BY 或GROUP BY表达式解析成为 SQL:1999 制定的那样。

<br/>**参见**

SELECT

