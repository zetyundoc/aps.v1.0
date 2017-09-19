# EXPLAIN（企业版支持）
<br/>**命令**
```SQL
EXPLAIN [ ( option [, ...] ) ] statement
EXPLAIN [ ANALYZE ] [ VERBOSE ] statement

这里的 option可以是下列之一:

ANALYZE [ boolean ]
VERBOSE [ boolean ]
COSTS [ boolean ]
BUFFERS [ boolean ]
TIMING [ boolean ]
FORMAT { TEXT | XML | JSON | YAML }
```
<br/>**描述**

这条命令显示EdsSQL规划器为所提供的语句生成的执行规划。 执行规划显示语句引用的表是如何被扫描的(简单的顺序扫描，还是索扫描)，并且如果引用了多个表， 采用了什么样的连接算法从每个输入的表中取出所需要的记录。

显示出来的最关键的部分是预计的语句执行开销，这就是规划器对运行该语句所需时间的估计 (以任意的开销单位计量，但是通常意味着磁盘页面存取)。实际上显示了两个数字： 返回第一行记录前的启动开销，和返回所有记录的总开销。对于大多数查询而言，关心的是总开销， 但是，在某些环境下，比如一个EXISTS子查询里， 规划器将选择最小启动开销而不是最小总开销(因为执行器在获取一条记录后总是要停下来)。 同样，如果你用一条LIMIT子句限制返回的记录数， 规划器会在最终的开销上做一个合理的插值以计算哪个规划开销最省。

ANALYZE选项导致查询被实际执行，而不仅仅是规划。显示中加入了实际的运行时间统计， 包括在每个规划节点内部花掉的总时间(以毫秒计)和它实际返回的行数。 这些数据对搜索该规划器的预期是否和现实相近很有帮助。

 <p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >注意：要记住的是查询实际上在使用ANALYZE选项的时候是执行的。 尽管EXPLAIN会抛弃任何SELECT返回的输出， 但是其它查询的副作用还是一样会发生的。如果你在INSERT<!--, UPDATE, DELETE,--> CREATE TABLE AS, EXECUTE 语句里使用EXPLAIN ANALYZE而且还不想让查询影响数据，可以用下面的方法：</p>
 ```SQL
BEGIN;
EXPLAIN ANALYZE ...;
ROLLBACK;
```

<!--只能声明ANALYZE和VERBOSE选项，并且只能以那种顺序， 不能将选项列表放在圆括号中。PostgreSQL 9.0之前， 只支持不用圆括号的语法。人们希望只在圆括号语法中支持所有新的选项。-->

<br/>**参数**

ANALYZE：执行命令并显示实际运行时间和其他统计。这个参数缺省为FALSE。

VERBOSE：显示关于规划的额外的信息。特别的包括规划树上的每个节点的输出字段列表， 模式修饰表和函数名，表达式中的标签变量总是和他们的范围表别名在一起， 并且总是打印统计数据中显示的每个触发器的名字。这个参数缺省为FALSE。

COSTS：包括每个规划节点的估计启动成本和总成本的信息，也包括估计行数和估计的每行的宽度。 这个参数缺省为TRUE。

BUFFERS：包含缓冲区使用的信息。特别的，包括共享块命中、读、脏和写的次数，本地块命中、读、脏和写的次数， 临时块读和写的次数。命中意味着避免了读，因为块在需要时已经在缓存中发现了。 共享块包含普通表和索引的数据；本地块包含临时表和索引的数据；而临时块包含用于排序、哈希、 物化规划节点和相似情况的短期工作数据。脏块的数量表示这个查询改变的先前未更改的块的数量； 写块的数量表示在查询处理的时候被这个后端驱逐出缓存的先前脏了的块的数量。 高级节点显示的块的数量包含所有它的子节点使用的块的数量。在文本格式中，只打印非零值。 这个参数可能只在ANALYZE也启用的时候使用。它的缺省为FALSE。

TIMING：在输出中包含实际启动时间和每个节点花费的时间。重复读系统块的总开销会在某些系统上显著的减缓查询的速度， 所以当需要只有实际行被计算，并且没有准确时间时，设置这个参数为FALSE会很有用。 即使是用这个选项关闭了节点级别的时间，也测量整个语句的运行时间。 这个参数可能只在ANALYZE也启用的时候使用。它缺省为TRUE。

FORMAT：声明输出格式，可以为TEXT, XML, JSON 或 YAML。非文本的输出包含文本输出格式相同的信息， 但是更容易被程序解析。这个参数缺省为TEXT。

*boolean*：声明选中的选项打开或者关闭。可以用TRUE, ON 或 1 启用这个选项，用FALSE, OFF 或 0 禁用这个选项。在假设为TRUE的情况下， boolean值也可以忽略，

*statement*：你想要查看执行规划的任何SELECT, INSERT<!--, UPDATE, DELETE,--> VALUES, EXECUTE, DECLARE, 或 CREATE TABLE AS语句之一。

<!--<br/>**输出**

命令的结果是从statement选择的规划的文字描述， 可选的有执行统计数据的注释。-->

<br/>**注意**

为了让EdsSQL查询规划器在优化查询的时候做出合理的判断， eds_statistic 数据应该为所有用于查询的表更新。通常autovacuum daemon 会自动注意这些。但是如果一个表最近在内容上有大量的更改，你可能需要手动ANALYZE 而不是等待autovacuum赶上变化。

为了测量运行时在执行规划中每个节点的开销，EXPLAIN ANALYZE 的当前应用增加查询执行开销的性能分析。结果，在一个查询上运行EXPLAIN ANALYZE 有时会比普通查询明显的花费更多的时间。超支的数量依赖于查询的本质和使用的平台。 最坏的情况发生在他们本身的规划节点时每个执行需要非常少的时间， 并且在操作系统相当慢的机器上需要获得时刻。

<br/>**示例**

显示一个对只有一个integer列和 10000 行表的简单查询的查询规划：
```SQL
EXPLAIN SELECT * FROM foo;

                       QUERY PLAN
---------------------------------------------------------
 Seq Scan on foo  (cost=0.00..155.00 rows=10000 width=4)
(1 row)
```
相同的查询用JSON输出格式：
```SQL
EXPLAIN (FORMAT JSON) SELECT * FROM foo;
           QUERY PLAN
--------------------------------
 [                             +
   {                           +
     "Plan": {                 +
       "Node Type": "Seq Scan",+
       "Relation Name": "foo", +
       "Alias": "foo",         +
       "Startup Cost": 0.00,   +
       "Total Cost": 155.00,   +
       "Plan Rows": 10000,     +
       "Plan Width": 4         +
     }                         +
   }                           +
 ]
(1 row)
```
如果存在一个索引，并且使用一个可应用索引的WHERE条件的查询， EXPLAIN会显示不同的规划：
```SQL
EXPLAIN SELECT * FROM foo WHERE i = 4;

                         QUERY PLAN
--------------------------------------------------------------
 Index Scan using fi on foo  (cost=0.00..5.98 rows=1 width=4)
   Index Cond: (i = 4)
(2 rows)
```
相同的查询，但是用YAML格式：
```SQL
EXPLAIN (FORMAT YAML) SELECT * FROM foo WHERE i='4';
          QUERY PLAN
-------------------------------
 - Plan:                      +
     Node Type: "Index Scan"  +
     Scan Direction: "Forward"+
     Index Name: "fi"         +
     Relation Name: "foo"     +
     Alias: "foo"             +
     Startup Cost: 0.00       +
     Total Cost: 5.98         +
     Plan Rows: 1             +
     Plan Width: 4            +
     Index Cond: "(i = 4)"
(1 row)
```
XML格式留给读者做练习。

这是相同的查询关闭成本估算：
```SQL
EXPLAIN (COSTS FALSE) SELECT * FROM foo WHERE i = 4;

        QUERY PLAN
----------------------------
 Index Scan using fi on foo
   Index Cond: (i = 4)
(2 rows)
```
下面是一个使用了聚合函数的查询的查询规划的例子：
```SQL
EXPLAIN SELECT sum(i) FROM foo WHERE i < 10;

                             QUERY PLAN
---------------------------------------------------------------------
 Aggregate  (cost=23.93..23.93 rows=1 width=4)
   ->  Index Scan using fi on foo  (cost=0.00..23.92 rows=6 width=4)
         Index Cond: (i < 10)
(3 rows)
```
下面是一个使用EXPLAIN EXECUTE显示一个已预编写的查询规划的例子：
```SQL
PREPARE query(int, int) AS SELECT sum(bar) FROM test
    WHERE id > $1 AND id < $2
    GROUP BY foo;

EXPLAIN ANALYZE EXECUTE query(100, 200);

                                                       QUERY PLAN
-------------------------------------------------------------------------------------------------------------------------
 HashAggregate  (cost=39.53..39.53 rows=1 width=8) (actual time=0.661..0.672 rows=7 loops=1)
   ->  Index Scan using test_pkey on test  (cost=0.00..32.97 rows=1311 width=8) (actual time=0.050..0.395 rows=99 loops=1)
         Index Cond: ((id > $1) AND (id < $2))
 Total runtime: 0.851 ms
(4 rows)
```
注意这里显示的数字，依赖于所包含的表的实际内容。还请注意该数字甚至还有选择的查询策略都有可能在各个 EdsSQL版本之间不同，因为规划器在不断改进。另外，ANALYZE 命令使用随机的采样来估计数据统计；因此，一次新的ANALYZE 运行之后开销估计可能会变化，即使数据的实际分布没有改变也这样。

<br/>**兼容性**

在 SQL 标准中没有EXPLAIN语句。

<br/>**参见**

ANALYZE