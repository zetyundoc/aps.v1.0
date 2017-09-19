# HAVING

可选的HAVING子句有如下形式：
```SQL
HAVING condition
```
这里*condition*与为 WHERE子句里声明的相同。

HAVING去除了一些不满足条件的组行。它与WHERE 不同：WHERE在使用GROUP BY之前过滤出单独的行， 而HAVING过滤由GROUP BY创建的行。 在condition 里引用的每个字段都必须无歧义地引用一个分组的行，除非引用出现在一个聚合函数里。

HAVING的出现把查询变成一个分组的查询，即使没有GROUP BY 子句也这样。这一点和那些包含聚合函数但没有GROUP BY子句的查询里发生的事情是一样的。 所有选取的行都被认为会形成一个单一的组，而SELECT 列表和HAVING子句只能从聚合函数里面引用表的字段。 这样的查询在HAVING条件为真的时候将发出一个行，如果为非真，则返回零行。

<br/>**示例**

```SQL
select a,sum(b) from t group by a having sum(b) > 5;
```

<br/>**参见**

SELECT