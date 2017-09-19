#ALTER TABLE
<br/>**命令**
```SQL
ALTER TABLE [ IF EXISTS ] [ ONLY ] name [ * ]
    action [, ... ]
ALTER TABLE [ IF EXISTS ] [ ONLY ] name [ * ]
    RENAME [ COLUMN ] column_name TO new_column_name
ALTER TABLE [ IF EXISTS ] [ ONLY ] name [ * ]
    RENAME CONSTRAINT constraint_name TO new_constraint_name
ALTER TABLE [ IF EXISTS ] name
    RENAME TO new_name
ALTER TABLE [ IF EXISTS ] name
    SET SCHEMA new_schema

其中action 可以是以选项之一：

    ADD [ COLUMN ] column_name data_type [ COLLATE collation ] [ column_constraint [ ... ] ]
    DROP [ COLUMN ] [ IF EXISTS ] column_name [ RESTRICT | CASCADE ]
    ALTER [ COLUMN ] column_name [ SET DATA ] TYPE data_type [ COLLATE collation ] [ USING expression ]
    ALTER [ COLUMN ] column_name SET DEFAULT expression
    ALTER [ COLUMN ] column_name DROP DEFAULT
    ALTER [ COLUMN ] column_name { SET | DROP } NOT NULL
    ALTER [ COLUMN ] column_name SET STATISTICS integer
    ALTER [ COLUMN ] column_name SET ( attribute_option = value [, ... ] )
    ALTER [ COLUMN ] column_name RESET ( attribute_option [, ... ] )
    ALTER [ COLUMN ] column_name SET STORAGE { PLAIN | EXTERNAL | EXTENDED | MAIN }
    ADD table_constraint [ NOT VALID ]
    ADD table_constraint_using_index
    VALIDATE CONSTRAINT constraint_name
    DROP CONSTRAINT [ IF EXISTS ]  constraint_name [ RESTRICT | CASCADE ]
    DISABLE TRIGGER [ trigger_name | ALL | USER ]
    ENABLE TRIGGER [ trigger_name | ALL | USER ]
    ENABLE REPLICA TRIGGER trigger_name
    ENABLE ALWAYS TRIGGER trigger_name
    DISABLE RULE rewrite_rule_name
    ENABLE RULE rewrite_rule_name
    ENABLE REPLICA RULE rewrite_rule_name
    ENABLE ALWAYS RULE rewrite_rule_name
    CLUSTER ON index_name
    SET WITHOUT CLUSTER
    SET WITH OIDS
    SET WITHOUT OIDS
    SET ( storage_parameter = value [, ... ] )
    RESET ( storage_parameter [, ... ] )
    INHERIT parent_table
    NO INHERIT parent_table
    OF type_name
    NOT OF
    OWNER TO new_owner
    SET TABLESPACE new_tablespace

and table_constraint_using_index is:

    [ CONSTRAINT constraint_name ]
    { UNIQUE | PRIMARY KEY } USING INDEX index_name
    [ DEFERRABLE | NOT DEFERRABLE ] [ INITIALLY DEFERRED | INITIALLY IMMEDIATE ]
```
<br/>**描述**

ALTER TABLE 改变一个现存表的定义。它有好几种子形式：

<ul>
<li>ADD COLUMN<br/><br/>
这种形式使用和CREATE TABLE一样的语法向表中增加一个新的字段。

DROP COLUMN [ IF EXISTS ]
这种形式从表中删除一个字段。和这个字段相关的索引和表约束也会被自动删除。 如果任何表之外的对象依赖于这个字段，必须使用CASCADE选项，比如外键约束、视图等。 如果使用了IF EXISTS选项并且相关字段不存在，则在删除时不会显示错误，仅会有一个通知。</li><br/><br/>

<li>IF EXISTS<br/><br/>
如果使用了IF EXISTS选项并且表不存在，则在删除时不会显示错误，仅会有一个通知。</li><br/><br/>

<li>SET DATA TYPE<br/><br/>
这种形式改变表中一个字段的类型。该字段涉及的索引和简单的表约束将被自动地转换为使用新的字段类型，方法是重新分析最初提供的表达式。 可选的COLLATE选项定义了新列的字符集排序方式，如果不加这个选项，则排序方式使用新类型的缺省值。 可选的USING选项定义如何从旧的字段值里计算新的字段值；如果省略，那么缺省的转换就是从旧类型向新类型的赋值转换。 如果从旧数据类型到新类型没有隐含或者赋值的转换，那么必须提供一个USING选项。</li><br/><br/>

<li>SET/DROP DEFAULT<br/><br/>
这种形式设置或是删除一列的缺省值。缺省值仅会对后续的INSERT<!--或是UPDATE命令-->，不会影响已经在表中存在的记录。</li><br/><br/>

<li>SET/DROP NOT NULL<br/><br/>
这种形式修改一个字段是否允许 NULL 值或者拒绝 NULL 值。如果表中字段里包含非 NULL值 ，那么你只可以使用SET NOT NULL选项。</li><br/><br/>

<li>SET STATISTICS<br/><br/>
这种形式为后续的ANALYZE操作设置每列的数据统计目标值。 目标值可以设置为0至10000；相应地，设置为-1则会反向使用系统缺省的统计目标值 (default_statistics_target)。 </li><br/><br/>

<li>SET ( attribute_option = value [, ... ] )<br/>
RESET ( attribute_option [, ... ] )<br/><br/>
这种形式设置或者重置每个属性的参数值。目前，已定义的属性参数值是n_distinct和n_distinct_inherited， 它们会覆盖由随后的ANALYZE操作所估算的number-of-distinct-values。 n_distinct影响表本身的统计值，而n_distinct_inherited影响表及其继承子表的统计。 当设置为一个正值时，ANALYZE将会假定列准确包含明确的非空值的指定数目。 当设置为一个必须大于或者等于-1的负值时，ANALYZE将会假定在列中的不同的非空值的数目与表的大小关系是线性的； 确切的统计通过将估算的表大小与给定数字的绝对值相乘来统计。 例如， 值-1意味着在此列中的所有值是不同的，值-0.5意味着每个值平均出现两次。 当表的大小随时间变化时这是很有效的，尽管表中行数的乘法运算在查询规划计时前是不会这样计算的， 声明一个0值来正常地恢复到估计不同数值的数目。 </li><br/><br/>

<li>SET STORAGE<br/><br/>
这种形式为一个字段设置存储模式。这个设置控制这个字段是内联保存还是保存在一个TOAST附属的表里，以及数据是否要压缩。 PLAIN选项必须用于定长的数值，比如integer并且是内联的、不压缩的。 MAIN用于内联、可压缩的数据。 EXTERNAL用于外部保存、不压缩的数据，EXTENDED用于外部的压缩数据。 EXTENDED是大多数支持非PLAIN存储的数据的缺省值。 使用EXTERNAL将会在text和bytea字段上的字符串操作更快，但付出的代价是增加了存储空间。 请注意SET STORAGE本身并不改变表上的任何东西，只是设置将来的表操作时，建议使用的策略。</li><br/><br/>

<li>ADD table_constraint [ NOT VALID ]<br/><br/>
这种形式给表增加一个新的约束，使用的语法和CREATE TABLE一样，而与NOT VALID选项组合时，这种约束仅在对外键和CHECK类药束有效。 如果约束条件增加了NOT VALID选项后，表中已有记录是否满足初始约束检查会被跳过。这种约束会对后续的新增记录或是记录更新产生影响（在外键约束的情况下， 除非是参照的表中有匹配的记录，否则这样的情形会出错；而在CHECK约束下，除非是新增记录满足CHECK约束条件，否则也会也错）。 但数据库自身不会认定这种约束会对表中所有记录生效，除非是在使用了VALIDATE CONSTRAINT选项进行了有效性检查。

ADD table_constraint_using_index
这种形式根据已有的唯一索引给表增加新的PRIMARY KEY或UNIQUE约束。索引中所有的列也会在包含在约束中。

索引中不能含有表达式列或者是局部索引。另外索引的缺省排序方式应是B-tree类型。这些限制保证了索引会与通过正常ADD PRIMARY KEY或ADD UNIQUE选项生成的索引相同。

如果使用了PRIMARY KEY选项，则索引的列不能已定义为NOT NULL，因为这个选项会对涉及的列去执行ALTER COLUMN SET NOT NULL。 这也会对全表数据进行扫描以验证该列是否包含空值，（全表扫描很慢），在其他情况下，这是一个很快的操作。

如果指定了一个约束名，索引将会被重命名为指定的约束中的名称。相反，约束会被命名为索引名。

在这个命令执行后，约束就相当于是索引的"所有者"了，就如同使用了ADD PRIMARY KEY或ADD UNIQUE命令创建的索引一样。 特别要注意的事，这种情况下删除约束也会清除了索引。

注意: 在新增约束时使用已有的索引对新约束增加时对表记录的较长时间不能更新的问题有较好的帮助。 使用CREATE INDEX CONCURRENTLY指令可以实现这种方式，参见下面的示例。</li><br/><br/>

<li>VALIDATE CONSTRAINT<br/><br/>
这种形式用于验证一个外键或是一个使用NOT VALID选项创建的检查类约束，通过扫描全表来保证所有记录都符合约束条件。 如果约束已标记为有效时，什么操作也不会发生。

对大表的记录进行验证一般是一个很长的过程，并且目前这种操作还需要ACCESS EXCLUSIVE排他类锁。 初始的不同验证可以将验证工作后延至系统不忙时进行，或者通过先花一点额外的时间来纠正可能存在的错误以防止新错误的发生。</li><br/><br/>

<li>DROP CONSTRAINT [ IF EXISTS ]<br/><br/>
这种形式删除一个表中指定的约束，如果使用了 IF EXISTS选项并且约束并不存在时，也不会有错误产生，仅会有一个通知。</li><br/><br/>

<li>DISABLE/ENABLE [ REPLICA | ALWAYS ] TRIGGER<br/><br/>
这种形式禁用或者启用属于该表的触发器。一个被关闭掉的触发器是系统仍然知道的，但是在触发器事件发生的时候不会被执行。 对于一个推迟了的触发器，在触发事件发生的时候会检查打开状态，触发器相关的函数实际也不会执行。 可以通过指定名字的方法启用或者禁用任意一个触发器，或者是该表上的所有触发器，或者只是用户自定义的触发器(这个选项排除了那些用于实现外键约束或是可延迟的唯一性约束或是排他性约束的触发器)。 启用或者禁用约束触发器要求超级用户权限；这么做的时候应该小心，因为如果触发器不执行的话，约束保证的数据完整性也就没有办法确保了。 触发器启动原理也受配置变量session_replication_role影响。 简单启动的触发器将会在复制任务为"初始"(默认情况)或者"本地"时启动。 配置为ENABLE REPLICA的触发器将会仅在会话为"replica" 模式时启动，而配置为ENABLE ALWAYS的触发器将总是会启动，无论是否为当前复制模式。</li><br/><br/>

<li>DISABLE/ENABLE [ REPLICA | ALWAYS ] RULE<br/><br/>
这种形式配置属于表的重写规则制定。一个已禁用的规则对系统来说仍然是可知的，但在查询重写期间是不被应用的。 语义为关闭/启动触发器。这个配置对ON SELECT规则来说是可忽略的，常常用来保持视图工作，即使当前会话处于一个非默认的复制角色中。</li><br/><br/>

<li>CLUSTER ON<br/><br/>
这种形式为将来的CLUSTER操作选择默认索引。 实际上并没有重新聚簇该表。</li><br/><br/>

<li>SET WITHOUT CLUSTER<br/><br/>
这种形式从表中删除最近一次用到的CLUSTER索引定义。这会影响将来不指定索引的聚簇操作。</li><br/><br/>

<li>SET WITH OIDS<br/><br/>
这种形式向表中增加一个oid系统字段。 如果表中已存在有OID字段，则操作对表无任何影响。

注意这种形式与ADD COLUMN oid oid选项并不相同，后者对给表增加一个普通的字段，只不过它的名称恰好是叫 oid，并非是系统字段。</li><br/><br/>

<li>SET WITHOUT OIDS<br/><br/>
这种形式从表中删除 oid系统字段。它和DROP COLUMN oid RESTRICT完全相同，只不过是如果表上已经没有oid字段的时候不会报错。</li><br/><br/>

<li>SET ( storage_parameter = value [, ... ] )<br/><br/>
这种形式改变表的一个或者更多存储参数。参阅存储参数获取关于可用参数的详细信息。 请注意表的内容将不会因为此命令被立刻调整；根据此参数你可能需要重写此表来得到希望的效果。 这可以通过VACUUM FULL、CLUSTER或者ALTER TABLE命令中的选项之一来实现。

注意: 尽管CREATE TABLE允许OIDS在WITH (storage_parameter)语义中声明， 但ALTER TABLE不会将OIDS作为一个存储参数。 相反地，要使用SET WITH OIDS和SET WITHOUT OIDS形式来更改OID状态。
RESET ( storage_parameter [, ... ] )
这种形式重置表的一个或多个存储参数为缺省值。与SET选项一样，根据参数的不同可能需要重写表才能获得想要的效果。</li><br/><br/>

<li>INHERIT parent_table<br/><br/>
这种形式将目标表添加为指定父表的新子表。 之后在父表上的查询将包含目标表中的记录。 要被添加为一个子表，目标表必须已经包含所有与父表相同的字段(除此之外 当然也可以包含一些其它字段)，这些字段的数据类型必须匹配，并且如果父表的字段有NOT NULL约束的话子表的相应字段也必须有NOT NULL约束。

所有父表的CHECK约束必须同时与子表的约束匹配。 不过一些标记为不可继承类的约束（类似使用ALTER TABLE ... ADD CONSTRAINT ... NO INHERIT创建的约束）不包括在内， 所有子表匹配的约束也不能标记为不可继承。 目前UNIQUE、PRIMARY KEY和FOREIGN KEY约束不被考虑在内，但是将来可能会有所改变。</li><br/><br/>

NO INHERIT parent_table
这种形式从指定父表的子表列表中删除目标表。这样，在父表上的查询将不再目标表中的记录。</li><br/><br/>

<li>OF type_name<br/><br/>
这种形式将表链接至一种复合类型，就好象是使用CREATE TABLE OF选项创建表一样。 表的字段的名称和类型必须精确匹配复合类型中的定义，不过oid系统字段允许不一样。 表不能是从任何其他表继承的。 这些限制确保CREATE TABLE OF选项允许一个相同的表定义。</li><br/><br/>

<li>NOT OF<br/><br/>
这种形式将一个与某类型进行关联的表进行关联的解除。</li><br/><br/>

<li>OWNER<br/><br/>
这种形式改变表、序列或是视图的所有者为一个指定的用户。</li><br/><br/>

<li>SET TABLESPACE<br/><br/>
这种形式更改表的表空间为一个指定的表空间，并将与这个表相关的数据文件移至新的表空间。 表上如果有索引，一般不会移动。不过它们也可以通过使用SET TABLESPACE命令单独移动，参见CREATE TABLESPACE。</li><br/><br/>

<li>RENAME<br/><br/>
RENAME形式改变一个表(或者索引、序列、视图)的名称，表中单个字段的名称，或是表中约束的名称。 它们对存储的数据没有影响。</li><br/><br/>

<li>SET SCHEMA
这种形式把表移动到另外一个模式。相关的索引、约束、序列都跟着移动。

除了RENAME和SET SCHEMA之外所有动作都可以组合在一个多次修改列表中同时使用。 比如，可以在一个命令里增加几个字段和/或修改几个字段的类型。 对于大表，这么做特别有用，因为只需要对该表做一次处理。

要使用ALTER TABLE，你必须是该表的所有者。 要修改一个表的模式，你还必须在新模式上拥有CREATE权限。 要把该表添加为一个父表的新子表，你必须同时是父表的所有者。 要修改所有者，你还必须是新的所有角色的直接或间接成员，并且该成员必须在此表的模式上有CREATE权限。 （这些限制强制了修改该所有者不会做任何通过删除和重建表不能做的事情。不过，超级用户可以以任何方式修改任意表的所有权。） 增加一个字段或是改变字段的类型或是使用OF选项，你也必须对那个数据类型有USAGE权限。</li></ul>

<br/>**参数**

*name*：要修改的已有表的名称（可以有模式修饰）。若声明了ONLY选项，则只有那个表被更改。若未声明ONLY，该表及其所有子表都将会被更改。 另外，可以在表名称后面精确地增加*选项来指定包括子表。

*column_name*：现存或新的字段名称。

*new_column_name*：现存字段的新名称。

*new_name*：表的新名称。

*type*：新字段的类型，或者现存字段的新类型。

*table_constraint*：新的表约束。

*constraint_name*：要删除的现有约束的名字。

CASCADE：级联删除依赖于被依赖字段或者约束的对象(比如引用该字段的视图)。

RESTRICT：如果字段或者约束还有任何依赖的对象，则拒绝删除该字段。这是缺省行为。。

*trigger_name*：要启用或者禁用的单个触发器的名字。

ALL：启用或者禁用所有属于该表的触发器。（如果任何触发器属于内部会产生约束的触发器，这要求超级用户权限，例如那些用于执行外键约束或者可推迟的独特性和排除约束。）

USER：启用或者禁用所有属于表的触发器，除了任何属于内部会产生约束的触发器，例如那些用于执行外键约束或者可推迟的独特性和排除约束。）

*index_name*：标记实施表的聚簇操作的索引名字。

*storage_parameter*：表的存储参数的名字。

*value*：表的存储参数的新值，根据参数的不同，可能是一个数字或单词。

*parent_table*：将要与该表建立/取消关联的父表。

*new_owner*：该表的新所有者的用户名。

*new_tablespace*：这个表将要移动到的表空间名字。

*new_schema*：表将移动到的新模式的名字。

<br/>**注意**

COLUMN关键字是多余的，可以省略。。

如果用ADD COLUMN增加一个字段，那么所有表中现有行都初始化为该字段的缺省值(如果没有声明DEFAULT子句，那么就是 NULL)。

添加一个非空缺省值列或者改变一个原有列的类型需要重写整个表和索引。 不过也有例外，如果使用USING选项不改变字段的内容并且字段的新旧类型是二进制兼容的，也可以不重写表。 添加或者删除一个系统oid列同样需要重写整个表。 大表及索引的重写可能需要非常长的时间，并且也临时需要两倍的磁盘空间。

增加一个CHECK或NOT NULL约束将会扫描该表以保证现有的行符合约束要求。

提供在一个ALTER TABLE里面声明多个修改的主要原因是原先需要的对表的多次扫描和重写可以组合成一个操作。

DROP COLUMN命令并不是物理上把字段删除，而只是简单地 把它标记为对 SQL 操作不可见。 随后对该表的插入和更新将在该字段存储一个 NULL 。 因此，删除一个字段是很快的，但是它不会立即释放表在磁盘上的空间，因为被删除 了的字段占据的空间还没有回收。 这些空间将随着现有的行的更新而得到回收。 (在删除系统oid列时，方式有点不同，这个操作是直接执行了一次表的重写。)

要强制立刻执行一次表的重写，可以使用VACUUM FULL、CLUSTER或是ALTER TABLE命令中的一些形式。 这些命令从可见的语义上不会对表产生更新，但会清除不再有用的数据。

使用SET DATA TYPE命令中的USING选项实际上可以指定任何表达式涉及到记录旧值； 也就是说，它可以引用除了正在被转换的字段之外其它的字段。 这样，就可以用SET DATA TYPEE语法做非常普遍性的转换。 因为这个灵活性， USING表达式并没有应用于该字段的缺省值(如果有的话)；结果可能不是缺省表达式要求的常量表达式。 这就意味着如果从旧类型到新类型没有隐含或者赋值 转换的话，那么即使存在USING选项，SET DATA TYPE也可能无法把缺省值转换成新的类型。 在这种情况下，应该用DROP DEFAULT先删除缺省值，执行 ALTER TYPE，然后使用SET DEFAULT增加一个 合适的新缺省值。 类似的考虑也适用于涉及该字段的索引和约束。

如果一个表有子表，那么如果不在子表上做同样的修改的话，就不允许在父表上增加、 重命名、修改一个字段的类型。 也就是说，ALTER TABLE ONLY将被拒绝执行。 这样就保证了子表总是有和父表匹配的字段。

一个递归 DROP COLUMN操作将只有在子表并不从任何其它父表中继承该字段并且从来没有独立定义该字段的时候才能删除一个子表的字段。 一个非递归的DROP COLUMN(也就是 ALTER TABLE ONLY ... DROP COLUMN) 从来不会删除任何子表字段， 而是把他们标记为独立定义的(而不是继承的)。 。

TRIGGER、CLUSTER、OWNER和TABLESPACE的操作绝不会递归影响到子表； 也就是说，它们的行为就像总是声明了ONLY 一样。 只有没有标记为NO INHERIT的CHECK约束才能添加一个递归性的约束。

不允许更改系统表结构的任何部分。

<br/>**示例**

向表中增加一个varchar字段：
```SQL
ALTER TABLE distributors ADD COLUMN address varchar(30);
```
从表中删除一个字段：
```SQL
ALTER TABLE distributors DROP COLUMN address RESTRICT;
```
在一个操作中修改两个现有字段的类型：
```SQL
ALTER TABLE distributors
    ALTER COLUMN address TYPE varchar(80),
    ALTER COLUMN name TYPE varchar(100);
```
使用一个USING选项，把一个包含 UNIX 时间戳的 integer 字段转化成 timestamp with time zone字段：
```SQL
ALTER TABLE foo
    ALTER COLUMN foo_timestamp SET DATA TYPE timestamp with time zone
    USING
        timestamp with time zone 'epoch' + foo_timestamp * interval '1 second';
```
同样地，当字段有一个不会自动转换成新类型的缺省值表达式时：
```SQL
ALTER TABLE foo
    ALTER COLUMN foo_timestamp DROP DEFAULT,
    ALTER COLUMN foo_timestamp TYPE timestamp with time zone
    USING
        timestamp with time zone 'epoch' + foo_timestamp * interval '1 second',
    ALTER COLUMN foo_timestamp SET DEFAULT now();
```
对现有字段改名：
```SQL
ALTER TABLE distributors RENAME COLUMN address TO city;
```
更改现有表的名字：
```SQL
ALTER TABLE distributors RENAME TO suppliers;
```
更改现有约束的名字：
```SQL
ALTER TABLE distributors RENAME CONSTRAINT zipchk TO zip_check;
```
给一个字段增加一个非空约束：
```SQL
ALTER TABLE distributors ALTER COLUMN street SET NOT NULL;
```
从一个字段里删除一个非空约束：
```SQL
ALTER TABLE distributors ALTER COLUMN street DROP NOT NULL;
```
给一个表增加一个检查约束：
```SQL
ALTER TABLE distributors ADD CONSTRAINT zipchk CHECK (char_length(zipcode) = 5);
```
给一个表且不包含其子表增加一个检查约束：
```SQL
ALTER TABLE distributors ADD CONSTRAINT zipchk CHECK (char_length(zipcode) = 5) NO INHERIT;
```
（这个检查约束也不会被以后新增的子表继承。）

删除一个表及其所有子表的检查约束：
```SQL
ALTER TABLE distributors DROP CONSTRAINT zipchk;
```
从表中删除一个检查约束（不包含子表）：
```SQL
ALTER TABLE ONLY distributors DROP CONSTRAINT zipchk;
```
（这个检查约束对所有子表仍保留。）

向表中增加一个外键约束：
```SQL
ALTER TABLE distributors ADD CONSTRAINT distfk FOREIGN KEY (address) REFERENCES addresses (address);
```
给表增加一个(多字段)唯一约束：
```SQL
ALTER TABLE distributors ADD CONSTRAINT dist_id_zipcode_key UNIQUE (dist_id, zipcode);
```
给一个表增加一个自动命名的主键约束，要注意的是一个表只能有一个主键：
```SQL
ALTER TABLE distributors ADD PRIMARY KEY (dist_id);
```
把表移动到另外一个表空间：
```SQL
ALTER TABLE distributors SET TABLESPACE fasttablespace;
```
把表移动到另外一个模式：
```SQL
ALTER TABLE myschema.distributors SET SCHEMA yourschema;
```
重新创建一个主键约束，并且在主键索引在创建时不影响记录的更新：
```SQL
CREATE UNIQUE INDEX CONCURRENTLY dist_id_temp_idx ON distributors (dist_id);
ALTER TABLE distributors DROP CONSTRAINT distributors_pkey,
    ADD CONSTRAINT distributors_pkey PRIMARY KEY USING INDEX dist_id_temp_idx;
```    
<br/>**兼容性**

ADD (不包含USING INDEX)、DROP、SET DEFAULT和SET DATA TYPE (不包含USING)形式与 SQL 标准兼容。 其它形式是EdsSQL对 SQL 标准 的扩展。还有，在一个ALTER TABLE命令里声明多个操作也是扩展。

ALTER TABLE DROP COLUMN可以用于删除表中的唯一的一个字段，留下一个零字段的表。这是对 SQL 的扩展，它不允许零字段表。

<br/>**参见**

CREATE TABLE