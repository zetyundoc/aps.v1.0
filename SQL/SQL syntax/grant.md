# GRANT（企业版支持）
<br/>**命令**
```SQL
GRANT { { SELECT | INSERT | REFERENCES | TRIGGER }
    [, ...] | ALL [ PRIVILEGES ] }
    ON { [ TABLE ] table_name [, ...]
         | ALL TABLES IN SCHEMA schema_name [, ...] }
    TO { [ GROUP ] role_name | PUBLIC } [, ...] [ WITH GRANT OPTION ]

GRANT { { SELECT | INSERT | REFERENCES } ( column_name [, ...] )
    [, ...] | ALL [ PRIVILEGES ] ( column_name [, ...] ) }
    ON [ TABLE ] table_name [, ...]
    TO { [ GROUP ] role_name | PUBLIC } [, ...] [ WITH GRANT OPTION ]

GRANT { { USAGE | SELECT }
    [, ...] | ALL [ PRIVILEGES ] }
    ON { SEQUENCE sequence_name [, ...]
         | ALL SEQUENCES IN SCHEMA schema_name [, ...] }
    TO { [ GROUP ] role_name | PUBLIC } [, ...] [ WITH GRANT OPTION ]

GRANT { { CREATE | CONNECT | TEMPORARY | TEMP } [, ...] | ALL [ PRIVILEGES ] }
    ON DATABASE database_name [, ...]
    TO { [ GROUP ] role_name | PUBLIC } [, ...] [ WITH GRANT OPTION ]

GRANT { USAGE | ALL [ PRIVILEGES ] }
    ON DOMAIN domain_name [, ...]
    TO { [ GROUP ] role_name | PUBLIC } [, ...] [ WITH GRANT OPTION ]

GRANT { USAGE | ALL [ PRIVILEGES ] }
    ON FOREIGN DATA WRAPPER fdw_name [, ...]
    TO { [ GROUP ] role_name | PUBLIC } [, ...] [ WITH GRANT OPTION ]

GRANT { USAGE | ALL [ PRIVILEGES ] }
    ON FOREIGN SERVER server_name [, ...]
    TO { [ GROUP ] role_name | PUBLIC } [, ...] [ WITH GRANT OPTION ]

GRANT { EXECUTE | ALL [ PRIVILEGES ] }
    ON { FUNCTION function_name ( [ [ argmode ] [ arg_name ] arg_type [, ...] ] ) [, ...]
         | ALL FUNCTIONS IN SCHEMA schema_name [, ...] }
    TO { [ GROUP ] role_name | PUBLIC } [, ...] [ WITH GRANT OPTION ]

GRANT { USAGE | ALL [ PRIVILEGES ] }
    ON LANGUAGE lang_name [, ...]
    TO { [ GROUP ] role_name | PUBLIC } [, ...] [ WITH GRANT OPTION ]

GRANT {  SELECT [, ...] | ALL [ PRIVILEGES ] }
    ON LARGE OBJECT loid [, ...]
    TO { [ GROUP ] role_name | PUBLIC } [, ...] [ WITH GRANT OPTION ]

GRANT { { CREATE | USAGE } [, ...] | ALL [ PRIVILEGES ] }
    ON SCHEMA schema_name [, ...]
    TO { [ GROUP ] role_name | PUBLIC } [, ...] [ WITH GRANT OPTION ]

GRANT { CREATE | ALL [ PRIVILEGES ] }
    ON TABLESPACE tablespace_name [, ...]
    TO { [ GROUP ] role_name | PUBLIC } [, ...] [ WITH GRANT OPTION ]

GRANT { USAGE | ALL [ PRIVILEGES ] }
    ON TYPE type_name [, ...]
    TO { [ GROUP ] role_name | PUBLIC } [, ...] [ WITH GRANT OPTION ]

GRANT role_name [, ...] TO role_name [, ...] [ WITH ADMIN OPTION ]
```
<br/>**描述**

GRANT命令有两个基本变种：一个变种是给数据库对象 (表、字段、视图、外部表、序列、数据库、外部数据封装器、外部服务器、函数、过程语言、 模式、表空间)赋予权限；一个变种是赋予一个角色中的成员关系。这些变种在很多方面都非常类似， 但是它们之间的区别也有足够理由来分开描述。

<br/>**在数据库对象上的 GRANT**

这个变种的GRANT命令在数据库对象上给一个或多个角色授予特定的权限。 这些权限追加到已经授予的权限上。

也有一个选项赋予一个或多个模式内的相同类型的所有对象权限。这个功能当前只支持表， 序列和函数（但请注意ALL TABLES被认为包含视图和外部表）。

关键字PUBLIC表示该权限要赋予所有角色，包括那些以后可能创建的用户。 PUBLIC可以看做是一个隐含定义好的组，它总是包括所有角色。 任何特定的角色都将拥有直接赋予他/它的权限，加上他/它所处的任何组， 以及再加上赋予PUBLIC的权限的总和。

如果声明了WITH GRANT OPTION，那么权限的接收者也可以将此权限赋予他人， 否则就不能授权他人。这个选项不能赋予PUBLIC。

对于对象的所有者(通常就是创建者)而言，没有什么权限需要赋予，因为所有者缺省就持有所有权限。 不过，所有者出于安全考虑可以选择废弃一些他自己的权限。

删除一个对象的权力，或者是任意修改它的权力都不是可赋予的权限；它是创建者固有的， 并且不能赋予或撤销。（然而，一个类似的影响可以通过赋予或撤销拥有这个对象的角色的成员关系来获得； 参阅下文。）所有者也隐含地拥有该对象的所有授权选项。

EdsSQL给PUBLIC对象的某些类型赋予缺省的权限。在表、字段、 模式或表空间上缺省不会赋予PUBLIC权限。对于其他类型， 赋予PUBLIC以下缺省的权限：数据库为 CONNECT 和CREATE TEMP TABLE权限；函数为EXECUTE权限； 语言为USAGE权限。对象所有者当然可以REVOKE 缺省的或明确赋予的权限。出于最大安全性考虑，在创建该对象的同一个事务中发出 REVOKE就不会打开给别的用户使用该对象的窗口。同样， 这些缺省初始化权限设置可以使用ALTER DEFAULT PRIVILEGES命令改变。

可能的权限有：

SELECT：允许对声明的表、视图、序列SELECT任意字段或指定字段列表。 还允许做COPY的源。<!--这个权限也需要引用UPDATE 或DELETE现存的字段值。-->对于序列而言，这个权限还允许使用 currval函数。对于大对象，这个权限允许读对象。

INSERT：允许向声明的表INSERT一个新行。如果列出了指定的字段， 那么只有列出的字段被指派给INSERT命令（其他字段因此接受缺省的值）。 同时还允许做COPY。

<!--UPDATE：允许对声明的表中任意字段或指定的字段列表做UPDATE。 实际上，任何重要的UPDATE命令也需要SELECT权限， 因为必须引用表字段决定要更新哪个行，和/或为字段计算新值。 SELECT ... FOR UPDATE和SELECT ... FOR SHARE 也至少在一个字段上要求这个权限，除了SELECT权限之外。比如， 这个权限允许使用nextval和setval函数。 对于大对象，这个权限允许写或截断对象。-->

<!--DELETE：允许从声明的表中DELETE行。实际上，任何重要的DELETE 命令也需要SELECT权限，因为必须引用表字段决定要删除哪个行。-->

TRUNCATE：允许在指定的表上TRUNCATE。

REFERENCES：要创建一个外键约束，你必须在参考字段和被参考字段上都拥有这个权限。 该权限可能赋予了表的所有字段，或只是赋予了指定的字段。

TRIGGER：允许在声明表上创建触发器(参见CREATE TRIGGER语句)。

CREATE：

对于数据库，允许在该数据库里创建新的模式。<br/>
对于模式，允许在该模式中创建新的对象。要重命名一个现有对象， 你必需拥有该对象并且对包含该对象的模式拥有这个权限。<br/>
对于表空间，允许在其中创建表，索引和临时文件，以及允许创建数据库的时候把该表空间指定为其缺省表空间。 请注意，撤销这个权限不会改变现有对象的存放位置。

CONNECT：允许用户连接到指定的数据库。该权限将在连接启动时检查 (除了检查pg_hba.conf中的任何限制之外)。

TEMPORARY<br/>
TEMP：

允许在使用指定数据库的时候创建临时表

EXECUTE：允许使用指定的函数并且可以使用任何利用这些函数实现的操作符。 这是适用于函数的唯一权限。该语法同样适用于聚合函数。

USAGE
对于过程语言，允许使用指定过程语言创建该语言的函数。这是适用于过程语言的唯一权限。<br/>
对于模式，允许访问包含在指定模式中的对象(假设该对象的所有权要求同样也设置了)。<br/> 最终这些就允许了权限接受者"查询"模式中的对象。没有这个权限仍然可以看见这些对象的名字 (比如通过查询系统视图)。同样，撤销该权限之后，现有的后端可能有在查找之前就执行了的语句， 因此这不是一个很安全的限制对象访问的方法。<br/>

对于序列，该权限允许使用currval和nextval函数。<br/>

对于类型和域，该权限允许在创建表、函数和其他模式对象时使用类型或域。 （请注意，它不控制类型的一般"使用"，例如显示在查询中的该类型的值。 它只防止依赖于该类型的对象被创建。该权限的主要目的是控制哪个用户在类型上创建依赖， 该依赖会防止用户稍后改变类型。）<br/>

对于外部数据封装器，该权限使权限接受者能够使用那个外部数据封装器创建新的服务器。<br/>

对于服务器，该权限使权限接受者能够使用该服务器创建外部表，并且也能创建、 修改或删除他自己的用户的与该服务器相关的用户映射。<br/>

ALL PRIVILEGES：一次性给予所有可以赋予的权限。PRIVILEGES关键字在 EdsSQL里是可选的，但是严格的 SQL 要求有这个关键字。

其它命令要求的权限都在相应的命令的参考页上列出。

<br/>**角色上的 GRANT**

这个变种的GRANT命令把一个角色的成员关系赋予一个或多个其它角色。 角色里的成员关系很重要，因为它会将赋予该角色的权限传播给所有该角色的成员。

如果声明了WITH ADMIN OPTION，那么该成员随后就可以将角色的成员关系赋予其它角色， 以及撤销其它角色的成员关系。如果没有 admin 选项，普通用户就不能这么做。不过， 数据库超级用户可以给任何人赋与或者撤销任何角色的任何成员关系。拥有CREATEROLE 权限的角色可以赋予或者撤销任何非超级用户角色的成员关系。

与权限不同，角色的成员关系不能被赋予PUBLIC。需要注意的是， 这种形式的命令不允许使用无意义的GROUP关键字。

<br/>**注意**

REVOKE命令用于删除访问权限。

自PostgreSQL 8.1以来，用户和组的概念统一为角色。 因此不再需要使用关键字GROUP来确定接受者是一个用户还是一个组。 仍然允许在命令中出现GROUP，但这是一个噪声字。

一个用户可以他有相应权限的字段上执行SELECT, INSERT等，该权限是在指定字段或整个表上。 在表级别授予权限然后从一个字段上撤销该权限将不会做你希望发生的事情：表级别的授权不受字段级别操作的影响。

如果非对象所有者企图在对象上GRANT权限，而该用户没有该对象上指定的权限， 那么命令将立即失败。只要有某些可用的权限，该命令就会继续，但是它只授予那些该用户有授权选项的权限。 如果没有可用的授权选项，那么GRANT ALL PRIVILEGES形式将发出一个警告消息， 其它命令形式将发出在命令中提到的、但是没有授权选项的那些权限相关的警告消息。 这些语句原则上也适用于对象所有者，但是因为所有者总是被认为拥有所有授权选项， 所以这种情况永远不会发生在所有者身上。

要注意数据库超级用户可以访问所有对象，而不会受对象的权限设置影响。 这个特点类似 Unix 系统的root的权限。和root一样，除了必要的情况， 总是以超级用户身份进行操作是不明智的做法。

如果一个超级用户选择发出一个GRANT或REVOKE命令， 那么这条命令将是以被影响对象的所有者的形式执行的。特别是， 通过这种方法赋与的权限将显得好像是由对象所有者赋与的。对于角色成员关系， 成员关系的赋与就会像是通过包含角色自己赋与的一样。

GRANT和REVOKE也可以不由被影响对象的所有者来执行， 而是由拥有该对象的角色的一个成员来执行，或者是一个在该对象上持有 WITH GRANT OPTION权限的角色的成员。在这种情况下， 该权限将被纪录为是由实际拥有该对象或者持有WITH GRANT OPTION 权限的对象赋与的。比如，如果表t1被角色g1拥有， 并且u1是g1的一个成员，然后u1可以把t1 的权限赋予u2，但是这些权限将表现为是由g1直接赋予的。 任何g1角色的成员都可以在之后撤销这些权限。

如果执行GRANT的角色所持有的所需权限是通过角色成员关系间接获得的， 那么究竟是那个角色将被纪录为赋予权限的角色就是未知的。在这种情况下， 最好的方法是使用SET ROLE成为你想执行GRANT命令的指定角色。

在表上赋予的权限不会自动传播到该表使用的序列上，包括SERIAL字段上的序列。 必须单独设置序列的权限。

使用psql的\dp 命令获取表和字段的现存权限的有关的信息。例如：
```SQL
=> \dp mytable
                              Access privileges
 Schema |  Name   | Type  |   Access privileges   | Column access privileges 
--------+---------+-------+-----------------------+--------------------------
 public | mytable | table | miriam=arwdDxt/miriam | col1:
                          : =r/miriam             :   miriam_rw=rw/miriam
                          : admin=arw/miriam        
(1 row)
```
\dp显示的条目解释如下：
```SQL
rolename=xxxx -- 赋予一个角色的权限
        =xxxx -- 赋予 PUBLIC 的权限

            r -- SELECT ("读")
            a -- INSERT ("追加")
            D -- TRUNCATE
            x -- REFERENCES
            t -- TRIGGER
            X -- EXECUTE
            U -- USAGE
            C -- CREATE
            c -- CONNECT
            T -- TEMPORARY
      arwdDxt -- ALL PRIVILEGES (用于表，用于其他对象时不同)
            * -- 给前面权限的授权选项

        /yyyy -- 授出这个权限的角色
```
用户miriam在建完mytable表之后再做下面的语句，就可以得到上面例子的结果：
```SQL
GRANT SELECT ON mytable TO PUBLIC;
GRANT SELECT,INSERT ON mytable TO admin;
GRANT SELECT (col1) ON mytable TO miriam_rw;
```
对于非表的对象，可以使用\d命令显示他们的权限。

如果一个给定的对象的"Access privileges"字段是空的，这意味着该对象有缺省权限 (也就是说，它的权限字段是 NULL)。缺省权限总是包括所有者的所有权限，以及根据对象的不同， 可能包含一些给PUBLIC的权限。对象上第一个GRANT或REVOKE 将实例化这个缺省权限(比如，产生 {miriam=arwdDxt/miriam}) 然后根据每次特定的需求修改它。 相似的，"Column access privileges"中的记录只为非缺省权限字段。（请注意：为了这个目的， "default privileges"总是意味着该对象类型的内建缺省权限。受ALTER DEFAULT PRIVILEGES 命令影响权限的对象总是和一个包括ALTER影响的明确权限记录一起显示。）

请注意所有者的隐含授权选项没有在显示出来的访问权限里标记出来。 只有在授权选项明确地授予某人之后，才会显示一个*。

<br/>**示例**

把表films的插入权限赋予所有用户：
```SQL
GRANT INSERT ON films TO PUBLIC;
```
赋予用户manuel对视图kinds的所有权限：
```SQL
GRANT ALL PRIVILEGES ON kinds TO manuel;
```
请注意，如果上面的命令由超级用户或者kinds的所有者执行，那么它实际上会赋予所有权限， 如果由其他人执行，那么它会赋予这个"其他人"拥有授权选项的所有权限。

把角色admins的成员关系赋与用户joe：
```SQL
GRANT admins TO joe;
```
<br/>**兼容性**

根据 SQL 标准，在ALL PRIVILEGES里的PRIVILEGES关键字是必须的。 SQL 标准不支持在一条命令里对多个表设置权限。

EdsSQL允许一个对象所有者撤销它自己的普通权限：比如， 一个表所有者可以让自己对这个表是只读的，方法是撤销自己的INSERT<!--, UPDATE, DELETE, 和 -->TRUNCATE权限。根据 SQL 标准， 这是不可能的。原因是EdsSQL把所有者的权限当作由所有者给自己赋予的； 因此也可以撤销他们。在 SQL 标准里，所有者的权限是假设为"_SYSTEM"实体赋予的。 因为所有者不是"_SYSTEM"，所以他不能撤销这些权限。

根据 SQL 标准，授权选项可以授予PUBLIC；EdsSQL 只支持授予授权选项给角色。

SQL 标准对其它类型的对象提供了一个USAGE权限：字符集、排序规则、转换。

在SQL标准中，序列只有一个USAGE权限，该权限控制NEXT VALUE FOR 表达式的使用，该表达式相当于EdsSQL中的nextval函数。序列权限 SELECT和UPDATE是EdsSQL的扩展。序列USAGE 权限应用到currval函数也是一个EdsSQL扩展（就像函数本身）。

在数据库、表空间、模式、语言上的权限是EdsSQL扩展。

<br/>**参见**

REVOKE<!--, ALTER DEFAULT PRIVILEGES-->