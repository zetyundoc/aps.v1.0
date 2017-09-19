# INSERT
<br/>**命令**

```SQL
[ WITH [ RECURSIVE ] with_query [, ...] ]
INSERT INTO table_name [ ( column_name [, ...] ) ]
    { DEFAULT VALUES | VALUES ( { expression | DEFAULT } [, ...] ) [, ...] | query }
    [ RETURNING * | output_expression [ [ AS ] output_name ] [, ...] ]
```
<br/>**描述**

INSERT向表中插入新行。可以一次插入用值表达式声明的一行或多行， 或者一个查询结果表现出来的零行或多个行。

目标列表中的列/字段可以按任何顺序排列。如果完全没有列出任何字段名，那么缺省是全部字段， 顺序是按照表声明的时候的顺序；如果VALUES子句或者*query* 里面只提供了N个字段，那么就是头N个字段。VALUES 子句或者*query*提供的数值是以从左到右的方式与明确或者隐含的字段列表关联的。

每个没有在明确或者隐含的字段列表中出现的字段都将填充缺省值， 如果有声明的缺省值则用声明的那个，如果没有则用 NULL 。

如果每个字段的表达式不是正确的数据类型，系统将试图进行自动的类型转换。

可选的RETURNING子句将导致INSERT计算和返回实际插入的行， 它主要用于获取缺省的计算值(比如序列值)，不过，任何使用该表字段的表达式都是允许的。 RETURNING列表的语法都与SELECT的输出列表相同。

要想向表中插入数据，你必须有INSERT权限。如果指定了一个字段列表， 那么只需要在列表字段上的INSERT权限。使用RETURNING 子句需要要在RETURNING中提到的所有字段上的SELECT权限。 如果你使用了*query*子句插入来自查询里的数据行， 你还需要拥有在查询里使用的表或字段的SELECT权限。

<br/>**参数**

*with_query*：

WITH子句允许声明一个或多个可以在INSERT 查询中通过名字引用的子查询。<br/>
*query*(SELECT 语句) 也包含一个WITH子句是可能的。在这种情况下，两套* with_query*都可以在*query* 中引用，但是第二个优先，因为它嵌套更紧密。

*table_name*：现存表的名称(可以有模式修饰)。

*column_name*：通过*table_name*命名的表中的字段名。必要时， 字段名可以有子字段名或者数组下标修饰。向一个复合类型中的某些字段插入数据的话， 其它字段将是 NULL 。

DEFAULT VALUES：所有字段都会用它们的缺省值填充。

*expression*：赋予对应的 column 的一个有效表达式或值。

DEFAULT：对应的 column 将被它的缺省值填充。

*query*：一个查询(SELECT语句)，它提供插入的数据行。请参考SELECT 语句获取语法描述。

*output_expression*：INSERT命令在每一行都被插入之后用于计算输出结果的表达式。该表达式可以使用 通过table_name命名的表的的任意字段。 可以使用*返回被插入行的所有字段。

*output_name*：一个字段的输出名称。

<br/>**输出**

成功完成后，一条 INSERT命令返回一个下面形式的命令标签
```SQL
INSERT oid count
```
*count*是插入的行数。如果 *count*正好是一，并且目标表有 OID ， 那么*oid*是赋予插入行的OID。 否则oid是零。

如果INSERT命令包含RETURNING子句，其结果将和一个SELECT 语句相同，包含那些基于该命令实际插入的行计算的、定义在RETURNING列表中的字段的值。

<br/>**示例**

向表films里插入单独一行：
```SQL
INSERT INTO films VALUES
    ('UA502', 'Bananas', 105, '1971-07-13', 'Comedy', '82 minutes');
```
在这个例子里面省略了字段len，因此在它里面将存储缺省值：
```
INSERT INTO films (code, title, did, date_prod, kind)
    VALUES ('T_601', 'Yojimbo', 106, '1961-06-16', 'Drama');
```
在这个例子里，用DEFAULT子句作为日期字段，而不是声明一个数值：
```SQL
INSERT INTO films VALUES
    ('UA502', 'Bananas', 105, DEFAULT, 'Comedy', '82 minutes');
INSERT INTO films (code, title, did, date_prod, kind)
    VALUES ('T_601', 'Yojimbo', 106, DEFAULT, 'Drama');
```
插入一行完全由缺省值组成的数据行：
```
INSERT INTO films DEFAULT VALUES;
```
使用多行VALUES语法插入多行：
```SQL
INSERT INTO films (code, title, did, date_prod, kind) VALUES
    ('B6717', 'Tampopo', 110, '1985-02-10', 'Comedy'),
    ('HG120', 'The Dinner Game', 140, DEFAULT, 'Comedy');
```
从表tmp_films中插入几行到表 films中， 字段布局与films相同：
```SQL
INSERT INTO films SELECT * FROM tmp_films WHERE date_prod < '2004-05-07';
```
插入数组：
```SQL
-- 创建一个空的3x3游戏板来玩圈-和-叉游戏(这些查询创建相同的板属性)
INSERT INTO tictactoe (game, board[1:3][1:3])
    VALUES (1, '{{" "," "," "},{" "," "," "},{" "," "," "}}');
-- 上述例子中的下标并非真正必须
INSERT INTO tictactoe (game, board)
    VALUES (2, '{{X," "," "},{" ",O," "},{" ",X," "}}');
```
向distributors表插入一个单独的行，并返回由DEFAULT 子句生成的序列值：
```
INSERT INTO distributors (did, dname) VALUES (DEFAULT, 'XYZ Widgets')
   RETURNING did;
```
<!--增加销售人员为Acme Corporation的打折力度，并且在日志表中记录真个更新了的行和当前时间：
```SQL
WITH upd AS (
  UPDATE employees SET sales_count = sales_count + 1 WHERE id =
    (SELECT sales_person FROM accounts WHERE name = 'Acme Corporation')
    RETURNING *
)
INSERT INTO employees_log SELECT *, current_timestamp FROM upd;
```-->
<br/>**兼容性**

INSERT语句与 SQL 标准兼容。但RETURNING子句是 EdsSQL扩展，就像在INSERT中使用WITH。 同样，省略字段名列表，但是并非所有字段都从VALUES子句或者*query* 填充的这种用法是标准不允许的。

可能碰到的关于*query*子句特性的限制在 SELECT语句中有相关文档。