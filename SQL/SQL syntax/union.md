# UNION

UNION子句的一般形式是：
```SQL
select_statement UNION [ ALL | DISTINCT ] select_statement
```
这里的*select_statement*是任意没有 ORDER BY, LIMIT, <!--FOR NO KEY UPDATE, FOR UPDATE, -->FOR SHARE, 或 FOR KEY SHARE子句的SELECT语句。 如果用圆括弧包围，ORDER BY和LIMIT可以附着在子表达式里。 如果没有圆括弧，这些子句将交给UNION的结果使用，而不是给它们右边的输入表达式。

UNION操作符计算那些涉及到的所有SELECT 语句返回的行的结果联合。一个行如果至少在两个结果集中的一个里面出现， 那么它就会在这两个结果集的集合联合中。两个作为UNION 直接操作数的SELECT必须生成相同数目的字段， 并且对应的字段必须有兼容的数据类型。

缺省的UNION结果不包含任何重复的行，除非声明了ALL选项。 ALL制止了消除重复的动作。因此，UNION ALL通常比 UNION明显要快，可能的情况下尽量使用ALL。 DISTINCT可以明确的指定消除重复行的缺省行为。

同一个SELECT语句中的多个UNION操作符是从左向右计算的， 除非用圆括弧进行了标识。

目前，<!--FOR NO KEY UPDATE, FOR UPDATE, -->FOR SHARE 和 FOR KEY SHARE不能在UNION的结果或输入中声明。

<br/>**参见**

SELECT