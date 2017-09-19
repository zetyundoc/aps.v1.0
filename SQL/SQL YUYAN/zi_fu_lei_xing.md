# 字符类型

表1 字符类型

| 名字 | 描述 |
| -- | -- |
| character varying(*n*), varchar(*n*) | 变长，有长度限制 |
| character(*n*), char(*n*) | 定长，不足补空白 |
| text | 变长，无长度限制 |


表1 显示了在EdsSQL 里可用于一般用途的字符类型。

SQL定义了两种基本的字符类型：character varying(*n*) 和character(*n*)，这里的n 是一个正整数。两种类型都可以存储最多n个字符的字符串（没有字节）。 试图存储更长的字符串到这些类型的字段里会产生一个错误， 除非超出长度的字符都是空白，这种情况下该字符串将被截断为最大长度。 这个看上去有点怪异的例外是SQL标准要求的。 如果要存储的字符串比声明的长度短，类型为character的数值将会用空白填满； 而类型为character varying的数值将只是存储短些的字符串。

如果我们明确地把一个数值转换成character varying(*n*) 或character(*n*)，那么超长的数值将被截断成n 个字符，且不会抛出错误。这也是SQL标准的要求。

varchar(*n*)和char(*n*) 分别是character varying(*n*) 和character(*n*)的别名， 没有声明长度的character等于character(1)； 如果不带长度说明词使用character varying， 那么该类型接受任何长度的字符串。后者是EdsSQL的扩展。

另外，EdsSQL提供text类型， 它可以存储任何长度的字符串。尽管类型text不是SQL 标准，但是许多其它SQL数据库系统也有它。

character类型的数值物理上都用空白填充到指定的长度n， 并且以这种方式存储和显示。不过，填充的空白是无语意的。在比较两个character 值的时候，填充的空白都不会被关注，在转换成其它字符串类型的时候， character值里面的空白会被删除。请注意， 在character varying和text数值里，结尾的空白是有语意的。 并且当使用模式匹配时，如LIKE，使用正则表达式。

一个简短的字符串（最多126个字节）的存储要求是1个字节加上实际的字符串， 其中包括空格填充的character。更长的字符串有4个字节的开销， 而不是1。长的字符串将会自动被系统压缩，因此在磁盘上的物理需求可能会更少些。 更长的数值也会存储在后台表里面，这样它们就不会干扰对短字段值的快速访问。 不管怎样，允许存储的最长字符串大概是1GB 。允许在数据类型声明中出现的n 的最大值比这还小。修改这个行为没有什么意义，因为在多字节编码下字符和字节的数目可能差别很大。 如果你想存储没有特定上限的长字符串，那么使用text 或没有长度声明的character varying，而不要选择一个任意长度限制。

<p style="color: #c09853;background: #fcf8e3 none repeat scroll 0% 0%; font-size:13px;" >提示: 这三种类型之间没有性能差别，除了当使用填充空白类型时的增加存储空间， 和当存储长度约束的列时一些检查存入时长度的额外的CPU周期。 虽然在某些其它的数据库系统里，character(*n*) 有一定的性能优势，但在EdsSQL里没有。 事实上，character(*n*)通常是这三个中最慢的， 因为额外存储成本。在大多数情况下，应该使用text 或character varying。</p>

数据库的字符集决定用于存储文本值的字符集。

例1 使用字符类型
```SQL
CREATE TABLE test1 (a character(4));
INSERT INTO test1 VALUES ('ok');
SELECT a, char_length(a) FROM test1; -- (1)
  a   | char_length
------+-------------
 ok   |           2

CREATE TABLE test2 (b varchar(5));
INSERT INTO test2 VALUES ('ok');
INSERT INTO test2 VALUES ('good      ');
INSERT INTO test2 VALUES ('too long');
ERROR:  value too long for type character varying(5)
INSERT INTO test2 VALUES ('too long'::varchar(5)); -- 明确截断
SELECT b, char_length(b) FROM test2;
   b   | char_length
-------+-------------
 ok    |           2
 good  |           5
 too l |           5
```

在EdsSQL里另外还有两种定长字符类型。 在表2 里显示。name 类型只用于在内部系统表中存储标识符并且不是给一般用户使用的。 该类型长度当前定为 64 字节(63 可用字符加结束符)但应该使用C源码中的常量 NAMEDATALEN引用。这个长度是在编译的时候设置的， 因而可以为特殊用途调整，缺省的最大长度在以后的版本可能会改变。 类型"char"(注意引号)和char(1)是不一样的， 它只用了一个字节的存储空间。它在系统内部用于系统表当做过分简单化的枚举类型用。

表2 特殊字符类型

| 名字 | 存储空间 | 描述 |
| -- | -- | -- |
| "char" | 1 字节 | 单字节内部类型 |
| name | 64 字节 | 用于对象名的内部类型 |

