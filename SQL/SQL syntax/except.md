# EXCEPT


EXCEPT子句有如下的通用形式：
```SQL
select_statement EXCEPT [ ALL | DISTINCT ] select_statement
```
select_statement是任何没有 ORDER BY, LIMIT<!--, FOR NO KEY UPDATE, FOR UPDATE-->, FOR SHARE, 或 FOR KEY SHARE子句的SELECT表达式。

EXCEPT操作符计算存在于左边SELECT 语句的输出而不存在于右边SELECT语句输出的行。

EXCEPT的结果不包含任何重复的行，除非声明了ALL选项。 使用ALL时，一个在左边表中有m个重复而在右边表中有 n个重复的行将在结果中出现max(m-n,0)次。 DISTINCT可以明确的指定消除重复行的缺省行为。

除非用圆括弧指明顺序，否则同一个SELECT语句中的多个 EXCEPT操作符是从左向右计算的。EXCEPT 和UNION的绑定级别相同。

目前，不能给EXCEPT的结果或者任何EXCEPT的输入声明<!-- FOR NO KEY UPDATE, FOR UPDATE, -->FOR SHARE 和 FOR KEY SHARE子句。

<br/>**参见**

SELECT

