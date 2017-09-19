# WINDOW

WINDOW子句的一般形式是
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

一个WINDOW语句的目的是指定出现在查询的SELECT 列表 或者ORDER BY 子句中的window 函数的行为。 这些函数可以在其OVER子句中通过名称引用WINDOW子句条目。 一个WINDOW子句条目不需要在任何地方都引用；若它不在查询中使用，它将被忽略。 可以使用窗口程序而根本不需要任何WINDOW子句， 因为一个窗口函数调用可以直接在其OVER子句中指定其窗口定义。然而， WINDOW子句会在多个窗口函数需要相同窗口定义时保存输入。

<br/>**兼容性**

**WINDOW 子句限制**

SQL标准为窗口frame_clause提供了附加选项。 EdsSQL目前仅支持上面列出的选项。

<br/>**参见**

SELECT