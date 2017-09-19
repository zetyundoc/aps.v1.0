# REVOKE（企业版支持）
<br/>**命令**
```SQL
REVOKE [ GRANT OPTION FOR ]
    { { SELECT | INSERT | TRUNCATE | REFERENCES | TRIGGER }
    [, ...] | ALL [ PRIVILEGES ] }
    ON { [ TABLE ] table_name [, ...]
         | ALL TABLES IN SCHEMA schema_name [, ...] }
    FROM { [ GROUP ] role_name | PUBLIC } [, ...]
    [ CASCADE | RESTRICT ]

REVOKE [ GRANT OPTION FOR ]
    { { SELECT | INSERT | REFERENCES } ( column_name [, ...] )
    [, ...] | ALL [ PRIVILEGES ] ( column_name [, ...] ) }
    ON [ TABLE ] table_name [, ...]
    FROM { [ GROUP ] role_name | PUBLIC } [, ...]
    [ CASCADE | RESTRICT ]

REVOKE [ GRANT OPTION FOR ]
    { { USAGE | SELECT}
    [, ...] | ALL [ PRIVILEGES ] }
    ON { SEQUENCE sequence_name [, ...]
         | ALL SEQUENCES IN SCHEMA schema_name [, ...] }
    FROM { [ GROUP ] role_name | PUBLIC } [, ...]
    [ CASCADE | RESTRICT ]

REVOKE [ GRANT OPTION FOR ]
    { { CREATE | CONNECT | TEMPORARY | TEMP } [, ...] | ALL [ PRIVILEGES ] }
    ON DATABASE database_name [, ...]
    FROM { [ GROUP ] role_name | PUBLIC } [, ...]
    [ CASCADE | RESTRICT ]

REVOKE [ GRANT OPTION FOR ]
    { USAGE | ALL [ PRIVILEGES ] }
    ON DOMAIN domain_name [, ...]
    FROM { [ GROUP ] role_name | PUBLIC } [, ...]
    [ CASCADE | RESTRICT ]

REVOKE [ GRANT OPTION FOR ]
    { USAGE | ALL [ PRIVILEGES ] }
    ON FOREIGN DATA WRAPPER fdw_name [, ...]
    FROM { [ GROUP ] role_name | PUBLIC } [, ...]
    [ CASCADE | RESTRICT ]

REVOKE [ GRANT OPTION FOR ]
    { USAGE | ALL [ PRIVILEGES ] }
    ON FOREIGN SERVER server_name [, ...]
    FROM { [ GROUP ] role_name | PUBLIC } [, ...]
    [ CASCADE | RESTRICT ]

REVOKE [ GRANT OPTION FOR ]
    { EXECUTE | ALL [ PRIVILEGES ] }
    ON { FUNCTION function_name ( [ [ argmode ] [ arg_name ] arg_type [, ...] ] ) [, ...]
         | ALL FUNCTIONS IN SCHEMA schema_name [, ...] }
    FROM { [ GROUP ] role_name | PUBLIC } [, ...]
    [ CASCADE | RESTRICT ]

REVOKE [ GRANT OPTION FOR ]
    { USAGE | ALL [ PRIVILEGES ] }
    ON LANGUAGE lang_name [, ...]
    FROM { [ GROUP ] role_name | PUBLIC } [, ...]
    [ CASCADE | RESTRICT ]

REVOKE [ GRANT OPTION FOR ]
    {  SELECT [, ...] | ALL [ PRIVILEGES ] }
    ON LARGE OBJECT loid [, ...]
    FROM { [ GROUP ] role_name | PUBLIC } [, ...]
    [ CASCADE | RESTRICT ]

REVOKE [ GRANT OPTION FOR ]
    { { CREATE | USAGE } [, ...] | ALL [ PRIVILEGES ] }
    ON SCHEMA schema_name [, ...]
    FROM { [ GROUP ] role_name | PUBLIC } [, ...]
    [ CASCADE | RESTRICT ]

REVOKE [ GRANT OPTION FOR ]
    { CREATE | ALL [ PRIVILEGES ] }
    ON TABLESPACE tablespace_name [, ...]
    FROM { [ GROUP ] role_name | PUBLIC } [, ...]
    [ CASCADE | RESTRICT ]

REVOKE [ GRANT OPTION FOR ]
    { USAGE | ALL [ PRIVILEGES ] }
    ON TYPE type_name [, ...]
    FROM { [ GROUP ] role_name | PUBLIC } [, ...]
    [ CASCADE | RESTRICT ]

REVOKE [ ADMIN OPTION FOR ]
    role_name [, ...] FROM role_name [, ...]
    [ CASCADE | RESTRICT ]
```
<br/>**描述**

REVOKE撤销以前赋予一个或多个角色的权限。关键字PUBLIC 代表隐含定义的、拥有所有角色的组。

参阅GRANT命令的描述获取权限类型的含义。

请注意，任何特定的角色都将拥有直接赋予它的权限，加上它所在组的权限， 再加上赋予PUBLIC的权限的总和。因此，举例来说，废止PUBLIC 的SELECT权限并不意味着所有角色都失去了对该对象的SELECT权限： 那些直接得到的权限以及通过一个组得到的权限仍然有效。相似的，废止一个用户的SELECT 权限可能并不阻止用户使用SELECT，如果PUBLIC或其他成员组仍然拥有 SELECT权限。

如果指定了GRANT OPTION FOR，那么只是撤销对该权限的授权的权力， 而不是撤销该权限本身。否则，权限和授权选项都被撤销。

如果一个用户持有某个权限，并且还有授权的选项，并且还把这个权限赋予了其它用户， 那么那些其它用户持有的权限都叫做依赖性权限。如果第一个用户持有的权限或者授权选项被撤销， 而依赖性权限仍然存在；那么如果声明了CASCADE，则所有依赖性权限都被撤销， 否则撤销动作就会失败。这个递规的撤销只影响那种通过一个用户链赋予的权限， 这个链条可以通过这条REVOKE命令里面给出的用户跟踪。因此， 如果权限本身是通过其它用户赋予的，那么被影响的用户可以有效地保留这个权限。

当在表上撤销权限时，相应的字段权限（如果有）也自动撤销。另一方面， 如果已经赋予了一个角色在表上的权限，那么在单独的字段上删除相同的权限将不会有作用。

在撤销一个角色里的成员关系的时候，不是调用ADMIN OPTION而是调用 GRANT OPTION，但是行为类似。不过这种形式的命令不允许出现GROUP噪声字。

<br/>**注意**

使用psql的\dp命令显示在一个现存表和字段上赋予的权限。 又见GRANT获取关于格式的信息。对于非表对象，可以使用\d 命令显示他们的权限。

一个用户只能撤销由它自己直接赋予的权限。举例来说，如果用户 A 带着授权选项把一个权限赋予了用户 B ， 然后用户 B 又赋予了用户 C ，那么用户 A 不能直接将 C 的权限撤销。但是， 用户 A 可以撤销用户 B 的授权选项，并且使用CASCADE选项，这样， 用户 C 的权限就会自动被撤销。另外一个例子：如果 A 和 B 都赋予了 C 同样的权限， 则 A 可以撤销他自己的授权选项，但是不能撤销 B 的，因此 C 仍然有效地拥有该权限。

如果一个对象的非所有者试图REVOKE对象上的权限，那么，如果这个用户没有该对象上的权限， 则命令马上失败。只要他有某些权限，则命令继续，但是它只撤销那些该用户有授权选项的权限。 如果没有在授权选项，那么REVOKE ALL PRIVILEGES形式将发出一个错误消息， 而对于其它形式的命令而言，如果同样是命令中指定名字的权限没有相应的授权选项， 那么该命令将发出一个警告。原则上这些语句也适用于对象所有者， 但是因为所有者总是认为持有所有授权选项，所以这种情况绝不会发生。

如果一个超级用户发出一个GRANT或REVOKE命令， 那么命令是以被影响的对象的所有者执行的。因为所有权限最终从对象所有者 (可能间接通过赋权选项)获取，超级用户可以废除所有权限， 但是这样就要求像上面说的那样使用CASCADE。

REVOKE也可以由一个并非被影响对象的所有者来执行， 不过这个角色必须是拥有该对象的角色的成员，或者是一个在该对象上持有 WITH GRANT OPTION的角色的成员。在这种情况下， 该命令执行起来就好像是由实际拥有该对象的角色，或者是在该对象上持有 WITH GRANT OPTION权限的角色发出的一样。比如，如果表t1 被g1所有，而u1是g1的成员，那么u1 可以撤销t1上的权限，而纪录为g1发出的命令。这种现像包括 u1和其它g1角色成员发出的授权。

如果执行REVOKE的角色持有权限是通过多层成员关系获得的， 那么具体是哪个包含的角色执行的该命令就是未声明的。在这种场合下， 最好的方法是使用SET ROLE成为你希望执行REVOKE的角色。 不这么做的后果可能导致删除你不想删除的权限，或者是啥权限都没有删除。

<br/>**示例**

撤销公众在表films上的插入权限：
```SQL
REVOKE INSERT ON films FROM PUBLIC;
```
撤销用户manuel对视图kinds的所有权限：
```SQL
REVOKE ALL PRIVILEGES ON kinds FROM manuel;
```
请注意这样实际上意味着"撤销所有我赋予的权限"。

删除用户joe的admins成员关系：
```SQL
REVOKE admins FROM joe;
```
<br/>**兼容性**

GRANT命令的兼容性信息基本上也适用于REVOKE。 标准要求RESTRICT或CASCADE必须出现， 但是EdsSQL假设缺省是RESTRICT。

<br/>**参见**

GRANT