# INTERSECT

INTERSECT子句的一般形式是：
```SQL
select_statement INTERSECT [ ALL | DISTINCT ] select_statement
```
*select_statement*是任何不带 ORDER BY, LIMIT<!--, FOR NO KEY UPDATE, FOR UPDATE-->, FOR SHARE, 或 FOR KEY SHARE子句的SELECT语句。

INTERSECT计算涉及的SELECT语句返回的行集合的交集。 如果一个行在两个结果集中都出现，那么它就在两个结果集的交集中。

INTERSECT的结果不包含任何重复行，除非你声明了ALL选项。 用了ALL以后，一个在左边的表里有m个重复而在右边表里有 n个重复的行将在结果集中出现min(*m,n*)次。 DISTINCT可以明确的指定消除重复行的缺省行为。

除非用圆括号指明顺序，同一个SELECT语句中的多个 INTERSECT操作符是从左向右计算的。INTERSECT 比UNION绑定得更紧，也就是说A UNION B INTERSECT C 将理解成 A UNION (B INTERSECT C)。

目前，不能给INTERSECT的结果或者任何INTERSECT的输入声明 <!--FOR NO KEY UPDATE, FOR UPDATE, -->FOR SHARE 和 FOR KEY SHARE。

<br/>**参见**

SELECT

