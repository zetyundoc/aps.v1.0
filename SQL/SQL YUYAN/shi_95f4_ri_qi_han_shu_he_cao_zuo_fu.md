# 时间/日期函数和操作符

表-2 显示了可以用于处理日期/时间数值的函数。表-1 演示了基本算术操作符的行为(+,*, 等)。 

所有下述函数和操作符接收的time或timestamp输入实际上都来自两种可能： 一种是接收time with time zone或timestamp with time zone， 另外一种是接收time without time zone或timestamp without time zone。 出于简化考虑，这些变种没有独立显示出来。还有，+和* 操作符都是以可交换的操作符对(比如，date + integer 和 integer + date)； 我们只显示了这样的交换操作符对中的一个。

表-1 日期/时间操做符

|操作符|	例子|	结果|
| -- | -- | -- |
|+|	date '2001-09-28' + integer '7'	|date '2001-10-05'|
|+|	date '2001-09-28' + interval '1 hour'	|timestamp '2001-09-28 01:00:00'|
|+|	date '2001-09-28' + time '03:00'	|timestamp '2001-09-28 03:00:00'|
|+|	interval '1 day' + interval '1 hour'	|interval '1 day 01:00:00'|
|+|	timestamp '2001-09-28 01:00' + interval '23 hours'	|timestamp '2001-09-29 00:00:00'|
|+|	time '01:00' + interval '3 hours'|	time '04:00:00'|
|-|	- interval '23 hours'	|interval '-23:00:00'|
|-|	date '2001-10-01' - date '2001-09-28'	|integer '3' (days)|
|-|	date '2001-10-01' - integer '7'|	date '2001-09-24'|
|-|	date '2001-09-28' - interval '1 hour'|	timestamp '2001-09-27 23:00:00'
|-|	time '05:00' - time '03:00'	|interval '02:00:00'|
|-|	time '05:00' - interval '2 hours'	|time '03:00:00'|
|-|	timestamp '2001-09-28 23:00' - interval '23 hours'|	timestamp '2001-09-28 00:00:00'|
|-|	interval '1 day' - interval '1 hour'|	interval '1 day -01:00:00'|
|-|	timestamp '2001-09-29 03:00' - timestamp '2001-09-27 12:00'|	interval '1 day 15:00:00'|
|*|	900 * interval '1 second'|	interval '00:15:00'|
|*|	21 * interval '1 day'|	interval '21 days'|
|*|	double precision '3.5' * interval '1 hour'|	interval '03:30:00'|
|/|	interval '1 hour' / double precision '1.5'|	interval '00:40:00'|

表-2 日期/时间函数

|函数|	返回类型|	描述|	例子|	结果|
| -- | -- | -- | -- | -- |
|age(timestamp, timestamp)|	interval|	减去参数后的"符号化"结果|	age(timestamp '2001-04-10', timestamp '1957-06-13')|	43 years 9 mons 27 days|
|age(timestamp)	|interval	|从current_date减去参数后的结果（在午夜）|	age(timestamp '1957-06-13')	|43 years 8 mons 3 days|
|clock_timestamp()	|timestamp with time zone	实时时钟的当前时间戳（在语句执行时变化）；|  |  |	 |
|current_date|	date|	当前的日期；	| 	 ||
|current_time|	time with time zone	|当日时间；|	 ||
|current_timestamp	|timestamp with time zone|	当前事务开始时的时间戳；|||
|date_part(text, timestamp)	|double precision	|获取子域(等效于extract)；|	date_part('hour', timestamp '2001-02-16 20:38:40')|	20|
|date_part(text, interval)|	double precision	|获取子域(等效于extract)；|	date_part('month', interval '2 years 3 months')	|3|
|date_trunc(text, timestamp)|	timestamp	|截断成指定的精度；|	date_trunc('hour', timestamp '2001-02-16 20:38:40')	|2001-02-16 20:00:00|
|extract(field from timestamp)	|double precision|	获取子域；	|extract(hour from timestamp '2001-02-16 20:38:40')|	20|
|extract(field from interval)|	double precision|	获取子域；|	extract(month from interval '2 years 3 months')|	3|
|isfinite(date)|	boolean	|测试是否为有穷日期(不是 +/-无穷)|	isfinite(date '2001-02-16')|	true|
|isfinite(timestamp)|	boolean|	测试是否为有穷时间戳(不是 +/-无穷)|	isfinite(timestamp '2001-02-16 21:28:30')|	true|
|isfinite(interval)	|boolean|	测试是否为有穷时间间隔|	isfinite(interval '4 hours')|	true|
|justify_days(interval)	|interval|	按照每月 30 天调整时间间隔|	justify_days(interval '35 days')|	1 mon 5 days|
|justify_hours(interval)|	interval|	按照每天 24 小时调整时间间隔|	justify_hours(interval '27 hours')|	1 day 03:00:00|
|justify_interval(interval)|	interval|	使用justify_days和justify_hours调整时间间隔的同时进行正负号调整|	justify_interval(interval '1 mon -1 hour')|	29 days 23:00:00|
|localtime|	time|	当日时间； 	||| 
|localtimestamp|	timestamp|	当前事务开始时的时间戳；|||	 	 
|now()|	timestamp with time zone|	当前事务开始时的时间戳；	||| 	 
|statement_timestamp()|	timestamp with time zone|	实时时钟的当前时间戳；|||
|timeofday()|	text	|与clock_timestamp相同，但结果是一个text 字符串；|||	 
|transaction_timestamp()|	timestamp with time zone|	当前事务开始时的时间戳；||||

除了这些函数以外，还支持 SQL 的OVERLAPS操作符：
```SQL
(start1, end1) OVERLAPS (start2, end2)
(start1, length1) OVERLAPS (start2, length2)
```
这个表达式在两个时间域(用它们的终点定义)重叠的时候生成真值，在不重叠是生成假值。 终点可以用一对日期、时间、时间戳来声明；或者是一个后面跟着一个时间间隔的日期、 时间、时间戳。当提供一对值，不管先写开始还是结束；OVERLAPS 自动将这对值较早的作为开始。每段时间取值为半开区间开始 <= 时间 < 结束，除非开始 和结束相等，此时表示单一的时刻。 这意味着两个时间段只有一个共同的端点没有重叠。
```SQL
SELECT (DATE '2001-02-16', DATE '2001-12-21') OVERLAPS
       (DATE '2001-10-30', DATE '2002-10-30');
Result: true
SELECT (DATE '2001-02-16', INTERVAL '100 days') OVERLAPS
       (DATE '2001-10-30', DATE '2002-10-30');
Result: false
SELECT (DATE '2001-10-29', DATE '2001-10-30') OVERLAPS
       (DATE '2001-10-30', DATE '2001-10-31');
Result: false
SELECT (DATE '2001-10-30', DATE '2001-10-30') OVERLAPS
       (DATE '2001-10-30', DATE '2001-10-31');
Result: true
```
当把interval值添加到timestamp with time zone上(或从中减去)的时候， days 部分会按照指定的天数增加(或减少)timestamp with time zone的日期。 对于横跨夏令时的变化(会话的时区设置被识别为夏时制)，interval '1 day' 并不一定等于interval '24 hours'。例如，当会话的时区设置为CST7CDT 的时候timestamp with time zone '2005-04-02 12:00-07' + interval '1 day' 的结果是timestamp with time zone '2005-04-03 12:00-06'， 而将interval '24 hours'增加到相同的timestamp with time zone 之上的结果则是timestamp with time zone '2005-04-03 13:00-06'， 因为CST7CDT时区在2005-04-03 02:00的时候有一个夏令时变更。

注意age返回的月数可能有歧义， 因为不同的月份有不同的天数。EdsSQL的方法是当计算部分月数时， 采用两个日期较早的月。例如：age('2004-06-01', '2004-04-30') 使用4月份产生1 mon 1 day，当用5月分时产生1 mon 2 days， 因为5月有31天，而4月只有30天。

<br/>**EXTRACT, date_part**
```SQL
EXTRACT(field FROM source)
```
extract函数从日期/时间数值里抽取子域，比如年、小时等。 source必须是一个timestamp, time, interval类型的值表达式(类型为date的表达式转换为 timestamp，因此也可以用)。field 是一个标识符或者字符串，它指定从源数据中抽取的域。extract 函数返回类型为double precision的数值。下列数值是有效数据域的名字：

<ul><li>century--世纪
```SQL
SELECT EXTRACT(CENTURY FROM TIMESTAMP '2000-12-16 12:21:13');
Result: 20
SELECT EXTRACT(CENTURY FROM TIMESTAMP '2001-02-16 20:38:40');
Result: 21
```
第一个世纪从 0001-01-01 00:00:00 AD 开始，尽管那时候人们还不知道这是第一个世纪。 这个定义适用于所有使用阳历的国家。没有 0 世纪，我们直接从公元前 1 世纪到公元 1 世纪。 如果你认为这个不合理，那么请把抱怨发给：梵蒂冈，罗马圣彼得教堂，教皇收。

<!--PostgreSQL 8.0 以前版本里并不遵循世纪的习惯编号， 只是把年份除以 100 。-->

<li>day--对于timestamp值，(月份)里的日期(1-31)；对于interval，天数
```SQL
SELECT EXTRACT(DAY FROM TIMESTAMP '2001-02-16 20:38:40');
Result: 16

SELECT EXTRACT(DAY FROM INTERVAL '40 days 1 minute');
Result: 40
```
<li>decade--年份除以 10
```SQL
SELECT EXTRACT(DECADE FROM TIMESTAMP '2001-02-16 20:38:40');
Result: 200
```
<li>dow--每周的星期号，星期天(0)到星期六(6)
```SQL
SELECT EXTRACT(DOW FROM TIMESTAMP '2001-02-16 20:38:40');
Result: 5
```
请注意，extract的星期几编号和to_char(..., 'D')函数不同。

<li>doy--一年的第几天(1-365/366)
```SQL
SELECT EXTRACT(DOY FROM TIMESTAMP '2001-02-16 20:38:40');
Result: 47
epoch
```
对于timestamp with time zone值而言， 是自 1970-01-01 00:00:00-00 UTC以来的秒数(结果可能是负数)； 对于date和timestamp值而言， 是自 1970-01-01 00:00:00 当地时间以来的秒数； 对于interval值而言，它是时间间隔的总秒数。
```SQL
SELECT EXTRACT(EPOCH FROM TIMESTAMP WITH TIME ZONE '2001-02-16 20:38:40.12-08');
Result: 982384720.12

SELECT EXTRACT(EPOCH FROM INTERVAL '5 days 3 hours');
Result: 442800
```
下面是把 epoch 值转换回时间戳的方法：
```SQL
SELECT TIMESTAMP WITH TIME ZONE 'epoch' + 982384720.12 * INTERVAL '1 second';
（to_timestamp函数封装上面的转换。）
```
<li>hour--小时域(0-23)
```SQL
SELECT EXTRACT(HOUR FROM TIMESTAMP '2001-02-16 20:38:40');
Result: 20
```
<li>isodow--周中的第几天 [1-7] 星期一：(1)星期天：(7)。
```SQL
SELECT EXTRACT(ISODOW FROM TIMESTAMP '2001-02-18 20:38:40');
Result: 7
```
除了星期天外，都与dow相同。这与ISO 8601标准周中的第几天编码相匹配。

<li>isoyear--日期中的ISO 8601标准年（不适用于间隔）。
```SQL
SELECT EXTRACT(ISOYEAR FROM DATE '2006-01-01');
Result: 2005
SELECT EXTRACT(ISOYEAR FROM DATE '2006-01-02');
Result: 2006
```
每个带有星期一开始的周中包含1月4日的ISO年， 所以在年初的1月或12月下旬的ISO年可能会不同于阳历的年。 见week获取更多信息。

<!--这个域不能用于 PostgreSQL 8.3之前的版本。-->

<li>microseconds--秒域(包括小数部分)乘以 1,000,000 。请注意它包括全部的秒。
```SQL
SELECT EXTRACT(MICROSECONDS FROM TIME '17:12:28.5');
Result: 28500000
```
<li>millennium--千年
```SQL
SELECT EXTRACT(MILLENNIUM FROM TIMESTAMP '2001-02-16 20:38:40');
Result: 3
```
20 世纪(19xx 年)里面的年份在第二个千年里。第三个千年从 2001 年 1 月 1 日零时开始。

<!--PostgreSQL 8.0 之前的版本并不遵循千年编号的习惯， 只是返回年份除以 1000 。-->

<li>milliseconds--秒域(包括小数部分)乘以 1000 。请注意它包括完整的秒。
```SQL
SELECT EXTRACT(MILLISECONDS FROM TIME '17:12:28.5');
Result: 28500
```
<li>minute--分钟域(0-59)
```SQL
SELECT EXTRACT(MINUTE FROM TIMESTAMP '2001-02-16 20:38:40');
Result: 38
```
<li>month
对于timestamp值，它是一年里的月份数(1-12)；对于interval值， 它是月的数目，然后对 12 取模(0-11)
```SQL
SELECT EXTRACT(MONTH FROM TIMESTAMP '2001-02-16 20:38:40');
Result: 2

SELECT EXTRACT(MONTH FROM INTERVAL '2 years 3 months');
Result: 3

SELECT EXTRACT(MONTH FROM INTERVAL '2 years 13 months');
Result: 1
```
<li>quarter--该天所在的该年的季度(1-4)
```SQL
SELECT EXTRACT(QUARTER FROM TIMESTAMP '2001-02-16 20:38:40');
Result: 1
```
<li>second--秒域，包括小数部分(0-59)[1])
```SQL
SELECT EXTRACT(SECOND FROM TIMESTAMP '2001-02-16 20:38:40');
Result: 40

SELECT EXTRACT(SECOND FROM TIME '17:12:28.5');
Result: 28.5
```
<li>timezone--与 UTC 的时区偏移量，以秒记。正数对应 UTC 东边的时区，负数对应 UTC 西边的时区。 <!--（技术角度讲，PostgreSQL使用UT1，因为不处理闰秒。）-->

<li>timezone_hour--时区偏移量的小时部分。

<li>timezone_minute--时区偏移量的分钟部分。

<li>week--该天在所在的年份里是第几周。ISO 8601 定义一年的第一周包含该年的一月四日(ISO-8601 的周从星期一开始)。换句话说， 一年的第一个星期四在第一周。

在ISO定义里，一月的头几天可能是前一年的第 52 或者第 53 周， 十二月的后几天可能是下一年第一周。比如，2005-01-01是 2004 年的第 53 周， 而2006-01-01是 2005 年的第 52 周，2012-12-31是2013年的第一周。 建议isoyear字段和week一起使用以得到一致的结果。
```SQL
SELECT EXTRACT(WEEK FROM TIMESTAMP '2001-02-16 20:38:40');
Result: 7
```
<li>year--年份域。要记住这里没有0 AD，所以从AD年里抽取BC年应该小心些。
```SQL
SELECT EXTRACT(YEAR FROM TIMESTAMP '2001-02-16 20:38:40');
Result: 2001
```
<li>extract函数主要的用途是运算。 <!--对于用于显示的日期/时间数值格式化，参阅第 9.8 节。-->

<li>date_part函数是在传统的Ingres 函数的基础上制作的(该函数等效于SQL标准函数extract)：
```SQL
date_part('field', source)
```
请注意这里的 field参数必须是一个字符串值，而不是一个名字。 有效的date_part数域名和extract是一样的。
```SQL
SELECT date_part('day', TIMESTAMP '2001-02-16 20:38:40');
Result: 16

SELECT date_part('hour', INTERVAL '4 hours 3 minutes');
Result: 4
```
<br/>**date_trunc**

date_trunc函数在概念上和用于数字的trunc函数类似。
```SQL
date_trunc('field', source)
```
source是timestamp或interval 类型的值表达式(date和time类型的值都分别自动转换成 timestamp或interval)。用field 选择对该时间戳值用什么样的精度进行截断。返回的数值是timestamp 或interval类型，所有小于选定的精度的域都设置为零(日期和月份域则为 1)。

field的有效数值是：<br/>

microseconds<br/>
milliseconds<br/>
second<br/>
minute<br/>
hour<br/>
day<br/>
week<br/>
month<br/>
quarter<br/>
year<br/>
decade<br/>
century<br/>
millennium<br/>

例子：
```SQL
SELECT date_trunc('hour', TIMESTAMP '2001-02-16 20:38:40');
Result: 2001-02-16 20:00:00

SELECT date_trunc('year', TIMESTAMP '2001-02-16 20:38:40');
Result: 2001-01-01 00:00:00
```

<br/>**AT TIME ZONE**

AT TIME ZONE构造允许把时间戳转换成不同的时区。 表-3显示了其变体。

表-3 AT TIME ZONE 变体

|表达式	|返回类型|	描述|
| -- | -- | -- | -- |
|timestamp without time zone AT TIME ZONE zone|	timestamp with time zone|	把给出的不带时区的时间戳转换成给定时区的时间戳|
|timestamp with time zone AT TIME ZONE zone	|timestamp without time zone|	把给出的带时区的时间戳转换成未指定时区的时间戳|
|time with time zone AT TIME ZONE zone|	time with time zone|	把给出的带时区的时间转换成给定时区的时间|

在这些表达式里，zone可以声明为文本串(比如'PST') 或者一个时间间隔(比如INTERVAL '-08:00')。

例子(假设本地时区是PST8PDT)：
```SQL
SELECT TIMESTAMP '2001-02-16 20:38:40' AT TIME ZONE 'MST';
Result: 2001-02-16 19:38:40-08

SELECT TIMESTAMP WITH TIME ZONE '2001-02-16 20:38:40-05' AT TIME ZONE 'MST';
Result: 2001-02-16 18:38:40
```
第一个例子接受一个无时区的时间戳然后把它解释成 MST(UTC-7) 时间生成 UTC 时间戳， 然后把这个时间转换为 PST(UTC-8) 显示。第二个例子接受一个声明为 EST(UTC-5) 的时间戳， 然后把它转换成 MST(UTC-7) 的当地时间。

timezone(*zone*, *timestamp*) 函数等效于 SQL 兼容的构造*timestamp* AT TIME ZONE *zone*。

<br/>**当前日期/时间**

EdsSQL提供许多返回当前日期和时间的函数。 这些符合 SQL 标准的函数全部都按照当前事务的开始时刻返回结果：
```SQL
CURRENT_DATE
CURRENT_TIME
CURRENT_TIMESTAMP
CURRENT_TIME(precision)
CURRENT_TIMESTAMP(precision)
LOCALTIME
LOCALTIMESTAMP
LOCALTIME(precision)
LOCALTIMESTAMP(precision)
```
CURRENT_TIME和CURRENT_TIMESTAMP 返回带有时区的值；LOCALTIME和LOCALTIMESTAMP 返回不带时区的值。

CURRENT_TIME,CURRENT_TIMESTAMP, LOCALTIME,LOCALTIMESTAMP 可以有选择地获取一个精度参数，该精度导致结果的秒数域园整到指定小数位。 如果没有精度参数，将给予所能得到的全部精度。

一些例子：
```SQL
SELECT CURRENT_TIME;
Result: 14:39:53.662522-05

SELECT CURRENT_DATE;
Result: 2001-12-23

SELECT CURRENT_TIMESTAMP;
Result: 2001-12-23 14:39:53.662522-05

SELECT CURRENT_TIMESTAMP(2);
Result: 2001-12-23 14:39:53.66-05

SELECT LOCALTIMESTAMP;
Result: 2001-12-23 14:39:53.662522
```
因为这些函数全部都按照当前事务的开始时刻返回结果， 所以它们的值在事务运行的整个期间内都不改变。我们认为这是一个特性： 目的是为了允许一个事务在"当前时间"上有连贯的概念， 这样在同一个事务里的多个修改可以保持同样的时间戳。

<p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >注意: 许多其它数据库系统更频繁地更新这些数值。</p>

EdsSQL同样也提供了返回实时时间值的函数， 它们的返回值会在事务中随时间的前进而变化。这些不附合 SQL 标准的函数列表如下：
```SQL
transaction_timestamp()
statement_timestamp()
clock_timestamp()
timeofday()
now()
```
transaction_timestamp()等效于CURRENT_TIMESTAMP， 不过其命名准确的表明了其含义。statement_timestamp() 返回当前事务开始时刻的时间戳(更准确的说是收到客户端最后一条命令的时间)。 statement_timestamp()和transaction_timestamp() 在一个事务的第一条命令里返回值相同，但是在随后的命令中却不一定相同。 cclock_timestamp()返回实时时钟的当前时间戳， 因此它的值甚至在同一条 SQL 命令中都会变化。timeofday()是一个历史的 EdsSQL函数，类似于clock_timestamp()， 它也返回实时时钟的当前时间戳，不过它返回一个格式化了的text字符串， 而不是timestamp with time zone值。now() 是传统的EdsSQL和transaction_timestamp()等效的函数。

所有日期/时间类型还接受特殊的文本值now， 用于声明当前的日期和时间(重申：当前事务的开始时刻)。 因此，下面三个都返回相同的结果：
```SQL
SELECT CURRENT_TIMESTAMP;
SELECT now();
SELECT TIMESTAMP 'now';  -- incorrect for use with DEFAULT
```

<p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >注意: 在创建表的时候你不应该用第三种形式声明一个DEFAULT值。 系统将在分析这个常量的时候把now转换为一个timestamp， 因此这个缺省值就会变成创建表的时间！而前两种形式要到实际使用缺省值的时候才计算， 因为它们是函数调用。因此它们可以给出每次插入行的时刻。</p>

<br/>**延时执行**

下面的这个函数可以用于让服务器进程延时执行：
```SQL
pg_sleep(seconds)
```
pg_sleep让当前的会话进程休眠seconds 秒以后再执行。seconds是一个double precision 类型的值，所以可以指定带小数的秒数。例如：
```SQL
SELECT pg_sleep(1.5);
```

<p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >注意: 在创建表的时候你不应该用第三种形式声明一个DEFAULT值。 系统将在分析这个常量的时候把now转换为一个timestamp， 因此这个缺省值就会变成创建表的时间！而前两种形式要到实际使用缺省值的时候才计算， 因为它们是函数调用。因此它们可以给出每次插入行的时刻。</p>

<p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >注意: 请确保调用eds_sleep的会话没有持有不必要的锁。 否则其它会话可能必须等待这个休眠的会话释放所持有的锁，从而减慢系统速度。</p>


<br/>**备注**

如果操作系统实现了润秒，那么上限是 60 。