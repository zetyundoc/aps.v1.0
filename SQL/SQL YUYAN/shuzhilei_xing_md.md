# 数值类型

数值类型由 2 、4 或 8 字节的整数以及 4 或 8 字节的浮点数和可选精度的小数组成。 下表列出了所有可用类型。

表1 数值类型

| 名字| 存储空间 | 描述 | 范围 |
| -- | -- | -- | -- |
| smallint | 2 字节 | 小范围整数 | -32768 到 +32767 |
| integer | 4 字节 | 常用的整数 | -2147483648 到 +2147483647 |
| bigint | 8 字节 | 大范围整数 | -9223372036854775808 到 +9223372036854775807 |
| decimal | 变长| 用户声明精度，精确 | 小数点前 131072 位；小数点后 16383 位 |
| numeric | 变长 | 用户声明精度，精确 | 小数点前 131072 位；小数点后 16383 位 |
| real | 4 字节 | 变精度，不精确 | 6 位十进制数字精度 |
| double precision | 8 字节 | 变精度，不精确 | 15 位十进制数字精度 |
| smallserial | 2 字节 | 小范围自增整数 | 1 到 32767 |
| serial | 4 字节 | 自增整数 | 1 到 2147483647 |
| bigserial | 8 字节 | 大范围自增整数 | 1 到 9223372036854775807 |

 数值类型对应有一套完整的数学操作符和函数。 下面的几节详细描述这些类型。

<br/>**整数类型**

smallint，integer和bigint类型存储各种范围的全部是数字的数， 也就是没有小数部分的数字。试图存储超出范围以外的数值将导致一个错误。

常用的类型是integer，因为它提供了在范围、存储空间、性能之间的最佳平衡。 一般只有在磁盘空间紧张的时候才使用smallint。 当integer的范围不够的时候才使用bigint。

SQL只声明了整数类型integer(或int)， smallint和bigint。类型int2, int4 和int8都是扩展，并且也在许多其它SQL数据库系统中使用。

<br/>**任意精度数值**

numeric类型可以存储非常大的数字并且准确地进行计算。 我们特别建议将它用于货币金额和其它要求精确计算的场合。不过， numeric类型上的算术运算比整数类型或者我们下一节描述的浮点数类型要慢很多。

在随后的内容里，我们使用下述术语：一个numeric类型的标度 (*scale*)是小数部分的位数，精度(*precision*) 是全部数据位的数目，也就是小数点两边的位数总和。 因此数字 23.5141 的精度为 6 而标度为 4 。你可以认为整数的标度为零。

numeric字段的最大精度和最大标度都是可以配置的。 要声明一个字段的类型为numeric，你可以用下面的语法：
```SQL
NUMERIC(precision, scale)
```
精度必须为正数，标度可以为零或者正数。另外:
```SQL
NUMERIC(precision)
```
选择了标度为 0 。不带任何精度与标度的声明
```SQL
NUMERIC
```
则创建一个可以存储一个直到实现精度上限的任意精度和标度的数值， 一个这样类型的字段将不会把输入数值转化成任何特定的标度， 而带有标度声明的numeric字段将把输入值转化为该标度。 SQL标准要求缺省的标度是 0(也就是转化成整数精度)。 我们觉得这样做有点没用。如果你关心移植性， 那你最好总是明确声明精度和标度。

<p style="color: #c09853;background: #fcf8e3 none repeat scroll 0% 0%; font-size:13px;" >注意: 当在类型声明中显示指定精度时允许的最大值为1000；没有指定精度的 NUMERIC遵从表1 里的描述。
</p>

如果一个要存储的数值的标度比字段声明的标度高， 那么系统将尝试圆整(四舍五入)该数值到指定的小数位。然后， 如果小数点左边的数据位数超过了声明的精度减去声明的标度，那么将抛出一个错误。

numeric 类型的数据值在物理上是不带任何前导或者后缀零的形式存储的。 因此，字段上声明的精度和标度都是最大值，而不是固定分配的。在这个方面， numeric类型更类似于varchar(*n*)而不是 char(*n*)。实际存储是每四个十进制位两个字节， 然后在整个数据上加上三到八个字节的额外开销。

除了普通的数字值之外，numeric类型允许用特殊值NaN 表示"不是一个数字"。任何在NaN上面的操作都生成另外一个 NaN。如果在 SQL 命令里把这些值当作一个常量写， 你必须在其周围放上单引号，比如UPDATE table SET x = 'NaN'。 在输入时，字符串NaN是大小写无关的。

<p style="color: #c09853;background: #fcf8e3 none repeat scroll 0% 0%; font-size:13px;" >注意: 在大多数"not-a-number"概念中，不认为NaN等于其他数值类型 （包括NaN）。为了能够存储numeric类型的值， 并且使用Tree索引，EdsSQL认为NaN相等， 并且大于所有非NaN值。</p>


类型decimal和numeric是等效的。 两种类型都是SQL标准。

<br/>**浮点数类型**

数据类型real和double precision是不精确的、变精度的数字类型。 实际上，这些类型是IEEE 754标准二进制浮点数算术(分别对应单和双精度)的一般实现， 外加下层处理器、操作系统和编译器对它的支持。

不精确意味着一些数值不能精确地转换成内部格式并且是以近似值存储的， 因此存储后再把数据打印出来可能有一些差异。 处理这些错误以及这些错误是如何在计算中传播的属于数学和计算机科学的一个完整的分支， 我们不会在这里进一步讨论它，这里的讨论仅限于如下几点：

<ul><li>如果你要求精确的计算(比如计算货币金额)，应使用numeric类型。</li>

<li>如果你想用这些类型做任何重要的复杂计算， 尤其是那些你对范围情况(无穷/下溢)严重依赖的事情，那你应该仔细评诂你的实现。</li>

<li>拿两个浮点数值进行相等性比较可能不像你想像那样运转。</li></ul>

在大多数平台上，real类型的范围是至少1E-37到1E+37， 精度至少是6位小数。double precision的范围通常是1E-307到1E+308， 精度是至少15位数字。太大或者太小的数值都会导致错误。如果输入数据的精度太高， 那么将会发生圆整。太接近零的数字，如果无法与零值的表现形式相区分就会产生下溢错误。

<p style="color: #c09853;background: #fcf8e3 none repeat scroll 0% 0%; font-size:13px;" >注意: 当一个浮点数值转化为文本输出时，extra_float_digits 设置控制额外有效数字的位数。默认值是0，EdsSQL 支持的平台上的输出是一样的。增加这个值产生的输出将更精确的表示存储值， 但是可能不利于移植。</p>

除了普通的数字值之外，浮点类型还有几个特殊值：

Infinity<br/>
-Infinity<br/>
NaN<br/>

这些值分别表示 IEEE 754 特殊值"正无穷大"、"负无穷大"、 "不是一个数字"。在不遵循 IEEE 754 浮点算术的机器上， 这些值的含义可能不是预期的。如果在 SQL 命令里把这些数值当作常量写， 你必须在它们周围放上单引号，像这样：UPDATE table SET x = 'Infinity'。 输入时，这些值是以大小写无关的方式识别的。

<p style="color: #c09853;background: #fcf8e3 none repeat scroll 0% 0%; font-size:13px;" >注意: IEEE754声明NaN不应该等于任何其他浮点值（包括NaN）。 为了能存储浮点值，并且使用Tree索引，EdsSQL认为NaN 相等，并且大于所有非NaN值。</p>

EdsSQL还支持 SQL 标准表示法float 和float(p)用于声明非精确的数值类型。 其中的p声明以二进制位表示的最低可接受精度。 在选取real类型的时候，EdsSQL接受 float(1)到float(24)，在选取double precision 的时候，接受float(25)到float(53)。 在允许范围之外的p值将导致一个错误。 没有声明精度的float将被当作double precision。

<!--注意: PostgreSQL 7.4以前，在float(p) 里面的精度会被当作是这么多位数的十进制位。到 7.4 已经被修改成与 SQL 标准匹配， 标准声明这个精度是以二进制位度量的。假设real和double precision 分别有 24 和 53 个二进制位的位数对 IEEE 标准的浮点实现来说是正确的。在非 IEEE 平台上， 这个数值可能略有偏差，但是为了简化，我们在所有平台上都用了同样的p值范围。-->

<br/>**序列号类型**

smallserial,serial和bigserial类型不是真正的类型， 只是为在表中创建唯一标识做的概念上的便利。类似其它一些数据库中的AUTO_INCREMENT 属性。在目前的实现中，下面一个语句：
```SQL
CREATE TABLE tablename (
    colname SERIAL
);
```
等价于声明下面几个语句：
```SQL
CREATE SEQUENCE tablename_colname_seq;
CREATE TABLE tablename (
    colname integer NOT NULL DEFAULT nextval('tablename_colname_seq')
);
ALTER SEQUENCE tablename_colname_seq OWNED BY tablename.colname;
```
因此，我们就创建了一个整数字段并且把它的缺省数值安排为从一个序列发生器读取。 应用了一个NOT NULL约束以确保 NULL 不会被插入。 在大多数情况下你可能还希望附加一个UNIQUE或PRIMARY KEY 约束避免意外地插入重复的数值，但这个不是自动的。最后， 将序列发生器"从属于"那个字段，这样当该字段或表被删除的时候也一并删除它。

<p style="color: #c09853;background: #fcf8e3 none repeat scroll 0% 0%; font-size:13px;" >注意: 因为smallserial, serial和bigserial 是使用序列实现的，所以显示在字段里的序列值可能有“漏洞”或者缺口，即使没有列曾经被删除。 一个从序列中分配的值仍然会“使用”，即使包含这个值的行没有成功的插入到表格的字段中。 这种情况是有可能发生的，比如，插入事务回滚。</p>

<!--注意: PostgreSQL 7.3以前，serial隐含UNIQUE。 但现在不再如此。如果你希望一个序列字段有一个唯一约束或者一个主键， 那么你现在必须声明，就像其它数据类型一样。-->

要在serial字段中插入序列中的下一个数值，主要是要注意serial 字段应该赋予缺省值。我们可以通过在INSERT 语句中把该字段排除在字段列表之外来实现，也可以通过使用DEFAULT关键字来实现。

类型名serial和serial4是等效的： 两者都创建integer字段。类型名bigserial和serial8 也一样，只不过它创建一个bigint字段。 如果你预计在表的生存期中使用的标识数目可能超过 2<sup>31</sup> 个， 那么你应该使用bigserial。类型名smallserial和 serial2也一样，只不过它创建一个smallint字段。

一个serial类型创建的序列在所属的字段被删除的时候自动删除。 你可以只删除序列而不删除字段，不过这将删除该字段的缺省值表达式。