# DISTINCT

如果声明了SELECT DISTINCT，那么就从结果集中删除所有重复的行 (每个有重复的组都保留一行)。SELECT ALL声明相反的作用：所有行都被保留(这是缺省)。

SELECT DISTINCT ON ( *expression* [, ...] ) 只保留那些在给出的表达式上运算出相同结果的行集合中的第一行。DISTINCT ON 表达式是使用与ORDER BY相同的规则进行解释的。请注意， 除非使用了ORDER BY来保证需要的行首先出现，否则，"第一行" 是不可预测的。比如：
```SQL
SELECT DISTINCT ON (location) location, time, report
    FROM weather_reports
    ORDER BY location, time DESC;
```
为每个地点检索最近的天气报告。但是如果没有使用ORDER BY 来强制对每个地点的时间值进行降序排序，那么就会得到每个地点的不知道什么时候的报告。

DISTINCT ON表达式必须匹配最左边的ORDER BY表达式。 ORDER BY子句将通常包含额外的表达式来判断每个DISTINCT ON 组里面需要的行的优先级。

<br/>**参见**

SELECT