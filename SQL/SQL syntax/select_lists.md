# SELECT LISTS

SELECT列表(在SELECT和FROM关键字之间的部分) 声明组成SELECT语句的输出行的表达式。这些表达式可以 (并且通常也会)引用在FROM子句里面计算出来的字段。

就像在一个表中，一个SELECT的每个输出列都有一个名称。 在一个简单的SELECT中，该名称仅用于标记显示的列， 但当SELECT是一个较大查询的子查询时，名称被较大查询视为子查询产生的虚表的字段名。 为了指定用于输出列的名称，要在列表达式后写AS *output_name*。为了防止将来可能的关键字添加， 建议您要么写AS要么用双引号引起输出名称。）如果你不指定一个字段名， EdsSQL会自动选择一个名称。如果字段的表达式是一个简单的列引用， 那么选择的名称与字段名相同；在更复杂的情况下，可能会使用一个函数或类型名， 或系统会依赖于一个类似?column?的生成名。

一个输出列的名称可以用来参考ORDER BY和GROUP BY子句中的字段的值， 而不是在WHERE或者HAVING子句中的；反而您必须在那里写出表达式。

除了表达式，也可以在输出列表中使用表示所有字段。 还可以用*table_name*. 作为来自该表的所有字段的缩写。这些情况下用AS指定新名称是不可能的； 输出列的名称将会与表列的名称相同。

<br/>**示例**

```SQL
select * from t;

select 1 + a from t;

select a as b from t;

select a+b, c+d from t;

select top 10 col from tbl;

select col from tbl limit 10;

select distinct a,b from t;
```

<br/>**参见**

SELECT