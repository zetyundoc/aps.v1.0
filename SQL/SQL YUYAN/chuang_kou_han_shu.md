# 窗口函数

窗口函数提供跨行相关的当前查询行集执行计算的能力。 

表-1 列出了内建的窗口函数。 注意必须使用窗口函数的语法调用这些函数；一个OVER子句是必需的。

除了这些函数外，任何内建的或用户定义的聚合函数都可以作为窗口函数。 仅当调用跟着OVER子句的聚合函数，作为窗口函数；否则它们作为常规的聚合。

表-1 通用窗口函数

|函数|	返回类型|	描述|
| -- | -- | -- | -- |
|row_number()|	bigint|	在其分区中的当前行号，从1计|
|rank()	|bigint|	有间隔的当前行排名；与它的第一个相同行的row_number相同|
|dense_rank()|	bigint|	没有间隔的当前行排名；这个函数计数对等组。|
|percent_rank()	|double precision	当前行的相对排名: (rank - 1) / (总行数 - 1)|
|cume_dist()|	double precision|	当前行的相对排名：(前面的行数或与当前行相同的行数)/(总行数)|
|ntile(num_buckets integer)	|integer|	从1到参数值的整数范围，尽可能相等的划分分区。
|lag(value any [, offset integer [, default any ]])	|类型同 value|	 计算分区当前行的前offset 行，返回value 。如果没有这样的行， 返回default替代。 offset和default 都是当前行计算的结果。如果忽略了，则offset 默认是1，default默认是 null。|
|lead(value any [, offset integer [, default any ]])	|类型同value|	 计算分区当前行的后offset行， 返回value。如果没有这样的行， 返回default替代。 offset和default 都是当前行计算的结果。如果忽略了，则offset 默认是1，default默认是 null。|
|first_value(value any)	|类型同value|	 返回窗口第一行的计算value值。|
|last_value(value any)|	类型同value|	 返回窗口最后一行的计算value值。|
|nth_value(value any, nth integer)|	类型同value|	 返回窗口第nth行的计算 value值（行从1计数）；没有这样的行则返回 null。|

在表-1 列出的所有函数， 依赖于与窗口定义有关的ORDER BY子句指定的排序。 同行是说在ORDER BY排序时不唯一的行。 定义的这四个排名函数，对于任何两个同行的答案相同。

注意first_value，last_value，和nth_value 只考虑"window frame"内的行，其默认情况下，包含从分区的开始行直到当前行的最后同行。 像last_value和nth_value有时会给出没有用的结果。 您可以通过向OVER子句添加合适的框架规范（RANGE或者ROWS） 来重新定义该框架。

当一个聚合函数作为窗口函数使用时，将聚合超过当前行的窗框内的行。 一个使用ORDER BY和默认窗框定义处理"运行时求和"类型的行为的聚合函数， 可能不是想要的结果。为了获取超过整个分区聚合，忽略ORDER BY 或者使用ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING。 其它窗框规格可以用来获取其它的效果。

<p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >注意: SQL 标准为lead, lag, first_value, last_value, 和nth_value定义了一个RESPECT NULLS或IGNORE NULLS选项。 这个在EdsSQL没有实现：行为总是与标准默认相同，即RESPECT NULLS。 同样用于nth_value的标准FROM FIRST或FROM LAST 选项也没有实现：只支持默认FROM FIRST行为。 （您可以通过ORDER BY排序取反获取到FROM LAST的结果。）</p>