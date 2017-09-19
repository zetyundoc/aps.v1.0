# 聚合函数

聚合函数从一组输入值里计算一个结果。 表-1和表-2 显示了内建的聚合函数。

表-1 通用聚合函数

|函数|	参数类型|	返回类型|	描述|
| -- | -- | -- | -- |
|array_agg(expression)	| 任意|	 参数类型的数组	|输入值，包括空，连接到一个数组|
|avg(expression)|	smallint, int, bigint, real, double precision, numeric, or interval|	 对于任何整数类型输入，结果都是numeric类型。 对于任何浮点输入，结果都是double precision类型。 否则和输入数据类型相同。|	所有输入值的均值(算术平均)|
|bit_and(expression)|	smallint, int, bigint, or bit	| 和参数数据类型相同|	所有非 NULL 输入值的按位与(AND)，如果全部输入值皆为 NULL ，那么结果也为 NULL 。|
|bit_or(expression)|	smallint, int, bigint, or bit|	 和参数数据类型相同|	所有非 NULL 输入值的按位或(OR)，如果全部输入值皆为 NULL ，那么结果也为 NULL 。|
|bool_and(expression)	|bool|	bool	|如果所有输入值都是真，则为真，否则为假。|
|bool_or(expression)	|bool|	bool|	如果至少有一个输入值为真，则为真，否则为假。|
|count(*)|	 	|bigint	|输入行数|
|count(expression)	|任意|	bigint|	 计算所有输入行中满足expression不为 NULL 的行数。|
|every(expression)	|bool|	bool|	等效于bool_and|
|json_agg(record)|	record|	json|	聚合记录作为JSON对象数组|
|max(expression)|	任意数组、数值、字符串、日期/时间类型|	和参数数据类型相同|	 有输入行中expression的最大值|
|min(expression)	|任意数组、数值、字符串、日期/时间类型|	和参数数据类型相同	| 所有输入行中expression的最小值|
|string_agg(expression, delimiter)	| (text, text) or (bytea, bytea)|	 和参数数据类型相同|	输入值连接成为一个字符串，用分隔符分开|
|sum(expression)|	smallint, int, bigint, real, double precision, numeric, or interval|	 对于smallint或int输入，输出类型为bigint。 对于bigint输入，输出类型为numeric，对于浮点数输入， 输出类型为double precision。否则和输入数据类型相同。|	所有输入行的expression总和。|
|xmlagg(expression)	|xml|	xml	|连接 XML 值 |

请注意，除了count以外，这些函数在没有输入行时返回 NULL 。 尤其要指出的是sum函数在没有输入行时返回 NULL ，而不是零。 array_agg函数在没有输入行时返回null而不是空数组。 必要时可以用coalesce把 NULL 替换成零或空数组。

<p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >注意: bool_and和bool_or布尔聚合对应标准的 SQL 聚合every和any或some。 对于any和some，标准语法里面似乎有些内置的歧义：<br/>
<code>SELECT b1 = ANY((SELECT b2 FROM t2 ...)) FROM t1 ...;</code><br/>
这里的ANY既可以被认为是引出一个子查询， 也可以被认为是一个聚合(如果子查询返回布尔值的1行的话)。因此标准的名字无法用于这些聚合。</p>

<p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >注意: 习惯了其它 SQL 数据库管理系统的用户可能被用于全表计算的count 的性能(之慢)惊住了。一个类似下面这样的查询：
<br/>
<code>SELECT count(*) FROM sometable;</code><br/>
将需要努力与表的大小成正比：EdsSQL 将需要扫面整个表或包含表中所有行的完整的索引。
</p>

聚合函数array_agg，json_agg，string_agg 和xmlagg，以及类似用户定义的聚合函数，根据输入值的顺序产生意义不同的结果值。 这个顺序默认没有指定，但是可以通过在聚合函数调用时，写一个ORDER BY子句来控制。另外， 通常可以从一个已排序的子查询中提供输入值。例如：
```SQL
SELECT xmlagg(x) FROM (SELECT x FROM test ORDER BY y DESC) AS tab;
```
但此语法在SQL标准里不允许，不能移植到其它数据库系统。

表-2 展示了用于统计分析的聚合函数。 单独列出这些函数仅仅是为了避免和那些经常使用的聚合函数混在一起而已。 "描述"列中的N 表示所有输入行中使得输入表达式不为 NULL 的行数。总的来说，如果计算本身变得没有意义， 那么返回值将是 NULL 。例如当N为零的时候。

表-2 统计聚合函数

|函数|	参数类型|	返回类型 |描述|
| -- | -- | -- | -- |
|corr(Y, X)|	double precision|	double precision|	相关系数|
|covar_pop(Y, X)	|double precision	|double precision|	总体协方差|
|covar_samp(Y, X)|	double precision|	double precision|	样本协方差|
|regr_avgx(Y, X)|	double precision|	double precision|	自变量的平均值 (sum(X)/N)|
|regr_avgy(Y, X)|	double precision|	double precision|	因变量的平均值 (sum(Y)/N)|
|regr_count(Y, X)|	double precision|	bigint	|两个表达式都不为 NULL 的输入行数
|regr_intercept(Y, X)|	double precision|	double precision	| 根据所有输入的点(X, Y)按照最小二乘法拟合成一个线性方程，然后返回该直线的 Y 轴截距|
|regr_r2(Y, X)|	double precision	|double precision|	相关系数的平方|
|regr_slope(Y, X)|	double precision|	double precision|	 根据所有输入的点(X, Y)按照最小二乘法拟合成一个线性方程， 然后返回该直线的斜率。|
|regr_sxx(Y, X)|	double precision|	double precision|	sum(X^2) - sum(X)^2/N (自变量的"平方和")|
|regr_sxy(Y, X)|	double precision|	double precision|	sum(X*Y) - sum(X) * sum(Y)/N (自变量和因变量的"乘方积")|
|regr_syy(Y, X)|	double precision|	double precision|	sum(Y^2) - sum(Y)^2/N (因变量的"平方和")|
|stddev(expression)	|smallint, int, bigint, real, double precision, 或 numeric|	 对于浮点类型的输入返回double precision，其他输入返回numeric|	stddev_samp的别名(历史原因)|
|stddev_pop(expression)	|smallint, int, bigint, real, double precision, 或 numeric	 |对于浮点类型的输入返回double precision，其他输入返回numeric|	总体标准差|
|stddev_samp(expression)|	smallint, int, bigint, real, double precision, 或 numeric	| 对于浮点类型的输入返回double precision，其他输入返回numeric|	样本标准差|
|variance(expression)|	smallint, int, bigint, real, double precision, 或 numeric	 |对于浮点类型的输入返回double precision，其他输入返回numeric|	var_samp的别名(历史原因)|
|var_pop(expression)|	smallint, int, bigint, real, double precision, 或 numeric	| 对于浮点类型的输入返回double precision，其他输入返回numeric|	总体方差(总体标准差的平方)|
|var_samp(expression)|	smallint, int, bigint, real, double precision, 或 numeric	 |对于浮点类型的输入返回double precision，其他输入返回numeric|	样本方差(样本标准差的平方)|