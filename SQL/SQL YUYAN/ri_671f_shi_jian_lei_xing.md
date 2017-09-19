#日期/时间类型

表1 显示了EdsSQL 支持的SQL中所有日期和时间类型。 日期是按照公历计算的，甚至日历之前的年份也介绍了。

表1  日期/时间类型

| 名字 | 存储空间 | 描述 | 最低值 | 最高值 | 分辨率 |
| -- | -- | -- | -- | -- | -- |
| timestamp [ (p) ] [ without time zone ] | 8 字节 | 日期和时间(无时区) | 4713 BC | 294276 AD | 1 毫秒 / 14 位 |
| timestamp [ (p) ] without time zone | 8 字节 | 日期和时间，有时区 | 4713 BC | 294276 AD | 1 毫秒 / 14 位 |
| date | 4 字节 | 只用于日期 | 4713 BC | 5874897 AD | 1 天 |
| time [ (p) ] [ without time zone ] | 8 字节 | 只用于一日内时间 | 00:00:00 | 24:00:00 | 1 毫秒 / 14 位 |
| time [ (p) ] with time zone | 12 字 | 只用于一日内时间，带时区 | 00:00:00+1459 | 24:00:00-1459 | 1 毫秒 / 14 位 |
| interval [ fields ] [ (p) ] | 12 字节 | 时间间隔 | -178000000 年 | 178000000 年 | 1 毫秒 / 14 位 |

<p style="color: #c09853;background: #fcf8e3 none repeat scroll 0% 0%; font-size:13px;" >注意: SQL标准要求仅仅将timestamp类型等于timestamp without time zone 类型，EdsSQL遵守这个行为。 timestamptz作为 timestamp with time zone的缩写被接受；这是EdsSQL 的一个扩展。</p>

time, timestamp和interval接受一个可选的精度值 p以指明秒域中小数部分的位数。没有明确的缺省精度， p的范围对timestamp和interval类型是从0到6。 

<p style="color: #c09853;background: #fcf8e3 none repeat scroll 0% 0%; font-size:13px;" >注意: 如果timestamp数值是以8字节整数(目前的缺省)的方式存储的， 那么微秒的精度就可以在数值的全部范围内都可以获得。如果timestamp 数值是以双精度浮点数(一个废弃的编译时的选项)的方式存储的， 那么有效精度会小于 6 。timestamp值是以 2000-01-01 午夜之前或之后的秒数存储的。 当timestamp值用浮点数实现时，微秒的精度是为那些在 2000-01-01 前后几年的日期实现的， 对于那些远一些的日子，精度会下降。注意，使用浮点时间允许一个比上面显示的更大的 timestamp值变化范围：从4713BC到5874897AD。<br/>
同一个编译时选项也决定time和interval值是保存成浮点数还是八字节整数。 在以浮点数存储的时候，随着时间间隔的增加，大的 interval数值的精度会降低。</p>

对于time类型，如果使用了八字节的整数存储，那么*p* 允许的范围是从 0 到 6 ，如果使用的是浮点数存储，那么这个范围是 0 到 10 。

interval类型有一个额外的选项，通过下下面词组之一限制存储的字段值：
```SQL
YEAR
MONTH
DAY
HOUR
MINUTE
SECOND
YEAR TO MONTH
DAY TO HOUR
DAY TO MINUTE
DAY TO SECOND
HOUR TO MINUTE
HOUR TO SECOND
MINUTE TO SECOND
```
注意如果同时指定了*fields*和*p*， *fields*必须包含SECOND，因为精度只应用于秒。

time with time zone类型是 SQL 标准定义的， 但是完整定义的有些方面会导致有问题的用法。在大多数情况下，date, time, timestamp without time zone, 和timestamp with time zone 的组合就应该能提供一切应用需要的日期/时间的完整功能。

abstime和reltime类型是低分辨率类型，它们被用于系统内部。 我们反对你在应用中使用这些类型，因为这些内部类型可能会在未来的版本里消失。

<br/>**日期/时间输入**

日期和时间的输入几乎可以是任何合理的格式，包括 ISO-8601 格式、SQL-兼容格式、 传统EDS格式、其它的形式。对于一些格式， 日期输入里的日、月、年可能会让人迷惑，因此系统支持自定义这些字段的顺序。 把DateStyle参数设置为MDY就按照"月-日-年"解析， 设置为DMY就按照"日-月-年"解析，设置为YMD就按照"年-月-日"解析。

EdsSQL在处理日期/时间输入上比SQL 标准要求的更灵活。

请记住任何日期或者时间的文本输入需要由单引号包围，就像一个文本字符串一样。  SQL要求使用下面的语法：
```SQL
type [ (p) ] 'value'
```
可选的精度声明中的p是一个整数，表示在秒域中小数部分的位数， 我们可以对time,timestamp,interval类型声明精度。 允许的精度在上面已经说明。如果在常量声明中没有声明精度，缺省是文本值的精度。

**日期**

表2 显示了date类型可能的输入方式。

表2 Date Input

| 例子 | 描述 |
| -- | -- |
| 1999-01-08 | ISO 8601格式(建议格式)，任何方式下都是 1999 年 1 月 8 号 |
| January 8, 1999 | 在任何datestyle输入模式下都无歧义 |
| 1/8/1999 | 有歧义，在MDY下是一月八号；在DMY模式下是八月一日 |
| 1/18/1999 | MDY模式下是一月十八日，其它模式下被拒绝 |
| 01/02/03 | MDY模式下的 2003 年 1 月 2 日； DMY模式下的 2003 年 2 月 1 日； YMD模式下的 2001 年 2 月 3 日 |
| 1999-Jan-08 | 任何模式下都是 1 月 8 日 |
| Jan-08-1999 | 任何模式下都是 1 月 8 日 |
| 08-Jan-1999 | 任何模式下都是 1 月 8 日 |
| 99-Jan-08 | YMD模式下是 1 月 8 日，否则错误 |
| 08-Jan-99 | 一月八日，除了在YMD模式下是错误的之外 |
| Jan-08-99 | 一月八日，除了在YMD模式下是错误的之外 |
| 19990108 | ISO 8601；任何模式下都是 1999 年 1 月 8 日 |
| 990108 | ISO 8601；任何模式下都是 1999 年 1 月 8 日 |
| 1999.008 | 年和年里的第几天 |
| J2451187 | 儒略日 |
| January 8, 99 BC | 公元前 99 年 |

<br/>**时间**

当日时间类型是time [ (*p*) ] without time zone 和time [ (*p*) ] with time zone。 只写time等效于time without time zone。

这些类型的有效输入由当日时间后面跟着可选的时区组成(参阅 表3 和表4 )。 如果在time without time zone类型的输入中声明了时区，那么它会被悄悄地忽略。 同样指定的日期也会被忽略，除非使用了一个包括夏令时规则的时区名，比如 America/New_York，在这种情况下， 必须指定日期以确定这个时间是标准时间还是夏令时。时区偏移将记录在 time with time zone中。

表3 时间输入

| 例子 | 描述 |
| -- | -- |
| 04:05:06.789 | ISO 8601 |
| 04:05:06 | ISO 8601 |
| 04:05 | ISO 8601 |
| 040506 | ISO 8601 |
| 04:05 AM | 与 04:05 一样；AM 不影响数值 |
| 04:05 PM | 与 16:05 一样；输入小时数必须<= 12 |
| 04:05:06.789-8 | ISO 8601 |
| 04:05:06-08:00 | ISO 8601 |
| 04:05-08:00 | ISO 8601 |
| 040506-08 | ISO 8601 |
| 04:05:06 PST | 缩写的时区 |
| 2003-04-12 04:05:06 America/New_York| 用名字声明的时区 |

表4 时区输入

| 例子 | 描述 |
| -- | -- |
| PST | 太平洋标准时间(Pacific Standard Time) |
| America/New_York | 完整时区名称 |
| PST8PDT	POSIX | 风格的时区 |
| -8:00 | ISO-8601 与 PST 的偏移 |
| -800 | ISO-8601 与 PST 的偏移 |
| -8 | ISO-8601 与 PST 的偏移 |
| zulu | 军方对 UTC 的缩写(译注：可能是美军) |
| z | zulu的缩写 |

**时间戳**

时间戳类型的有效输入由一个日期和时间的连接组成，后面跟着一个可选的时区， 一个可选的AD或BC。另外， AD/BC可以出现在时区前面， 但这个顺序并非最佳的。因此：
```SQL
1999-01-08 04:05:06
```
和：
```SQL
1999-01-08 04:05:06 -8:00
```
都是有效的数值，它是兼容ISO-8601 的。另外， 也支持下面这种使用广泛的格式：
```SQL
January 8 04:05:06 1999 PST
```
SQL标准通过"+"或者"-"是否存在和时间后的失去偏移来区分 timestamp without time zone和timestamp with time zone文本。因此， 根据标准，
```SQL
TIMESTAMP '2004-10-19 10:23:54'
```
是一个 timestamp without time zone，而
```SQL
TIMESTAMP '2004-10-19 10:23:54+02'
```
是一个timestamp with time zone。EdsSQL 从来不会在确定文本的类型之前检查文本内容，因此会把上面两个都看做是 timestamp without time zone。因此要保证把上面的第二个当作 timestamp with time zone看待，就要给它明确的类型：
```
TIMESTAMP WITH TIME ZONE '2004-10-19 10:23:54+02'
```
如果一个文本已被确定是timestamp without time zone，EdsSQL 将悄悄忽略任何文本中指出的时区。因此，生成的日期/时间值是从输入值的日期/时间字段衍生出来的， 并且没有就时区进行调整。

对于timestamp with time zone，内部存储的数值总是 UTC(全球统一时间， 以前也叫格林威治时间GMT)。如果一个输入值有明确的时区声明， 那么它将用该时区合适的偏移量转换成 UTC 。如果在输入字符串里没有时区声明， 那么它就假设是在系统的TimeZone参数里的那个时区， 然后使用这个timezone时区转换成 UTC 。

如果输出一个timestamp with time zone，那么它总是从 UTC 转换成当前的 timezone时区，并且显示为该时区的本地时间。要看其它时区的该时间， 要么修改timezone，要么使用AT TIME ZONE构造。

在timestamp without time zone和timestamp with time zone 之间的转换通常假设timestamp without time zone数值应该以 timezone本地时间的形式接受或者写出。其它的时区可以用 AT TIME ZONE的方式为转换声明。

**特殊值**

EdsSQL为方便起见支持在 表5 里面显示的几个特殊输入值。 值infinity和-infinity是特别在系统内部表示的， 并且将按照同样的方式显示；但是其它的都只是符号缩写， 在读取的时候将被转换成普通的日期/时间值。特别是now 和相关的字符串在读取的时候就被转换成对应的数值。 所有这些值在 SQL 命令里当作普通常量对待时，都需要包围在单引号里面。

表5 特殊日期/时间输入

| 输入字符串 | 适用类型 | 描述 |
| -- | -- | -- |
| epoch | date, timestamp | 1970-01-01 00:00:00+00 (Unix 系统零时) |
| infinity | date, timestamp | 比任何其它时间戳都晚 |
| -infinity | date, timestamp | 比任何其它时间戳都早 |
| now | date, time, timestamp | 当前事务的开始时间 |
| today | date, timestamp | 今日午夜 |
| tomorrow | date, timestamp | 明日午夜 |
| yesterday | date, timestamp | 昨日午夜 |
| allballs | time | 00:00:00.00 UTC |


下列SQL兼容函数也可以用于获取对应数据类型的当前时间值： CURRENT_DATE, CURRENT_TIME, CURRENT_TIMESTAMP, LOCALTIME, LOCALTIMESTAMP。后四个接受一个可选的精度声明。不过，请注意这些 SQL 函数不是 被当作数据输入字符串识别的。

<br/>**日期/时间输出**

日期/时间类型的输出格式可以设成 ISO 8601(默认)、SQL(Ingres)、 传统的EDS(Unix date格式)或German四种风格之一。 SQL标准要求使用 ISO 8601 格式。"SQL"输出格式的名字是历史偶然。 表6 显示了每种输出风格的例子。 date和time类型的输出当然只是给出的例子里面的日期和时间部分。

表6 日期/时间输出风格

| 风格 | 描述 | 例子 |
| -- | -- | -- |
| ISO | ISO 8601，SQL 标准 | 1997-12-17 07:37:16-08 |
| SQL | 传统风格 | 12/17/1997 07:37:16.00 PST |
| EDS | 原始风格 | Wed Dec 17 07:37:16 1997 PST |
| German | 地区风格 | 17.12.1997 07:37:16.00 PST |

注意: ISO 8601指定用大写字母T分隔日期和时间。EdsSQL 在输入上接受这种格式，但是在输出上使用一个空格而不是T，就像上面显示的。 这样更易读而且和RFC 3399或者其他的数据库系统一致。
如果声明了 DMY 顺序，那么在SQL和 POSTGRES 风格里， 日期在月份之前出现，否则月份出现在日期之前。表7 显示了一个例子。

表7 日期顺序习惯

|datestyle设置 | 输入顺序 | 输出样例 |
| -- | -- | -- |
| SQL, DMY | 日/月/年 | 17/12/1997 15:37:16.00 CET |
| SQL, MDY | 月/日/年 | 12/17/1997 07:37:16.00 PST |
| Postgres, DMY | 日/月/年 | Wed 17 Dec 07:37:16 1997 PST |


用户可以用SET datestyle命令选取日期/时间的风格， 也可以在配置文件p中设置，或者在服务器或客户端的 PGDATESTYLE环境变量中设置。

我们也可以用格式化函数to_char 来更灵活地控制时间/日期地输出。

<br/>**时区**

时区和时区习惯不仅仅受地球几何形状的影响，还受到政治决定的影响。 到了 19 世纪，全球的时区变得稍微标准化了些，但是还是易于遭受随意的修改， 部分是因为夏时制规则。EdsSQL 使用广泛使用的zoneinfo（Olson）时区信息数据库有关历史时区的规则。 对于未来的时间，假设对于给定时区最近的规则将在未来继续无期限的遵守。

EdsSQL在典型应用中尽可能与SQL 的定义相兼容。但SQL标准在日期/时间类型和功能上有一些奇怪的混淆。 两个显而易见的问题是：

<ul><li>date类型与时区没有联系，而time类型却有或可以有。 然而，现实世界的时区只有在与时间和日期都关联时才有意义， 因为时间偏移量(时差)可能因为实行类似夏时制这样的制度而在一年里有所变化。</li>

<li>缺省的时区用一个数字常量表示与UTC的偏移(时差)。因此， 当跨DST(夏时制)界限做日期/时间算术时， 我们根本不可能把夏时制这样的因素计算进去。</li></ul>

为了克服这些困难，我们建议在使用时区的时候，使用那些同时包含日期和时间的日期/时间类型。 我们建议不要使用time with time zone类型(尽管 EdsSQL出于合理应用以及为了与SQL 标准兼容的考虑支持这个类型)。EdsSQL 假设你用于任何类型的本地时区都只包含日期或时间(而不包含时区)。

在系统内部，所有日期和时间都用全球统一时间UTC格式存储， 时间在发给客户前端前由数据库服务器根据TimeZone 配置参数声明的时区转换成本地时间。

EdsSQL允许你用三种方法指定时区：

<ul><li>完整的时区名。</li><!--例如America/New_York。所有可以识别的时区名在 pg_timezone_names视图中列出(参见 第 47.71 节)。EdsSQL 使用广泛使用的zoneinfo时区数据， 所以这些时区名在其它软件里也能被轻松的识别。-->

<li>时区缩写。例如PST。这种缩写名通常只是定义了相对于 UTC 的偏移量， 而前一种完整的时区名可能还隐含着一组夏时制转换规则。 所有可以识别的时区缩写在pg_timezone_abbrevs视图中列出(参见 第 47.70 节)。你不能设置 TimeZone或log_timezone 配置参数为时区缩写，但是你可以在日期/时间输入值中结合AT TIME ZONE 操作符使用时区缩写。</li>

<li>除完整的时区名及其缩写之外，EdsSQL还接受 POSIX 风格的 STDoffset或STDoffsetDST 格式的时区，其中的STD是时区缩写、offset 是一个相对于 UTC 的小时偏移量、DST是一个可选的夏时制时区缩写 (假定相对于给定的偏移量提前一小时)。例如，如果EST5EDT不是一个已识别的时区名， 那么它将等同于美国东部时间。如果存在夏时制时区名是当前时区名，根据zoneinfo 时区数据库的 posixrules条目中相同的夏时制事务规则，可以考虑使用这个特性。 在一个EdsSQL标准安装中，posixrules与US/Eastern 相同，因此POSIX格式的时区声明遵循USA夏时制规则。如果需要，可以通过替换posixrules 文件来调整该习惯。</li></ul>

简言之，这就是完整的时区名与时区缩写之间的差异：时区缩写总是代表一个相对于 UTC 的固定偏移量， 然而大多数完整的时区名隐含着一个本地夏令时规则，因此就有可能有两个相对于 UTC 的不同偏移量。

需要警惕的是，由于没有合理的时区缩写检查，POSIX格式的时区特点能导致静默的伪输入。 例如，使用SET TIMEZONE TO FOOBAR0时，实际上系统使用的是一个很特别的UTC缩写。 另一个需要注意的是，在POSIX时区名中，积极的偏移用于west格林尼治位置。 在其他地方，EdsSQL遵循ISO-8601规定， 即积极的时区偏移east格林威治。

<!--总体而言，PostgreSQL 8.2 版本以后时区名在所有情况下都是大小写无关的。 而之前的版本在某些情况下是大小写敏感的。-->

无论是完整的时区名还是时区缩写都不是硬连接进服务器的，它们都是从安装目录下的 .../share/timezone/和.../share/timezonesets/ 配置文件中获取的。

可以在配置文件里设置TimeZone 配置参数，或者用任何其它标准方法。 除此之外，还有好几种特殊方法可以设置它：

<ul><li>使用SQL命令SET TIME ZONE为会话设置时区， 这是SET TIMEZONE TO的一个可选的拼写方式，更加兼容标准。</li>

<li>如果在客户端设置了PGTZ环境变量，那么libpq 在连接时将使用这个环境变量给后端发送一个SET TIME ZONE命令。</li></ul>

<br/>**间隔输入**

interval类型值可以用下面的详细语法写：
```SQL
[@] quantity unit [quantity unit...] [direction]
```
这里*quantity*是一个数字（可能已标记）；unit 可以是microsecond，millisecond，second， minute，hour，day， week，month，year， decade，century，millennium 或这些单位的缩写或复数。direction可以是ago或为空。 @标记是可选的。不同的单位的数量被隐式地添加适当的计算符号。 ago否定所有。<!--如果IntervalStyle 设置为postgres_verbose，那么这个语法同样用于间隔输出。-->

可以在没有明确单位标记的情况下声明天，小时，分钟和秒。例如，'1 12:59:10' 等同于'1 day 12 hours 59 min 10 sec'。同样， 可以用一个破折号来声明一个年和月的组合，例如'200-10'等同于 '200 years 10 months'。（事实上，SQL标准值允许短的格式， 并且当IntervalStyle设置为sql_standard时，用于输出）。

要么使用"format with designators"，要么使用"alternative format"，间隔值可以写为ISO 8601的时间间隔。格式如下：
```SQL
P quantity unit [ quantity unit ...] [ T [ quantity unit ...]]
```
字符串必须以P开始，并且可以含有一个T用以指明一天中时间的格式。 可用单位的缩写在表 8-16有说明。可以忽略单位， 也可以以任意顺序声明，但单位小于一天时必须在T之后。 尤其M的含义依赖于它在T之前或之后。

表8 ISO 8601 间隔单位的缩写

| 缩写 | 含义 |
| -- | -- |
| Y | 年 |
| M | 月（日期部分） |
| W | 周 |
| D | 日 |
| H | 小时 |
| M | 分钟（时间部分） |
| S | 秒 |


以缩写格式：
```SQL
P [ years-months-days ] [ T hours:minutes:seconds ]
```
一个字符串必须以P开始，然后以T隔开日期和时间。 给出的值是如同ISO 8601日期的数字。

当用*fields*规范写一个时间间隔常数，或将一个字符串标记为用 *fields*规范定义的一个间隔列时，未标记单位的解释由*fields* 解释。如INTERVAL '1' YEAR读作1年，然而INTERVAL '1'代表1秒。 同样，*fields*规范中"最低"有效字段值规定会被静默的忽略。 如，INTERVAL '1 day 2:03:04' HOUR TO MINUTE会导致删除秒字段，而不是天字段。

根据SQL标准，间隔值的所有字段必须有相同的符号，因此前导负号可以用于所有字段； 如'-1 2:03:04'中负号同时应用于天和小时/分钟/秒。EdsSQL 允许字段有不同的标记，并且传统上，文本表述中的每个字段会被认为是独立标记的， 因此在这个例子中的小时/分钟/秒被认为是正值。如果IntervalStyle被设置为 sql_standard，那么前导标记被认为是应用于所有字段的 （当然前提是没有再出现其他标记），否则会使用传统的EdsSQL解释。 为了避免这种歧义，如果任何字段是负的，建议为每个字段附上一个明确的标记。

EdsSQL内部，interval值被存储为月，日，秒的格式， 这是因为月中包含天数不同，并且如果进行了夏令时调整，那么一天可以有23或25小时。 当秒字段可以存储分数时，月和天字段可以是整数型。 由于时间间隔通常是由常量字符串或timestamp减法来定义的， 这种存储方法在大多数情况下很有效。justify_days和justify_hours 函数可用于调整溢出正常范围值的天和小时。

在详细的输入格式，以及更紧凑的输入格式中，字段值可以有小数部分， 例如'1.5 week'或'01:02:03.45'。这种输入被转换成恰当的月， 天和秒来存储。由于这样会产生小数的月或天，因此在低阶字段中引入了分数， 使用1 month = 30 days 和 1 day = 24 hours的转换。例如，'1.5 month' 即1个月15天。输出中，只有秒可以写成分数形式。

表9 中有一些有效的interval输入的例子。

表9 间隔输入

| 示例 | 说明 |
| -- | -- |
| 1-2 | SQL标准格式：一年两个月 |
| 3 4:05:06 | SQL标准格式：3天4小时5分6秒 |
| 1 year 2 months 3 days 4 hours 5 minutes 6 seconds | 传统EDS格式: 1年2个月3天4小时5分钟6秒 |
| P1Y2M3DT4H5M6S | ISO 8601 "带标识符格式":与上面相同含义 |
| P0001-02-03T04:05:06 | ISO 8601 "缩写格式":与上面相同含义 |

**间隔输出**

间隔类型的输出格式可以用命令SET intervalstyle设置为下面四种类型： sql_standard，postgres，postgres_verbose或 iso_8601。默认是postgres格式， 表 10 中有每种格式的示例。

sql_standard格式产生的输出结果符合SQL的间隔字符串标准， 如果间隔值满足标准的限制（无论只有年-月，或只有天-时间，没有积极和消极的构成的混合）。 否则输出类似一个标准年-月文本字符串后跟有一个天-时间文本字符串， 带有添加明确标记的消除歧义混合信号的时间间隔。

<!--当参数DateStyle设置为ISO时， postgres格式的输出与PostgreSQL 8.4之前的版本一致。

当参数DateStyle设置为非-ISO， postgres_verbose格式的输出与PostgreSQL 8.4之前的版本一致。-->

iso_8601格式的输出与ISO 8601标准的"format with designators"一致。

表10 间隔输出格式示例

|格式 | 年-月间隔 | 天-时间间隔 | 混合间隔 |
| -- | -- | -- | -- |
| sql_standard | 1-2 | 3 4:05:06 | -1-2 +3 -4:05:06 |
| postgres | 1 年 2 个月 | 3 天 04:05:06 |-1 年 -2 个月 +3 天 -04:05:06 |
| postgres_verbose | @ 1 年 2 个月 | @ 3 天 4 小时 5 分 6 秒 | @ 1 年 2 个月 -3 天 4 小时 5 分 6 秒以前 |
| iso_8601 | P1Y2M | P3DT4H5M6S | P-1Y-2M3DT-4H-5M-6S |


