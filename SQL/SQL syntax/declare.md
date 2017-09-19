# DECLARE（企业版支持）
<br/>**命令**
```SQL
DECLARE name [ BINARY ] [ INSENSITIVE ] [ [ NO ] SCROLL ]
    CURSOR [ { WITH | WITHOUT } HOLD ] FOR query
```
<br/>**描述**

DECLARE允许用户创建游标，用于在一个大的查询里面检索少数几行数据。 在游标建立后，使用FETCH可以从中取出行。

 <p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >注意: 本页描述游标在SQL命令级别的使用。如果你试图在一个PL/edsSQL函数里面使用游标， 结果往往是不同的。</p>


<br/>**参数**

*name*：将要创建的游标名。

BINARY：令游标以二进制而不是文本格式返回数据。

INSENSITIVE：表明从游标检索出来的数据不应该被发生在游标创建后的游标的更新动作影响。 在EdsSQL里，这是缺省行为；这个关键字没有什么作用， 只在为了和 SQL 标准兼容的时候接受它。

SCROLL<br/>
NO SCROLL

SCROLL声明该游标可以用于以倒序的方式检索数据行(也就是反向检索)。 根据查询的执行计划的不同，声明SCROLL可能会对查询的执行时间有不良影响。 NO SCROLL声明该游标不能用于以倒序的方式检索数据行。 缺省仅允许在某些情况下倒序检索，这不同于指定SCROLL。 参见注意获取细节。

WITH HOLD<br/>
WITHOUT HOLD

WITH HOLD(缺省)声明该游标可以在创建它的事务成功提交后继续使用。 WITHOUT HOLD声明该游标不能在创建它的事务之外使用。

*query*：一个SELECT或VALUES命令，它提供游标返回的行。

BINARY, INSENSITIVE, SCROLL 关键字可以以任何顺序出现。

<br/>**注意**

通常游标和SELECT一样返回文本格式。BINARY 选项声明游标应该返回二进制格式的数据。这样就减少了服务器和客户端的转化工作， 花费程序员更多的工作解决平台依赖的二进制数据格式问题。 比如，如果查询从某个整数列返回 1 ，在缺省的游标里将获得一个字符串1， 但在二进制游标里将得到一个 4 字节的包含该数值内部形式的数值(大端顺序)。

应该小心使用二进制游标。一些客户端应用(比如psql) 是不能识别二进制游标的，它们期望返回的数据是文本格式。

 <p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >注意: 如果客户端应用使用"扩展查询"协议发出FETCH命令， 那么 Bind 协议声明数据是用文本还是用二进制格式检索。这个选择覆盖游标的定义。 因此，在使用扩展查询协议的时候，二进制游标的概念已经过时了， 任何游标都可以当作文本或者二进制的格式发出。</p>

如果没有声明WITH HOLD，那么这个命令创建的游标只能在当前事务中使用。 这样，不带WITH HOLD的DECLARE在事务块外面没有任何用处： 游标将一直存活到事务结束。因此，EdsSQL 将在这样的命令出现在事务块外面的时候报错。使用BEGIN, COMMIT和ROLLBACK定义一个事务块。

如果声明了WITH HOLD并且创建该游标的事务成功提交， 那么游标还可以在同一会话随后的事务里访问。但如果创建它的事务回滚， 那么游标被删除。带WITH HOLD创建的游标是用一个明确的 CLOSE命令或者是会话终止来关闭的。在目前的实现里， 由一个游标代表的行是被拷贝到一个临时文件或者内存区里的， 这样他们就仍然可以在随后的事务中被访问。

当查询包含<!--FOR UPDATE或-->FOR SHARE的时候， 不能指定WITH HOLD。

在定义一个要用来反向抓取的游标的时候，应该声明SCROLL选项， 这是 SQL 标准要求的。不过，为了和早期的版本兼容， 只要游标的查询计划简单得不需要额外的开销，EdsSQL 在没有声明SCROLL的时候也允许反向抓取。不过， 建议应用开发人员不要依赖于使用没有使用SCROLL 定义的游标的反向查找功能。如果声明了NO SCROLL ， 那么不管怎样都会禁止反向抓取的功能。

当查询包含<!--FOR UPDATE或-->FOR SHARE时也不允许反向抓取； 因此在这种情况下不能声明SCROLL。

 <p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >注意: 如果调用任何不稳定的函数，那么可以回滚并且WITH HOLD 游标可能给出意外的结果（参阅xref linkend="xfunc-volatility">）。 当重新抓取到以前抓取到的行时，函数可能会重新执行，可能导致结果与之前的不同。 一个绕开这种情况的方法是声明WITH HOLD游标， 并且在从它读取任何行之前提交事务。这将强制游标的整个输出在临时存储中物化， 这样不稳定的函数精确的每行只执行一次。</p>

如果游标的查询<!--包括FOR UPDATE或-->FOR SHARE， 那么返回的行在它们第一次被抓取的时候锁定，和有这个选项的SELECT 一样。另外，返回行将是最新的版本；因此提供这些选项相当于SQL标准调用一个 "敏感的游标"。（和<!--FOR UPDATE或 -->FOR SHARE一起指定INSENSITIVE是错误的。）

<!--<p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >注意: 如果游标定义为使用UPDATE ... WHERE CURRENT OF或 DELETE ... WHERE CURRENT OF，通常推荐使用FOR UPDATE。 使用FOR UPDATE阻止其他会话在抓取和更新之间改变行。没有FOR UPDATE， 如果在创建游标后改变了行，那么随后的WHERE CURRENT OF命令将没有作用。<br/><br/>
使用FOR UPDATE的另外一个原因是，没有它，如果游标查询不符合SQL标准的 "简单可更新"原则，那么随后的WHERE CURRENT OF可能会失败 （尤其是，游标必须只引用一个表，并且不使用分组或ORDER BY）。 不是简单可更新的游标可能会也可能不会工作，取决于计划选择细节；所以在最坏的情况下， 应用可能在测试中工作而在生产中失败。<br/><br/>
不和WHERE CURRENT OF一起使用FOR UPDATE的主要原因是， 你需要游标是可回滚的，或对随后的更新不敏感的（也就是说，持续显示旧的数据）。 如果需要这样，请注意上面显示的注意事项。</p>-->

SQL 标准中的游标只能在嵌入SQL(ESQL)的应用中使用。 EdsSQL服务器没有一个明确的OPEN 语句；一个游标被认为在定义时就已经打开了。不过，EdsSQL 嵌入的 SQL 预处理器(ECPG)支持 SQL 标准的习惯， 包括那些和DECLARE和OPEN相关的语句。

可以通过查询eds_cursors 系统视图看到所有可用游标。

<br/>**示例**

定义一个游标：
``` SQL
DECLARE liahona CURSOR FOR SELECT * FROM films;
```
参阅FETCH获取有关游标使用的更多例子。

<br/>**兼容性**

SQL标准说它是依赖于实现的，不管游标对缺省的底层数据的当前更新是否敏感。 <!--在EdsSQL中，游标缺省是敏感的，并且可以通过声明 FOR UPDATE使其敏感。其他产品工作可能不同。-->

SQL 标准只允许在嵌入的SQL中和模块中使用游标。 EdsSQL允许交互地使用游标。

二进制游标是EdsSQL扩展。

<br/>**参见**

CLOSE<!--, FETCH, MOVE-->