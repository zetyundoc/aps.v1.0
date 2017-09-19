# CREATE DATABASE

**命令**

```SQL
CREATE DATABASE name
    [ [ WITH ] [ OWNER [=] user_name ]
           [ TEMPLATE [=] template ]
           [ ENCODING [=] encoding ]
           [ LC_COLLATE [=] lc_collate ]
           [ LC_CTYPE [=] lc_ctype ]
           [ TABLESPACE [=] tablespace_name ]
           [ CONNECTION LIMIT [=] connlimit ] ]```

<br/>**描述**

CREATE DATABASE创建一个新EdsSQL数据库。

要创建一个数据库，你必须是一个超级用户或者有特殊的CREATEDB权限。 参阅CREATE USER。

缺省情况下新数据库将通过复制标准系统数据库template1来创建。 可以通过TEMPLATE name指定不同的模板。 尤其是，用TEMPLATE template0创建一个很纯净的、 只包括EdsSQL预定义的标准对象的数据库。 这个方法对于避免把任何已经加入到template1里的本地安装对象拷贝到新数据库是非常有用的。

<br/>**参数**

*name*：要创建的数据库名字。

*user_name*：数据库用户的名字，他将是新数据库的所有者，或者是使用DEFAULT选项来指定当前缺省用户(也就是执行命令的用户)。 要创建一个其他角色所有的数据库，你必须是那个角色的直接或间接的成员，或者是超级用户。

*template*：模板名，即从哪个模板创建新数据库，或者使用DEFAULT选项来指定缺省模板(template1)。 

*encoding*：创建新数据库使用的字符编码。 可以使用文本名字(例如'SQL_ASCII')、整数编号或是DEFAULT选项来指定(模版数据库的编码)。  额外的限制参见下文。 

*lc_collate*：用于新数据库的排序规则(LC_COLLATE)。 这影响到应用对字符串的排序顺序，例如：在有ORDER BY的查询中，以及用于文本列的索引的顺序。 在默认情况下，使用模板数据库的排序规则。 请看以下附加的限制。 

*lc_ctype*：用于新数据库的字符分类(LC_CTYPE)。 这影响字符的分类，例如： 小写、大写和数字。 默认情况下使用模板数据库的字符分类。 请看以下附加的限制。 

*tablespace_name*：和新数据库关联的表空间名字， 或者使用DEFAULT选项表示使用模版数据库的表空间。 这个表空间将成为在这个数据库里创建的对象的缺省表空间。 参阅CREATE TABLESPACE获取更多信息。 

*connlimit*：数据库可以接受多少并发的连接。-1(缺省)意味着没有限制。 

可选参数可以按任意顺序书写，而不仅仅是上面显示的顺序。

<br/>**注意**

CREATE DATABASE不能在事务块里面执行。

类似"could not initialize database directory"这样的错误，最有可能是因为数据目录的权限不够或者磁盘满之类的文件系统问题。

使用DROP DATABASE删除一个数据库。

程序createdb是这个命令的封装，使用更加方便。

尽管可以通过把某数据库名声明为模板而不是用template1，但是这(还)不是一个通用的"COPY DATABASE"功能。 主要的限制是在从模版复制的时候不允许有其它会话链接到模版数据库上。 如果在开始执行CREATE DATABASE的时候有其它会话正连接在模版数据库上，那么操作将会失败； 否则直到CREATE DATABASE完成之后，才允许对模板数据库建立新的会话连接。 

为一个新数据库指定的字符集编码必须兼容所选择的区域环境设置 (LC_COLLATE和LC_CTYPE)。 若区域设置为 C(或相当于POSIX)，那么所有的编码都允许， 但是对于其他的区域设置，只有一个编码能正常工作。 （然而，在Windows系统中， UTF-8编码可用于任何区域设置。） CREATE DATABASE将会允许超级用户 来指定SQL_ASCII编码而不考虑区域设置情况，这种做法已过时了，是不宜采用的，它会导致编码与不兼容区域设置的数据被存储在数据库中时字符串函数功能会不正常。

除了template0用作模板的时候，其他数据库的编码和区域设置必须匹配模板数据库。 这是因为其他数据库可能会包含不匹配指定编码的数据，或者可能包含排序顺序受LC_COLLATE和LC_CTYPE影响的索引。 复制这些数据会导致数据库被新设置破坏。 然而，template0公认是不包含任何会受到影响的数据或者索引的。

CONNECTION LIMIT选项只是近似地强制；如果两个新的连接几乎同时发起， 而只剩下一个连接"slot"了，那么很可能两个连接都失效。另外，超级用户连接时不受这个限制。

<br/>**示例**

创建一个新数据库:

```SQL
CREATE DATABASE lusiadas;```

创建一个由用户salesapp拥有的数据库sales，缺省表空间是salesspace：

```SQL
CREATE DATABASE sales OWNER salesapp TABLESPACE salesspace;```

创建一个数据库music，支持ISO-8859-1字符集：

```SQL
CREATE DATABASE music ENCODING 'LATIN1' TEMPLATE template0;```

在这个例子中，TEMPLATE template0选项只在template1的编码不是ISO-8859-1时被请求。 

<p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >注意：更改编码可能也会需要选择新的LC_COLLATE和LC_CTYPE设置。</p>


<br/>**兼容性**

在sql标准里头没有 CREATE DATABASE 语句。数据库等同于目录，其创建是交给具体的数据库实现去定义的。

<br/>**参见**

ALTER DATABASE, DROP DATABASE