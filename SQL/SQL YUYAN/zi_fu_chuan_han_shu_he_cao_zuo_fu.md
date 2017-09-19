# 字符串函数和操作符

本节描述了用于检查和操作字符串数值的函数和操作符。 在这个环境中的字符串包括character,character varying, text类型的值。除非另外说明，所有下面列出的函数都可以处理这些类型， 不过要小心的是，在使用character类型的时候，需要注意自动填充的潜在影响。 有些函数还可以处理位串类型。

SQL定义了一些字符串函数，用特定的关键字而不是逗号来分隔参数。 详情请见表-1。EdsSQL 也提供了使用正常的函数调用语法实现的这些函数的版本(参阅 表-2)。

<!--注意: 在PostgreSQL 8.3之前， 这些函数将默默接受一些非字符串数据类型的值，由于存在从这些数据类型到text 的隐式强制转换，转换后的它们经常发生意外的行为，因此删除了隐式强制转换。 然而，字符串连接操作符(||)仍接受非字符串输入， 只要至少有一个输入是字符串类型，如表 9-5所示。 对于其它情况下，如果你需要重复以前的行为，插入一个明确的强制转换到text。-->

表-1 SQL 字符串函数和操作符

|函数|	返回类型|	描述|	示例|	结果|
| -- | -- | -- | -- | -- | -- |
|string II string|	text|	 字符串连接|	'Post' II 'greSQL'	|EdsSQL|
|string II non-string 或 non-string II string|	text|	 带有一个非字符串输入的字符串连接|	'Value: ' II 42|	Value: 42|
|bit_length(string)|	int	|字符串的位	|bit_length('jose')	|32|
|char_length(string) 或 character_length(string)|	int|	 字符串中的字符个数|	char_length('jose')|	4|
|lower(string)	|text|	把字符串转化为小写|	lower('TOM')|	tom|
|octet_length(string)|	int	|字符串中的字节数|	octet_length('jose')|	4|
|overlay(string placing string from int [for int])	|text|	 替换子字符串|	overlay('Txxxxas' placing 'hom' from 2 for 4)|	Thomas|
|position(substring in string)|	int	|指定子字符串的位置|	position('om' in 'Thomas')|	3|
|substring(string [from int] [for int])|	text|	 截取子字符串|	substring('Thomas' from 2 for 3)|	hom|
|substring(string from pattern)	|text	 |截取匹配POSIX正则表达式的子字符串。|	substring('Thomas' from '...$')|	mas|
|substring(string from pattern for escape)	|text	| 截取匹配SQL正则表达式的子字符串。|	substring('Thomas' from '%#"o_a#"_' for '#')|	oma|
|trim([leading I trailing I both] [characters] from string)	|text|	 从字符串string的开头/结尾/两边删除只包含 characters中字符 (缺省是空白)的最长的字符串	|trim(both 'x' from 'xTomxx')|	Tom|
|upper(string)	|text|	把字符串转化为大写|	upper('tom')|	TOM|

还有额外的字符串操作函数可以用，它们在表2列出。 它们有些在内部用于实现表-1 列出的SQL标准字符串函数。

表-2 其它字符串函数

|函数|	返回类型|	描述|	示例|	结果|
| -- | -- | -- | -- | -- | -- |
|ascii(string)|int	| 参数中第一个字符的ASCII编码值。 对于UTF8返回字符的宽字节编码值。 对于其它的多字节编码，参数必须是一个ASCII字符。|	ascii('x')|120|
|btrim(string text [, characters text])	|text|	 从string开头和结尾删除只包含 characters中字符(缺省是空白)的最长字符串。|	btrim('xyxtrimyyx', 'xy')|	trim|
|chr(int)|	text|	 给定编码的字符。对于UTF8这个参数作为宽字节代码处理。 对于其它的多字节编码，这个参数必须指定一个ASCII字符， 因为text数据类型无法存储NULL数据字节，不能将NULL(0)作为字符参数。|	chr(65)	|A|
|concat(str "any" [, str "any" [, ...] ])|	text|	 连接所有参数的文本表示。NULL 参数被忽略。|	concat('abcde', 2, NULL, 22)|	abcde222|
|concat_ws(sep text, str "any" [, str "any" [, ...] ])|	text|	 连接所有参数，但是第一个参数是分隔符，用于将所有参数分隔。NULL 参数被忽略。|	concat_ws(',', 'abcde', 2, NULL, 22)|	abcde,2,22|
|convert(string bytea, src_encoding name, dest_encoding name)|	bytea	| 把原来编码为src_encoding的字符串转换为 dest_encoding编码。 在这种编码格式中string必须是有效的。 用CREATE CONVERSION定义转换。 这也有些预定义的转换。 |	convert('text_in_utf8', 'UTF8', 'LATIN1')|	text_in_utf8 用 Latin-1 编码表示 (ISO 8859-1)|
|convert_from(string bytea, src_encoding name)	|text|	 把原来编码为src_encoding的字符串转换为数据库编码格式。 这种编码格式中，string必须是有效的。|	convert_from('text_in_utf8', 'UTF8')|	text_in_utf8 用当前数据库编码表示|
|convert_to(string text, dest_encoding name)	|bytea	| 将字符串转化为dest_encoding编码格式。|	convert_to('some text', 'UTF8')	some| text 用 UTF8 编码表示|
|decode(string text, format text)	|bytea	| 把用string表示的文本里面的二进制数据解码。 format选项和encode相同。|	decode('MTIzAAE=', 'base64')|	\x3132330001|
|encode(data bytea, format text)|	text|	 把二进制数据编码为文本表示。支持的格式有：base64, hex, escape。escape 转换零字节和高位设置字节为八进制序列(\nnn) 和双反斜杠。|	encode(E'123\\000\\001', 'base64')|	MTIzAAE=
|format(formatstr text I [, formatarg "any" [, ...] ])	|text|	 根据格式字符串格式参数。这个函数类似C函数sprintf。 |	format('Hello %s, %1$s', 'World')|	Hello World, World|
|initcap(string)	|text|	 把每个单词的第一个字母转为大写，其它的保留小写。 单词是一系列字母数字组成的字符，用非字母数字分隔。|	initcap('hi THOMAS')|	Hi Thomas|
|left(str text, n int)|	text|	 返回字符串的前n个字符。当n是负数时， 返回除最后InI个字符以外的所有字符。|	left('abcde', 2)|	ab|
|length(string)	|int|	string中字符的数目|	length('jose')|	4|
|length(string bytea, encoding name )	|int|	 指定encoding编码格式的string的字符数。 在这个编码格式中，string必须是有效的。|	length('jose', 'UTF8')|	4|
|lpad(string text, length int [, fill text])|	text|	 通过填充字符fill(缺省时为空白)， 把string填充为length长度。 如果string已经比length长则将其尾部截断。|	lpad('hi', 5, 'xy')	|xyxhi|
|ltrim(string text [, characters text])|	text|	 从字符串string的开头删除只包含characters 中字符(缺省是一个空白)的最长的字符串。|	ltrim('zzzytrim', 'xyz')|	trim|
|md5(string)|	text	| 计算string的MD5散列，以十六进制返回结果。|	md5('abc')	|900150983cd24fb0 d6963f7d28e17f72
|pg_client_encoding()|	name|	 当前客户端编码名称|	pg_client_encoding()|	SQL_ASCII|
|quote_ident(string text)|	text|	 返回适用于SQL语句的标识符形式(使用适当的引号进行界定)。 只有在必要的时候才会添加引号(字符串包含非标识符字符或者会转换大小写的字符)。 嵌入的引号被恰当地写了双份。|	quote_ident('Foo bar')|	"Foo bar"|
|quote_literal(string text)	|text|	 返回适用于在SQL语句里当作文本使用的形式(使用适当的引号进行界定)。 嵌入的引号和反斜杠被恰当地写了双份。请注意，当输入是null时， quote_literal返回null；如果参数可能为null， 通常quote_nullable更适用。|	quote_literal(E'O\'Reilly')|	'O''Reilly'|
|quote_literal(value anyelement)|	text|	 将给定的值强制转换为text，加上引号作为文本。嵌入的引号和反斜杠被恰当地写了双份。|	quote_literal(42.5)	|'42.5'|
|quote_nullable(string text)	|text	| 返回适用于在SQL语句里当作字符串使用的形式(使用适当的引号进行界定)。 或者，如果参数为空，返回NULL。嵌入的引号和反斜杠被恰当地写了双份。|	quote_nullable(NULL)|	NULL|
|quote_nullable(value anyelement)|	text|	 将给定的参数值转化为text，加上引号作为文本；或者，如果参数为空， 返回NULL。嵌入的引号和反斜杠被恰当地写了双份。|	quote_nullable(42.5)|	'42.5'|
|regexp_matches(string text, pattern text [, flags text])|	setof text[]|	 返回string中所有匹配POSIX正则表达式的子字符串。 |	regexp_matches('foobarbequebaz', '(bar)(beque)')|	{bar,beque}|
|regexp_replace(string text, pattern text, replacement text [, flags text])	|text|	 替换匹配 POSIX 正则表达式的子字符串。|	regexp_replace('Thomas', '.[mN]a.', 'M')|	ThM|
|regexp_split_to_array(string text, pattern text [, flags text ])|	text[]	| 用POSIX正则表达式作为分隔符，分隔string。| regexp_split_to_array('hello world', E'\\s+')|	{hello,world}|
|regexp_split_to_table(string text, pattern text [, flags text])|	setof text|	 用POSIX正则表达式作为分隔符，分隔string。 |	regexp_split_to_table('hello world', E'\\s+')|	hello<br/>world<br/>(2 rows)|
|repeat(string text, number int)|	text|	将string重复number次|	repeat('Pg', 4)|	PgPgPgPg|
|replace(string text, from text, to text)	|text|	 把字符串string里出现地所有子字符串from 替换成子字符串to|	replace('abcdefabcdef', 'cd', 'XX')|	abXXefabXXef|
|reverse(str)|	text	| 返回颠倒的字符串|	reverse('abcde')|	edcba|
|right(str text, n int)	|text|	 返回字符串中的后n个字符。当n是负值时， 返回除前InI个字符以外的所有字符。|	right('abcde', 2)	|de|
|rpad(string text, length int [, fill text])	|text|	 使用填充字符fill(缺省时为空白)， 把string填充到length长度。 如果string已经比length长则将其从尾部截断。|	rpad('hi', 5, 'xy')|	hixyx|
|rtrim(string text [, characters text])|	text|	 从字符串string的结尾删除只包含 characters中字符(缺省是个空白)的最长的字符串。|	rtrim('trimxxxx', 'x')|	trim|
|split_part(string text, delimiter text, field int)|	text|	 根据delimiter分隔string 返回生成的第 field 个子字符串(1为基)。|	split_part('abc~@~def~@~ghi', '~@~', 2)	|def|
|strpos(string, substring)|	int	| 指定的子字符串的位置。和position(substring in string)一样，不过参数顺序相反。	|strpos('high', 'ig')|	2|
|substr(string, from [, count])|	text|	 抽取子字符串。和substring(string from from for count))一样|	substr('alphabet', 3, 2)|	ph|
|to_ascii(string text [, encoding text])	|text	| 把string从其它编码转换为ASCII (仅支持LATIN1, LATIN2, LATIN9, WIN1250编码)。|	to_ascii('Karel')|	Karel|
|to_hex(number int or bigint)|	text|	 把number转换成十六进制表现形式|	to_hex(2147483647)|	7fffffff|
|translate(string text, from text, to text)	|text	 |把在string中包含的任何匹配from 中字符的字符转化为对应的在to中的字符。 如果from比to长， 删掉在from中出现的额外的字符。	|translate('12345', '143', 'ax')|	a2x5|

concat, concat_ws和format 函数是可变的，所以用VARIADIC 关键字标记传递的数值以连接或者格式化为一个数组是可能的。 数组的元素对函数来说是单独的普通参数。 如果可变数组的元素是NULL，那么concat和concat_ws 返回NULL，但是format把NULL作为零元素数组对待。


表-3 内置的转换

|转换名 [a]	|源编码|	目的编码|
| -- | -- | -- |
|ascii_to_mic|	SQL_ASCII|	MULE_INTERNAL|
|ascii_to_utf8|	SQL_ASCII|	UTF8|
|big5_to_euc_tw	|BIG5|	EUC_TW|
|big5_to_mic|	BIG5|	MULE_INTERNAL|
|big5_to_utf8|	BIG5|	UTF8|
|euc_cn_to_mic|	EUC_CN|	MULE_INTERNAL|
|euc_cn_to_utf8|	EUC_CN|	UTF8|
|euc_jp_to_mic|	EUC_JP|	MULE_INTERNAL|
|euc_jp_to_sjis	|EUC_JP	|SJIS|
|euc_jp_to_utf8|	EUC_JP|	UTF8|
|euc_kr_to_mic|	EUC_KR|	MULE_INTERNAL|
|euc_kr_to_utf8|	EUC_KR|	UTF8|
|euc_tw_to_big5	|EUC_TW	|BIG5|
|euc_tw_to_mic	|EUC_TW|	MULE_INTERNAL|
|euc_tw_to_utf8	|EUC_TW|	UTF8|
|gb18030_to_utf8|	GB18030|	UTF8|
|gbk_to_utf8	|GBK|	UTF8|
|iso_8859_10_to_utf8|	LATIN6	|UTF8|
|iso_8859_13_to_utf8|	LATIN7|	UTF8|
|iso_8859_14_to_utf8|	LATIN8|	UTF8|
|iso_8859_15_to_utf8	|LATIN9|	UTF8|
|iso_8859_16_to_utf8|	LATIN10|	UTF8|
|iso_8859_1_to_mic	|LATIN1|	MULE_INTERNAL|
|iso_8859_1_to_utf8|	LATIN1|	UTF8|
|iso_8859_2_to_mic|	LATIN2|	MULE_INTERNAL|
|iso_8859_2_to_utf8|	LATIN2|	UTF8|
|iso_8859_2_to_windows_1250|	LATIN2|	WIN1250|
|iso_8859_3_to_mic	|LATIN3	|MULE_INTERNAL|
|iso_8859_3_to_utf8|	LATIN3|	UTF8|
|iso_8859_4_to_mic	|LATIN4|	MULE_INTERNAL|
|iso_8859_4_to_utf8|	LATIN4	|UTF8|
|iso_8859_5_to_koi8_r|	ISO_8859_5|	KOI8R|
|iso_8859_5_to_mic	|ISO_8859_5|	MULE_INTERNAL|
|iso_8859_5_to_utf8|	ISO_8859_5	|UTF8|
|iso_8859_5_to_windows_1251	|ISO_8859_5|	WIN1251|
|iso_8859_5_to_windows_866	|ISO_8859_5|	WIN866|
|iso_8859_6_to_utf8|	ISO_8859_6|	UTF8|
|iso_8859_7_to_utf8	|ISO_8859_7|	UTF8|
|iso_8859_8_to_utf8	|ISO_8859_8|	UTF8|
|iso_8859_9_to_utf8	|LATIN5	|UTF8|
|johab_to_utf8	|JOHAB|	UTF8|
|koi8_r_to_iso_8859_5|	KOI8R|	ISO_8859_5|
|koi8_r_to_mic|	KOI8R|	MULE_INTERNAL|
|koi8_r_to_utf8|	KOI8R|	UTF8|
|koi8_r_to_windows_1251	|KOI8R	|WIN1251|
|koi8_r_to_windows_866|	KOI8R|	WIN866|
|koi8_u_to_utf8	|KOI8U	|UTF8|
|mic_to_ascii|	MULE_INTERNAL|	SQL_ASCII|
|mic_to_big5|	MULE_INTERNAL|	BIG5|
|mic_to_euc_cn|	MULE_INTERNAL|	EUC_CN|
|mic_to_euc_jp|	MULE_INTERNAL|	EUC_JP|
|mic_to_euc_kr|	MULE_INTERNAL|	EUC_KR|
|mic_to_euc_tw|	MULE_INTERNAL|	EUC_TW|
|mic_to_iso_8859_1	|MULE_INTERNAL|	LATIN1|
|mic_to_iso_8859_2	|MULE_INTERNAL|	LATIN2|
|mic_to_iso_8859_3|	MULE_INTERNAL|	LATIN3|
|mic_to_iso_8859_4	|MULE_INTERNAL|	LATIN4|
|mic_to_iso_8859_5	|MULE_INTERNAL|	ISO_8859_5|
|mic_to_koi8_r|	MULE_INTERNAL|	KOI8R|
|mic_to_sjis|	MULE_INTERNAL	|SJIS|
|mic_to_windows_1250|	MULE_INTERNAL|	WIN1250|
|mic_to_windows_1251|	MULE_INTERNAL|	WIN1251|
|mic_to_windows_866	|MULE_INTERNAL|	WIN866|
|sjis_to_euc_jp	|SJIS	|EUC_JP|
|sjis_to_mic|	SJIS|	MULE_INTERNAL|
|sjis_to_utf8	|SJIS	|UTF8|
|tcvn_to_utf8|	WIN1258|	UTF8|
|uhc_to_utf8	|UHC|	UTF8|
|utf8_to_ascii|	UTF8|	SQL_ASCII|
|utf8_to_big5|	UTF8|	BIG5|
|utf8_to_euc_cn	|UTF8|	EUC_CN|
|utf8_to_euc_jp	|UTF8|	EUC_JP|
|utf8_to_euc_kr	|UTF8|	EUC_KR|
|utf8_to_euc_tw|	UTF8|	EUC_TW|
|utf8_to_gb18030	|UTF8|	GB18030|
|utf8_to_gbk	|UTF8|	GBK|
|utf8_to_iso_8859_1	|UTF8|	LATIN1|
|utf8_to_iso_8859_10|	UTF8|	LATIN6|
|utf8_to_iso_8859_13|	UTF8|	LATIN7|
|utf8_to_iso_8859_14|	UTF8|	LATIN8|
|utf8_to_iso_8859_15|	UTF8|	LATIN9|
|utf8_to_iso_8859_16|	UTF8|	LATIN10|
|utf8_to_iso_8859_2	|UTF8|	LATIN2|
|utf8_to_iso_8859_3	|UTF8|	LATIN3|
|utf8_to_iso_8859_4	|UTF8|	LATIN4|
|utf8_to_iso_8859_5|	UTF8|	ISO_8859_5|
|utf8_to_iso_8859_6	|UTF8|	ISO_8859_6|
|utf8_to_iso_8859_7|	UTF8|	ISO_8859_7|
|utf8_to_iso_8859_8|	UTF8|	ISO_8859_8|
|utf8_to_iso_8859_9	|UTF8|	LATIN5|
|utf8_to_johab	|UTF8|	JOHAB|
|utf8_to_koi8_r|	UTF8|	KOI8R|
|utf8_to_koi8_u|	UTF8|	KOI8U|
|utf8_to_sjis|	UTF8|	SJIS|
|utf8_to_tcvn|	UTF8|	WIN1258|
|utf8_to_uhc|	UTF8|	UHC|
|utf8_to_windows_1250	|UTF8|	WIN1250|
|utf8_to_windows_1251|	UTF8|	WIN1251|
|utf8_to_windows_1252|	UTF8|	WIN1252|
|utf8_to_windows_1253|	UTF8|	WIN1253|
|utf8_to_windows_1254|	UTF8|	WIN1254|
|utf8_to_windows_1255|	UTF8|	WIN1255|
|utf8_to_windows_1256|	UTF8|	WIN1256|
|utf8_to_windows_1257|	UTF8|	WIN1257|
|utf8_to_windows_866|	UTF8|	WIN866|
|utf8_to_windows_874|	UTF8|	WIN874|
|windows_1250_to_iso_8859_2	|WIN1250|	LATIN2|
|windows_1250_to_mic	|WIN1250|	MULE_INTERNAL|
|windows_1250_to_utf8|	WIN1250	|UTF8|
|windows_1251_to_iso_8859_5	|WIN1251|	ISO_8859_5|
|windows_1251_to_koi8_r|	WIN1251	|KOI8R|
|windows_1251_to_mic|	WIN1251	|MULE_INTERNAL|
|windows_1251_to_utf8|	WIN1251	|UTF8|
|windows_1251_to_windows_866	|WIN1251|	WIN866|
|windows_1252_to_utf8	|WIN1252	|UTF8|
|windows_1256_to_utf8	|WIN1256|	UTF8|
|windows_866_to_iso_8859_5|	WIN866	|ISO_8859_5|
|windows_866_to_koi8_r|	WIN866	|KOI8R|
|windows_866_to_mic	|WIN866	|MULE_INTERNAL|
|windows_866_to_utf8|	WIN866|	UTF8|
|windows_866_to_windows_1251|	WIN866|	WIN|
|windows_874_to_utf8	|WIN874	|UTF8|
|euc_jis_2004_to_utf8|	EUC_JIS_2004|	UTF8|
|utf8_to_euc_jis_2004|	UTF8|	EUC_JIS_2004|
|shift_jis_2004_to_utf8|	SHIFT_JIS_2004	|UTF8|
|utf8_to_shift_jis_2004|	UTF8|	SHIFT_JIS_2004|
|euc_jis_2004_to_shift_jis_2004	|EUC_JIS_2004|	SHIFT_JIS_2004|
|shift_jis_2004_to_euc_jis_2004	|SHIFT_JIS_2004	|EUC_JIS_2004|

表注:
a. 转换名遵循一个标准的命名模式：将源编码中的所有非字母数字字符用下划线替换， 后面跟着_to_，然后后面再跟着经过相似处理的目标编码的名字。 因此这些名字可能和客户的编码名字不同。

<br/>**格式化**

函数format生成根据格式字符串格式化了的输出，风格类似于C函数sprintf。
```SQL
format(formatstr text [, formatarg "any" [, ...] ])
```
*formatstr*是指定结果如何格式化的格式字符串。格式字符串中的文本直接拷贝到结果中， 除非已经使用了格式说明符。格式说明符在字符串中作为占位符使用， 定义后续函数参数应该格式化并且插入到结果中。每个*formatarg* 参数根据这种数据类型的通常输出规则转化为文本，然后根据格式说明符格式化并且插入到结果中。

格式说明符由%字符引进，格式为
```SQL
%[position][flags][width]type
```
组件的字段有：

*position *(optional)

n$格式的字符串，这里的n 是要打印的参数的索引。索引为1表示在formatstr 之后的第一个参数。如果省略了formatstr， 默认使用序列中的下一个参数。

*flags* (optional)

附加选项，控制如何格式化格式说明符的输出。当前只支持负号(-)， 负号导致格式说明符的输出是左对齐的。这是没有影响的，除非指定了 width字段。

*width* (optional)

声明字符数的minimum值用来显示格式说明符的输出。需要补充宽度时， 空白添加到输出的左侧或右侧（取决于-标志）。一个比较小的宽度不会导致输出的截断， 只是简单的忽略了。宽度可以用下列方法指定：一个正整数；一个星号(*) 表示使用下一个函数参数作为宽度；或一个格式为*n$ 的字符串表示使用第n个函数参数作为宽度。

如果宽度来自函数参数，那么这个参数在作为格式说明符的数值之前消耗掉。 如果宽度参数是负的，那么结果是左对齐的（就像声明了-标志一样）， 并且字段长度为abs(width)。

*type* (required)
格式转换的类型用来产生格式说明符的输出。支持下列的类型：

<li>s格式参数值为简单的字符串。空值作为空字符串对待。</li>

<li>I将参数值作为SQL标识符对待，如果需要，双写它。值为空是错误的。</li>

<li>L引用参数值作为SQL文字。空值用字符串NULL显示，没有引用。</li><br/>
除了上述的格式说明符，特殊的序列%%可以用作输出%字符。

这里有一些基本的格式转换的例子：
```SQL
SELECT format('Hello %s', 'World');
Result: Hello World

SELECT format('Testing %s, %s, %s, %%', 'one', 'two', 'three');
Result: Testing one, two, three, %

SELECT format('INSERT INTO %I VALUES(%L)', 'Foo bar', E'O\'Reilly');
Result: INSERT INTO "Foo bar" VALUES('O''Reilly')

SELECT format('INSERT INTO %I VALUES(%L)', 'locations', E'C:\\Program Files');
Result: INSERT INTO locations VALUES(E'C:\\Program Files')
```
这里是使用*width*字段和-标志的例子：
```SQL
SELECT format('|%10s|', 'foo');
Result: |       foo|

SELECT format('|%-10s|', 'foo');
Result: |foo       |

SELECT format('|%*s|', 10, 'foo');
Result: |       foo|

SELECT format('|%*s|', -10, 'foo');
Result: |foo       |

SELECT format('|%-*s|', 10, 'foo');
Result: |foo       |

SELECT format('|%-*s|', -10, 'foo');
Result: |foo       |
```
下面是使用*position*字段的例子：
```SQL
SELECT format('Testing %3$s, %2$s, %1$s', 'one', 'two', 'three');
Result: Testing three, two, one

SELECT format('|%*2$s|', 'foo', 10, 'bar');
Result: |       bar|

SELECT format('|%1$*2$s|', 'foo', 10, 'bar');
Result: |       foo|
```
和C函数sprintf不同，EdsSQL的format 函数允许带有或不带有position字段的格式说明符在相同的格式字符串中混合使用。 没有position字段的格式说明符总是使用最后消耗参数的下一个参数。 另外，format函数不要求在格式字符串中使用所有函数参数。例如：
```SQL
SELECT format('Testing %3$s, %2$s, %s', 'one', 'two', 'three');
Result: Testing three, two, three
```
%I和%L格式说明符对于安全构造动态SQL语句尤其有用。