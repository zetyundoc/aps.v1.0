# CREATE TABLE（企业版支持）
<br/>**命令**
```SQL
CREATE [ [ GLOBAL | LOCAL ] { TEMPORARY | TEMP } | UNLOGGED ] TABLE [ IF NOT EXISTS ] table_name ( [
  { column_name data_type [ COLLATE collation ] [ column_constraint [ ... ] ]
    | table_constraint
    | LIKE source_table [ like_option ... ] }
    [, ... ]
] )
[ INHERITS ( parent_table [, ... ] ) ]
[ WITH ( storage_parameter [= value] [, ... ] ) | WITH OIDS | WITHOUT OIDS ]
[ ON COMMIT { PRESERVE ROWS | DROP } ]
[ TABLESPACE tablespace_name ]

CREATE [ [ GLOBAL | LOCAL ] { TEMPORARY | TEMP } | UNLOGGED ] TABLE [ IF NOT EXISTS ] table_name
    OF type_name [ (
  { column_name WITH OPTIONS [ column_constraint [ ... ] ]
    | table_constraint }
    [, ... ]
) ]
[ WITH ( storage_parameter [= value] [, ... ] ) | WITH OIDS | WITHOUT OIDS ]
[ ON COMMIT { PRESERVE ROWS | DROP } ]
[ TABLESPACE tablespace_name ]

 这里的column_constraint是:

[ CONSTRAINT constraint_name ]
{ NOT NULL |
  NULL |
  CHECK ( expression ) [ NO INHERIT ] |
  DEFAULT default_expr |
  UNIQUE index_parameters |
  PRIMARY KEY index_parameters |
  REFERENCES reftable [ ( refcolumn ) ] [ MATCH FULL | MATCH PARTIAL | MATCH SIMPLE ]}
[ DEFERRABLE | NOT DEFERRABLE ] [ INITIALLY DEFERRED | INITIALLY IMMEDIATE ]

 而table_constraint是:

[ CONSTRAINT constraint_name ]
{ CHECK ( expression ) [ NO INHERIT ] |
  UNIQUE ( column_name [, ... ] ) index_parameters |
  PRIMARY KEY ( column_name [, ... ] ) index_parameters |
  EXCLUDE [ USING index_method ] ( exclude_element WITH operator [, ... ] ) index_parameters [ WHERE ( predicate ) ] |
  FOREIGN KEY ( column_name [, ... ] ) REFERENCES reftable [ ( refcolumn [, ... ] ) ]
    [ MATCH FULL | MATCH PARTIAL | MATCH SIMPLE ] }
[ DEFERRABLE | NOT DEFERRABLE ] [ INITIALLY DEFERRED | INITIALLY IMMEDIATE ]

 like_option是:

{ INCLUDING | EXCLUDING } { DEFAULTS | CONSTRAINTS | INDEXES | STORAGE | COMMENTS | ALL }

 在UNIQUE、PRIMARY KEY和
 EXCLUDE约束中的index_parameters是:

[ WITH ( storage_parameter [= value] [, ... ] ) ]
[ USING INDEX TABLESPACE tablespace_name ]

exclude_element in an EXCLUDE constraint is:

{ column_name | ( expression ) } [ opclass ] [ ASC | DESC ] [ NULLS { FIRST | LAST } ]
```
<br/>**描述**

CREATE TABLE在当前数据库创建一个新的空白表。 该表将由发出此命令的用户所拥有。

如果给出了模式名(比如CREATE TABLE myschema.mytable ...)， 那么在指定的模式中创建表，否则在当前模式中创建。临时表存在于一个特殊的模式里， 因此创建临时表的时候不能指定模式名。表名字必需在同一模式中的其它表、 序列、索引、视图或外部表名字中唯一。

CREATE TABLE还自动创建一个与该表的行对应的复合数据类型。 因此，表不能和同模式中的现有数据类型同名。

可选的约束子句声明约束，新行或者更新的行必须满足这些约束才能成功插入或更新。 约束是一个 SQL 对象，它以多种方式协助在表上定义有效数值的集合。

定义约束有两种方法：表约束和列约束。列约束是作为一个列定义的一部分定义的。 而表约束并不和某个列绑在一起，它可以作用于多个列上。每个列约束也可以写成表约束； 如果某个约束只影响一个列，那么列约束只是符号上的简洁方式而已。

要能创建一个表，你必须分别在所有列类型或OF 子句的类型上有USAGE权限。

<br/>**参数**

**TEMPORARY 或 TEMP**：如果声明了，则创建为临时表。临时表在会话结束或(可选)当前事务的结尾 (参阅下面的ON COMMIT)自动删除。除非用模式修饰的名字引用， 否则现有的同名永久表在临时表存在期间，在本会话过程中是不可见的。 任何在临时表上创建的索引也都会被自动删除。

自动清理进程不能访问，因此不能清理或分析临时表。 由于这个原因，适当的清理和分析操作应该通过会话SQL命令来执行。例如， 如果一个临时表用于复杂查询中，那么在填充临时表之后在临时表上运行 ANALYZE是明智的。

可以选择在TEMPORARY或TEMP前面放上GLOBAL 或LOCAL。不过这目前对EdsSQL来说没有任何区别， 并且已经废弃了。

**UNLOGGED**：如果指定了，则表作为非日志表来创建。写入到非日志表中的数据并不写入预写式日志，这使得他们比普通表快的多。不过， 他们不是崩溃安全的：一个非日志表在崩溃或不清理关机之后被自动截断。 非日志表的内容也不复制到备用服务器。任何在非日志表上创建的索引也自动是非日志的。

**IF NOT EXISTS**：如果同名的关系已经存在，那么不要抛出一个错误。在这种情况下发出一个通知。 请注意，不保证已经存在的关系和要创建的关系相像。

*table_name*：要创建的表的名字(可以用模式修饰)

*OF type_name*：创建一个类型化的表，它的结构来自指定的复合类型 （名字可以有模式修饰）。一个类型化的表绑定到它的类型； 例如，如果类型被删除（使用DROP TYPE ... CASCADE）， 则该表也将被删除。

当类型化的表被创建时，字段的数据类型取决于底层复合类型， 而不是通过CREATE TABLE命令指定。 但是CREATE TABLE命令可以给表添加缺省和约束， 并且可以指定存储参数。

*column_name*：在新表中要创建的字段名字。

*data_type*：该字段的数据类型。它可以包括数组说明符。

COLLATE *collation*：COLLATE给字段（必须是可排序的数据类型）分配一个排序规则。 如果没有指定，则使用字段数据类型的缺省排序。

INHERITS ( *parent_table* [, ... ] )

可选的INHERITS子句声明一系列的表，新表自动从这一系列表中继承所有字段。

使用INHERITS将在新的子表和其父表之间创建一个永久的关系。 对父表结构的修改通常也会传播到子表。缺省时，扫描父表的时候也会扫描子表。

如果在多个父表中存在同名字段，那么就会报告一个错误， 除非这些字段的数据类型在每个父表里都是匹配的。如果没有冲突， 那么重复的字段在新表中融合成一个字段。如果列出的新表字段名和继承字段同名， 那么它的数据类型也必须和继承字段匹配，并且这些字段定义会融合成一个。 如果新表为该字段明确声明了缺省值，那么此缺省值将覆盖任何继承字段的缺省值。 否则，该字段的所有父字段缺省值都必须相同，否则就会报错。

CHECK约束本质上以和字段一样的方式合并：如果多个父表和/或新表的定义包含相同名字的 CHECK约束，那么这些约束必须都有相同的检查表达式，否则会报告一个错误。 有相同名字和表达式的约束将合并到一个。在父表中标记为NO INHERIT 的约束将不被考虑。请注意，新表中未命名的CHECK约束将永远不被合并， 因为它将永远都有一个唯一的名字。

字段STORAGE设置也从父表中拷贝。

LIKE *source_table* [ *like_option* ... ]

LIKE子句声明一个表， 新表自动从这个表里面继承所有字段名及其数据类型和非空约束。

和INHERITS不同，新表与原来的表之间在创建动作完毕之后是完全无关的。 在源表做的任何修改都不会传播到新表中，并且也不可能在扫描源表的时候包含新表的数据。

字段缺省表达式只有在声明了INCLUDING DEFAULTS之后才会包含进来。 缺省是不包含缺省表达式的，结果是新表中所有字段的缺省值都是 NULL 。

非空约束将总是复制到新表中，CHECK约束则仅在指定了 INCLUDING CONSTRAINTS的时候才复制。源表上的索引、 PRIMARY KEY和UNIQUE约束仅在指定了 INCLUDING INDEXES子句的时候才在新表上创建。 此规则同时适用于表约束和列约束。

拷贝字段定义的STORAGE设置只在声明了INCLUDING STORAGE 的时候才拷贝。缺省是不包括STORAGE设置的， 结果是新表中的拷贝字段有特定类型的缺省设置。

拷贝字段、约束和索引的注释只在声明了INCLUDING COMMENTS 的时候拷贝。缺省是不包含注释的，结果是新表中拷贝的字段和约束没有注释。

INCLUDING ALL是INCLUDING DEFAULTS INCLUDING CONSTRAINTS INCLUDING INDEXES INCLUDING STORAGE INCLUDING COMMENTS 的一个简写形式。

请注意，和INHERITS不同，通过LIKE 拷贝的列和约束并不使用相同的名字进行融合。 如果明确的指定了相同的名字或者在另外一个LIKE子句中，那么将会报错。

LIKE子句也可以用来从视图、外表或复合类型中拷贝字段。 不适用的选项（如，从一个视图中INCLUDING INDEXES）都被忽略。

CONSTRAINT *constraint_name*

可选的列约束或表约束的名字。如果约束本身是非法的，那么其名字将会出现在错误消息中， 因此像col must be positive这样的名字可以表达有用的约束信息。 如果约束名中含有空格则必须用双引号界定。如果没有指定约束名，那么系统将会自动生成一个名字。

**NOT NULL**：字段不允许包含 NULL 值

**NULL**：字段允许包含 NULL 值，这是缺省。

这个子句的存在只是为和那些非标准 SQL 数据库兼容。不建议在新应用中使用它。

CHECK ( *expression* ) [ NO INHERIT ]：CHECK约束声明一个生成布尔结果的表达式， 每次将要插入的新行或者将要被更新的行必须使表达式结果为真或未知才能成功， 否则会抛出一个异常并且不会修改数据库。 声明为字段约束的检查约束应该只引用该字段的数值， 而在表约束里出现的表达式可以引用多个字段。

目前，CHECK表达式不能包含子查询也不能引用除当前行字段之外的变量。

用NO INHERIT标记的约束将不会传递到子表中去。

DEFAULT *default_expr*：DEFAULT子句给字段指定缺省值。该数值可以是任何不含变量的表达式 (不允许使用子查询和对本表中的其它字段的交叉引用)。 缺省表达式的数据类型必须和字段类型匹配。

缺省表达式将被用于任何未声明该字段数值的插入操作。 如果没有指定缺省值则缺省值为 NULL 。

UNIQUE (column constraint)
UNIQUE ( column_name [, ... ] ) (table constraint)
UNIQUE约束表示表里的一个或多个字段的组合必须在全表范围内唯一。 唯一约束的行为和列约束一样，只不过多了跨多行的能力。

对于唯一约束而言，NULL 被认为是互不相等的。

每个唯一约束都必须给其使用的字段集合赋予一个与其它唯一约束都不同的名字， 并且也不能和主键约束的名字相同，否则就被认为是同样的约束写了两次。

PRIMARY KEY (column constraint)
PRIMARY KEY ( column_name [, ... ] ) (table constraint)
主键约束表明表中的一个或者一些字段只能包含唯一(不重复)的非 NULL 值。 从技术上讲，PRIMARY KEY只是UNIQUE 和NOT NULL的组合，不过把一套字段标识为主键同时也体现了模式设计的元数据， 因为主键意味着可以拿这套字段用做行的唯一标识。

一个表只能声明一个主键，不管是作为字段约束还是表约束。

主键约束使用的字段集合应该与其它唯一约束都不同。

EXCLUDE [ USING index_method ] ( exclude_element WITH operator [, ... ] ) index_parameters [ WHERE ( predicate ) ]
EXCLUDE子句定义一个排除约束，保证了两个行在指定的字段或表达式集合上使用指定操作符进行比较时， 并不都返回TRUE。如果所有指定的操作符测试都是相等比较， 那么就等同于UNIQUE约束，尽管一个普通的唯一约束会更快速。 不过，排除约束可以指定比简单相等更通用的约束。例如， 你可以通过使用&&操作符指定一个约束， 在一个表中没有两个行包含重叠的圆圈。

排除约束是使用索引实现的，所以每个指定的操作符都必须和索引访问方法的一个合适的操作符类相关。要求操作符是可交换的。 每个exclude_element 可以指定一个操作符类和/或排序选项；这些在CREATE INDEX中有充分的描述。

访问方法必须支持amgettuple； 目前，这意味着不能使用GIN。尽管允许使用， 但是在排除约束中使用B-tree或哈希索引没什么意义， 因为一个普通唯一约束做的更好。所以实际中，访问方法总是GiST 或SP-GiST。

predicate允许在表的子集上声明一个排除约束； 在内部这创建了一个部分索引。请注意，谓语需要有括号包围。

REFERENCES reftable [ ( refcolumn ) ] [ MATCH matchtype ](column constraint)
FOREIGN KEY ( column_name [, ... ] ) REFERENCES reftable [ ( refcolumn [, ... ] ) ] [ MATCH matchtype ] 
这些子句声明一个外键约束，外键约束要求新表中一列或多列组成的组应该只包含 匹配被参考的表中对应字段中的值。如果省略refcolumn，则使用 reftable的主键。 被参考字段必须是被参考表中的唯一字段或者主键。请注意， 不能在临时表和永久表之间定义外键约束。

向参考字段插入的数值将使用给出的匹配类型与被参考表中被参考列的数值进行匹配。 有三种匹配类型：MATCH FULL, MATCH PARTIAL, MATCH SIMPLE(缺省)。MATCH FULL 不允许一个多字段外键的字段为 NULL ，除非所有外键字段都为 NULL； 如果都是NULL，那么该行在引用的表中不需要有匹配。MATCH SIMPLE 允许任意外键字段为 NULL；如果都是NULL，那么该行在引用的表中不需要有匹配。 MATCH PARTIAL目前尚未实现。（当然，NOT NULL 约束可以应用于引用字段，以阻止这些情况的发生。）

另外，当被参考字段中的数据改变的时候，那么将对本表的字段中的数据执行某种操作。 <!--ON DELETE子句声明当被参考表中的被参考行被删除的时候要执行的操作。  如果该行被更新，但被参考的字段实际上没有变化，那么就不会有任何动作。 除了NO ACTION检查之外的其他参考动作都不能推迟， 即使该约束声明为可推迟也是如此。-->下面是每个子句的可能动作：

<!--NO ACTION
生成一个错误，表明删除或更新将产生一个违反外键约束的动作。 如果该约束是可推迟的，并且如果还存在任何引用行，那么这个错误将在检查约束的时候生成。 这是缺省动作。-->

RESTRICT
生成一个表明删除或更新将导致违反外键约束的错误。 和NO ACTION一样，只是动作不可推迟。

CASCADE
删除任何引用了被删除行的行，或者分别把引用行的字段值更新为被参考字段的新数值。

SET NULL
把引用行设置为 NULL 。

SET DEFAULT
把引用字段设置为它们的缺省值。（如果他们为非空，那么被引用的表中必须有一行匹配缺省值， 否则该操作将会失败。）

如果被参考字段经常更新，那么给引用字段增加一个索引可能是合适的， 这样与外键约束相关联的引用动作可以更有效地执行。

DEFERRABLE
NOT DEFERRABLE
这两个关键字设置该约束是否可推迟。一个不可推迟的约束将在每条命令之后马上检查。 可推迟约束可以推迟到事务结尾使用SET CONSTRAINTS命令检查。 缺省是NOT DEFERRABLE。目前，只有UNIQUE、 PRIMARY KEY、EXCLUDE和REFERENCES（外键） 约束接受这个子句。NOT NULL和CHECK约束都是不可推迟的。

INITIALLY IMMEDIATE
INITIALLY DEFERRED
如果约束是可推迟的，那么这个子句声明检查约束的缺省时间。 如果约束是INITIALLY IMMEDIATE(缺省)， 那么每条语句之后就立即检查它。如果约束是INITIALLY DEFERRED， 那么只有在事务结尾才检查它。约束检查的时间可以用 SET CONSTRAINTS命令修改。

WITH ( storage_parameter [= value] [, ... ] )
这个子句为表或索引指定一个可选的存储参数，参见存储参数获取更多信息。 用于表的WITH子句还可以包含OIDS=TRUE或单独的 OIDS来指定给新表中的每一行都分配一个 OID(对象标识符)， 或者OIDS=FALSE表示不分配 OID 。如果没有指定OIDS 默认行为取决于default_with_oids配置参数。 如果新表是从有 OID 的表继承而来，那么即使明确指定OIDS=FALSE 也将强制按照OIDS=TRUE执行。

如果明确或隐含的指定了OIDS=FALSE，新表将不会存储 OID ， 也不会为插入的行分配 OID 。这将减小 OID 的开销并因此延缓了 32-bit OID 计数器的循环。因为一旦计数器发生循环之后 OID 将不能被视为唯一， 这将大大降低 OID 的实用性。另外， 排除了 OID 的表也为每条记录减小了 4 字节的存储空间（在大多数机器上）， 从而可以稍微提升一些性能。

可以使用ALTER TABLE从已有的表中删除 OID 列。

WITH OIDS
WITHOUT OIDS
这些是被废弃的、分别等价于WITH (OIDS)和WITH (OIDS=FALSE) 的语法。如果想同时给出OIDS设置和存储参数，必须使用 WITH ( ... )语法；见上文。

ON COMMIT
可以使用ON COMMIT控制临时表在事务块结尾的行为。 这三个选项是：

PRESERVE ROWS
在事务的结尾不采取任何特别的动作，这是缺省。

<!--DELETE ROWS
在每个事务块的结尾都删除临时表中的所有行。 本质上是在每次提交事务后自动执行一个TRUNCATE命令。-->

DROP
在当前事务块的结尾删除临时表。

TABLESPACE tablespace_name
指定新表将要在tablespace_name 表空间内创建。如果没有声明，将使用default_tablespace， 如果该表为临时表，那么将使用temp_tablespaces。

USING INDEX TABLESPACE tablespace_name
这个子句允许选择与一个UNIQUE、PRIMARY KEY 或EXCLUDE约束相关的索引创建时所在的表空间。如果没有声明， 将使用default_tablespace， 如果该表为临时表，那么将使用temp_tablespaces。

<br/>**存储参数**

WITH子句可以为表指定存储参数，并在索引上创建 UNIQUE、PRIMARY KEY或EXCLUDE 约束。用于索引的存储参数在CREATE INDEX里面描述。 目前可以在表上使用的存储参数在下面列出。对于每个参数，除非指明了， 有一个额外的参数是相同的名字加上toast.前缀， 可以用来控制表的次要TOAST表的行为。 请注意，TOAST表从它的父表中继承autovacuum值 ，如果没有设置toast.autovacuum设置。

fillfactor (integer)

一个表的填充因子(fillfactor)是一个介于 10 和 100 之间的百分数。 100(完全填充)是默认值。如果指定了较小的填充因子，INSERT 操作仅按照填充因子指定的百分率填充表页。对于一个从不更新的表将填充因子设为 100 是最佳选择，但是对于频繁更新的表，较小的填充因子则更加有效。 这个参数不能为TOAST表设置。

autovacuum_enabled, toast.autovacuum_enabled (boolean)

在一个特别的表上启用或禁用自动清理守护进程。如果为真， 当更新或删除的元组的数量超过autovacuum_vacuum_threshold 加上autovacuum_vacuum_scale_factor倍的当前关系中评估的活动的元组时， 自动清理守护进程将在一个特定的表上发起一个VACUUM操作。 相似的，当插入、更新或删除的元组的数量超过autovacuum_analyze_threshold 加上autovacuum_analyze_scale_factor倍的当前关系中评估的活动的元组时， 自动清理守护进程将发起一个ANALYZE操作。如果为假， 这个表将不被自动清理，除了防止事务Id循环。注意，这个变量从autovacuum 设置中继承值。

autovacuum_vacuum_threshold, toast.autovacuum_vacuum_threshold (integer)

在一个特定的表上发起VACUUM操作之前，更新或删除的元组的最小值。

autovacuum_vacuum_scale_factor, toast.autovacuum_vacuum_scale_factor (float4)

添加到autovacuum_vacuum_threshold的reltuples的乘数。

autovacuum_analyze_threshold (integer)

在一个特定的表上发起ANALYZE操作之前，插入、更新或删除的元组的最小值。

autovacuum_analyze_scale_factor (float4)

添加到autovacuum_analyze_threshold的reltuples乘数。

autovacuum_vacuum_cost_delay, toast.autovacuum_vacuum_cost_delay (integer)

自定义autovacuum_vacuum_cost_delay参数。

autovacuum_vacuum_cost_limit, toast.autovacuum_vacuum_cost_limit (integer)

自定义autovacuum_vacuum_cost_limit参数。

autovacuum_freeze_min_age, toast.autovacuum_freeze_min_age (integer)

自定义autovacuum_vacuum_cost_limit参数。请注意， 自动清理将忽略设置一个每表autovacuum_freeze_min_age 大于半个系统范围的autovacuum_freeze_max_age设置的尝试。

autovacuum_freeze_max_age, toast.autovacuum_freeze_max_age (integer)

自定义autovacuum_freeze_max_age参数。请注意， 自动清理将忽略设置一个每表autovacuum_freeze_max_age 大于系统范围的设置的尝试（它只能设置的较小一些）。注意， 你可以将autovacuum_freeze_max_age设置的非常小，甚至为零， 这通常是不明智的，因为它将强制频繁的清理。

autovacuum_freeze_table_age, toast.autovacuum_freeze_table_age (integer)

自定义vacuum_freeze_table_age参数。

<br/>**注意**

不建议在新应用中使用 OID ，可能情况下，更好的选择是使用一个SERIAL 或者其它序列发生器做表的主键。如果一个应用使用了 OID 标识表中的特定行， 那么建议在该表的oid字段上创建一个唯一约束， 以确保该表的 OID 即使在计数器循环之后也是唯一的。 如果你需要一个整个数据库范围的唯一标识，那么就要避免假设 OID 是跨表唯一的， 你可以用tableoid和行 OID 的组合来实现这个目的。

提示: 对那些没有主键的表，不建议使用OIDS=FALSE， 因为如果既没有 OID 又没有唯一数据字段，那么就很难标识特定的行。
EdsSQL 自动为每个唯一约束和主键约束创建一个索引以确保其唯一性。因此， 不必为主键字段明确的创建索引。参阅CREATE INDEX获取更多信息。

唯一约束和主键在目前的实现里是不能继承的。 如果把继承和唯一约束组合在一起会导致无法运转。

一个表不能超过 1600 个字段。实际的限制比这个更低，因为还有元组长度限制。

<br/>**示例**

创建films和distributors表：
```SQL
CREATE TABLE films (
    code        char(5) CONSTRAINT firstkey PRIMARY KEY,
    title       varchar(40) NOT NULL,
    did         integer NOT NULL,
    date_prod   date,
    kind        varchar(10),
    len         interval hour to minute
);

CREATE TABLE distributors (
     did    integer PRIMARY KEY DEFAULT nextval('serial'),
     name   varchar(40) NOT NULL CHECK (name <> '')
);
```
创建一个带有 2 维数组的表：
```SQL
CREATE TABLE array_int (
    vector  int[][]
);
为表films定义一个唯一表约束。 唯一表约束可以在表的一个或多个字段上定义：

CREATE TABLE films (
    code        char(5),
    title       varchar(40),
    did         integer,
    date_prod   date,
    kind        varchar(10),
    len         interval hour to minute,
    CONSTRAINT production UNIQUE(date_prod)
);
```
定义一个检查列约束：
```SQL
CREATE TABLE distributors (
    did     integer CHECK (did > 100),
    name    varchar(40)
);
```
定义一个检查表约束：
```SQL
CREATE TABLE distributors (
    did     integer,
    name    varchar(40)
    CONSTRAINT con1 CHECK (did > 100 AND name <> '')
);
```
为表films定义一个主键表约束。

CREATE TABLE films (
    code        char(5),
    title       varchar(40),
    did         integer,
    date_prod   date,
    kind        varchar(10),
    len         interval hour to minute,
    CONSTRAINT code_title PRIMARY KEY(code,title)
);
为表distributors定义一个主键约束。下面两个例子是等效的， 第一个例子使用了表约束语法，第二个使用了列约束语法。
```SQL
CREATE TABLE distributors (
    did     integer,
    name    varchar(40),
    PRIMARY KEY(did)
);

CREATE TABLE distributors (
    did     integer PRIMARY KEY,
    name    varchar(40)
);
```
下面这个例子给字段name赋予了一个文本常量缺省值， 并且将字段did的缺省值安排为通过选择序列对象的下一个值生成。 modtime的缺省值将是该行插入的时间戳。

CREATE TABLE distributors (
    name      varchar(40) DEFAULT 'Luso Films',
    did       integer DEFAULT nextval('distributors_serial'),
    modtime   timestamp DEFAULT current_timestamp
);
在表distributors上定义两个NOT NULL列约束， 其中之一明确给出了名字：

CREATE TABLE distributors (
    did     integer CONSTRAINT no_null NOT NULL,
    name    varchar(40) NOT NULL
);
为name字段定义一个唯一约束：

CREATE TABLE distributors (
    did     integer,
    name    varchar(40) UNIQUE
);
相同的，声明为一个表约束：

CREATE TABLE distributors (
    did     integer,
    name    varchar(40),
    UNIQUE(name)
);
创建同样的表，并为表以及唯一索引指定 70% 填充率：

CREATE TABLE distributors (
    did     integer,
    name    varchar(40),
    UNIQUE(name) WITH (fillfactor=70)
)
WITH (fillfactor=70);
创建一个带有排除约束的表circles， 阻止任意两个圆重叠：

CREATE TABLE circles (
    c circle,
    EXCLUDE USING gist (c WITH &&)
);
在表空间diskvol1里创建cinemas表：

CREATE TABLE cinemas (
        id serial,
        name text,
        location text
) TABLESPACE diskvol1;
创建一个复合类型和类型化的表：

CREATE TYPE employee_type AS (name text, salary numeric);

CREATE TABLE employees OF employee_type (
    PRIMARY KEY (name),
    salary WITH OPTIONS DEFAULT 1000
);

<br/>**兼容性**

CREATE TABLE遵循SQL标准， 一些例外情况在下面列出。

**临时表**

尽管CREATE TEMPORARY TABLE的语法和 SQL 标准的类似，但是效果是不同的。 在标准里，临时表只是定义一次并且从空内容开始自动存在于任何需要它们的会话中。 EdsSQL要求每个会话为它们使用的每个临时表发出它们自己的 CREATE TEMPORARY TABLE命令。 这样就允许不同的会话将相同的临时表名字用于不同的目的， 而标准的实现方法则把一个临时表名字约束为具有相同的表结构。

标准定义的临时表的行为被广泛地忽略了。EdsSQL 在这方面上的行为类似于许多其它 SQL 数据库系统。

SQL标准也区分全局和局部临时表，局部临时表对于每个会话中的每个SQL模块都有一个单独的内容设置， 虽然它的定义仍然在会话中共享。因为EdsSQL 不支持SQL模块，所以这个差别在EdsSQL不相关。

出于兼容考虑，EdsSQL将接受临时表声明中的 GLOBAL和LOCAL关键字，但是他们没有任何作用。 不建议使用这些关键字，因为EdsSQL 的未来版本可能采取更加标准兼容的它们的含义。

<!--临时表的ON COMMIT子句也类似于 SQL 标准，但是有些区别。 如果忽略了ON COMMIT子句，SQL 标准声明缺省的行为是 ON COMMIT DELETE ROWS。但是EdsSQL 里的缺省行为是ON COMMIT PRESERVE ROWS。 在 SQL 标准里不存在ON COMMIT DROP选项。-->

**非延迟的唯一性约束**

当UNIQUE或PRIMARY KEY约束是不可延迟的时， 当插入或修改一个行时，EdsSQL 立即检查唯一性。SQL标准说唯一约束应该只在语句的结尾强制执行； 这使得它和EdsSQL不同，例如， 一个命令更新多个键值。要获得标准兼容的行为，声明该约束为 DEFERRABLE但是不能延迟（也就是说，INITIALLY IMMEDIATE）。 要知道，这会比立即检查唯一性要慢的多。

**列检查约束**

SQL 标准说CHECK列约束只能引用他们作用的字段； 只有CHECK表约束才能引用多个字段。EdsSQL 并不强制这个限制；它把字段和表约束看作相同的东西。

**EXCLUDE 约束**

EXCLUDE约束类型是一个EdsSQL扩展。

**NULL "约束"**

NULL "约束"(实际上不是约束)是 EdsSQL对 SQL 标准的扩展， 包括它是为了和其它一些数据库系统兼容以及为了和NOT NULL 约束对称。因为它是任何字段的缺省，所以它的出现是没有意义的。

**继承**

通过INHERITS子句的多重继承是EdsSQL 语言的扩展。SQL:1999 及以后的标准使用不同的语法和语义定义了单继承。 SQL:1999风格的继承还没有在EdsSQL中实现。

**零字段表**

EdsSQL允许创建没有字段的表(比如 CREATE TABLE foo();)。这是对 SQL 标准的扩展， 标准不允许存在零字段表。零字段表本身没什么用， 但是禁止他们会给ALTER TABLE DROP COLUMN带来很奇怪的情况， 所以，这个时候忽视标准的限制概念非常清楚。

**WITH 子句**

WITH子句是EdsSQL的扩展，同样， 存储参数和 OID 也是扩展。

**表空间**

EdsSQL的表空间概念不是标准的东西。 因此TABLESPACE和USING INDEX TABLESPACE都是扩展。

**类型化的表**

类型化的表实现了SQL标准的一个子集。根据标准， 类型化的表有对应于底层复合类型和一个其他的"自我参考字段"。 EdsSQL没有明确支持这些自我参考字段，但是相同的效果可以使用OID特性达到。

<br/>**参见**

ALTER TABLE, DROP TABLE<!--, CREATE TABLE AS, CREATE TABLESPACE, CREATE TYPE-->