# FETCH（企业版支持）
<br/>**命令**
```SQL
FETCH [ direction [ FROM | IN ] ] cursor_name

这里的 direction 可以为空或下列之一:

    NEXT
    PRIOR
    FIRST
    LAST
    ABSOLUTE count
    RELATIVE count
    count
    ALL
    FORWARD
    FORWARD count
    FORWARD ALL
    BACKWARD
    BACKWARD count
    BACKWARD ALL
```
<br/>**描述**

FETCH使用游标检索行。

一个游标有一个由FETCH使用的相关联的位置。游标的位置可以在查询结果的第一行之前， 或者在结果中的任意行，或者在结果的最后一行之后。刚创建完之后，游标是放在第一行之前的。 在抓取了一些行之后，游标放在检索到的最后一行上。如果FETCH抓完了所有可用行， 那么它就停在最后一行后面，或者在反向抓取的情况下是停在第一行前面。FETCH ALL 或FETCH BACKWARD ALL将总是把游标的位置放在最后一行或者在第一行前面。

NEXT, PRIOR, FIRST, LAST, ABSOLUTE, RELATIVE形式在恰当地移动游标之后抓取一个行。 如果没有数据行了，那么返回一个空的结果，此时游标就会停在查询结果的最后一行之后或者第一行之前。

FORWARD和BACKWARD形式在向前或者向后移动的过程中抓取指定的行数， 然后把游标定位在最后返回的行上；或者是，如果count 大于可用的行数，则在所有行之前或之后。

RELATIVE 0, FORWARD 0, BACKWARD 0 都要求在不移动游标的前提下抓取当前行，也就是重新抓取最近刚刚抓取过的行。 除非游标定位在第一行之前或者最后一行之后，这个动作都应该成功， 而在那两种情况下，不返回任何行。

<p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >注意: 本页描述在SQL命令级别的游标的使用。如果你尝试在PL/edsSQL 里使用游标，那么规则是不同的。</p>

<<br/>**参数**

*direction*：定义抓取的方向和抓取的行数。 它可以是下述之一：

<ul><li>NEXT：抓取下一行(缺省)。</li>

<li>PRIOR：抓取前面一行。</li>

<li>FIRST：抓取查询的第一行(和ABSOLUTE 1相同)。</li>

<li>LAST：抓取查询的最后一行(和ABSOLUTE -1相同)。</li>

<li>ABSOLUTE count：抓取查询中第count行，或者如果 count 为负就从查询结果末尾抓取第 abs(count)行。 如果count超出了范围， 那么定位在第一行之前和最后一行之后的位置；特别是ABSOLUTE 0定位在第一行之前。</li>

<li>RELATIVE count：抓取随后的第count行，或者如果 count为负就抓取前面的第 abs(count)行。</li> 如果有数据的话，RELATIVE 0重新抓取当前行。</li>

<li>count：抓取随后的count行(和 FORWARD count一样)。</li>

<li>ALL：抓取所有剩余的行(和FORWARD ALL一样)。</li>

<li>FORWARD：抓取下一行(和NEXT一样)。</li>

<li>FORWARD count：抓取随后的count行。 FORWARD 0重新抓取当前行。</li>

<li>FORWARD ALL：抓取所有剩余行。</li>

<li>BACKWARD：抓取前面一行(和PRIOR一样)。</li>

<li>BACKWARD count：抓取前面的count 行(向后扫描)。 BACKWARD 0重新抓取当前行。</li>

<li>BACKWARD ALL：抓取所有前面的行(向后扫描)。</li></ul>

*count*：count可能是一个有符号的整数常量， 决定要抓取的行数和方向。对于FORWARD和BACKWARD的情况， 声明一个带负号的count等效于改变 FORWARD和BACKWARD的方向。

*cursor_name*：一个打开的游标的名称。

<br/>**输出**

成功完成时，一个FETCH命令返回一个形如下面的标记。

FETCH *count*：这里的count是抓取的行数(可能是零)。 请注意在psql里，命令标签实际上不会显示， 因为psql用抓取的行数代替了。

<br/>**注意**

如果你想使用FETCH NEXT之外的任何FETCH变种，或者是带负数计数的 FETCH FORWARD，那么定义游标的时候应该带上SCROLL选项。 对于简单的查询，EdsSQL会允许那些没有带SCROLL 选项定义的游标也可以反向抓取，但是最好不要依赖这个行为。如果游标定义了 NO SCROLL，那么不允许反向抓取。

ABSOLUTE抓取不会比用相对位移移动到需要的数据行更快： 因为下层的实现必须遍历所有中间的行。负数的绝对抓取甚至更糟糕： 查询必须一直读到结尾才能找到最后一行，然后从那里开始反向遍历。不过， 回退到查询开头(比如FETCH ABSOLUTE 0)很快。

使用DECLARE语句定义一个游标。MOVE 语句改变游标位置而不检索数据。

<br/>**示例**

下面的例子用一个游标跨过一个表。
```SQL
BEGIN WORK;

-- 建立一个游标:

DECLARE liahona SCROLL CURSOR FOR SELECT * FROM films;

-- 抓取头5行到游标liahona里:

FETCH FORWARD 5 FROM liahona;

 code  |          title          | did | date_prod  |   kind   |  len
-------+-------------------------+-----+------------+----------+-------
 BL101 | The Third Man           | 101 | 1949-12-23 | Drama    | 01:44
 BL102 | The African Queen       | 101 | 1951-08-11 | Romantic | 01:43
 JL201 | Une Femme est une Femme | 102 | 1961-03-12 | Romantic | 01:25
 P_301 | Vertigo                 | 103 | 1958-11-14 | Action   | 02:08
 P_302 | Becket                  | 103 | 1964-02-03 | Drama    | 02:28

-- 抓取前面行:

FETCH PRIOR FROM liahona;

 code  |  title  | did | date_prod  |  kind  |  len
-------+---------+-----+------------+--------+-------
 P_301 | Vertigo | 103 | 1958-11-14 | Action | 02:08

-- 关闭游标并提交事务:

CLOSE liahona;
COMMIT WORK;
```
<br/>**兼容性**

SQL 标准定义的FETCH只用于嵌入式环境下。这里描述的 FETCH变种是把结果数据像SELECT结果那样返回， 而不是把它放在属主变量里。除了这点之外，FETCH 和 SQL 标准完全向前兼容。

涉及FORWARD和BACKWARD的FETCH形式， 包括FETCH *count*和 FETCH ALL形式(此时FORWARD是隐含的)， 是EdsSQL的扩展。

SQL 标准只允许游标前面有FROM，可选的IN，或者把它们都省去，是一个扩展。

<br/>**参见**

CLOSE, DECLARE<!--, MOVE-->