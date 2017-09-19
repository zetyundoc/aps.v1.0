# ALTER ROLE（企业版支持）

**命令**

```SQL
ALTER ROLE name [ [ WITH ] option [ ... ] ]

这里的 option 可以是：

SUPERUSER | NOSUPERUSER
| CREATEDB | NOCREATEDB
| CREATEROLE | NOCREATEROLE
| CREATEUSER | NOCREATEUSER
| INHERIT | NOINHERIT
| LOGIN | NOLOGIN
| REPLICATION | NOREPLICATION
| CONNECTION LIMIT connlimit
| [ ENCRYPTED | UNENCRYPTED ] PASSWORD 'password'
| VALID UNTIL 'timestamp'

 ALTER ROLE name RENAME TO new_name
    
ALTER ROLE name [ IN DATABASE database_name ] SET configuration_parameter { TO | = } { value | DEFAULT }
    ALTER ROLE { name | ALL } [ IN DATABASE database_name ] SET configuration_parameter FROM CURRENT
    ALTER ROLE { name | ALL } [ IN DATABASE database_name ] RESET configuration_parameter
    ALTER ROLE { name | ALL } [ IN DATABASE database_name ] RESET ALL```

<br/>**描述**

ALTER ROLE修改一个EdsSQL角色的属性。

这个命令的第一种形式可以修改很多CREATE ROLE里面声明的角色属性。 （除了增加和删除成员关系的选项之外，所有可能的属性都有介绍；使用GRANT和REVOKE可以实现前述两件事。) 没有在命令里提到的属性维持它们以前的设置。数据库超级用户可以给任何角色改变任何设置。 拥有CREATEROLE权限的角色可以修改任意这些设置，但是只能给非超级用户和非复制用户角色设置。 普通的角色只能修改它们自己的口令。

第二种形式可以修改角色的名称。数据库超级用户可以修改任何角色的名称。 拥有CREATEROLE权限的角色可以给非超级用户角色进行重命名。 当前会话的用户的角色是不能改名的。（如果一定需要这么做，则必须以另外一个用户的身份连接系统。） 因为MD5加密的口令使用角色名字作为加密的盐粒，所以， 如果口令是MD5加密的，那么给一个角色改名会清空其口令。

其他的形式更改一个角色的会话配置参数默认值，该值要么针对所有的数据库， 要么使用IN DATABASE选项，仅针对指定的数据库。 如果是未指定角色名称而是使用了ALL选项，则所有角色的相关参数都会更改。 当同时使用ALL和IN DATABASE选项时，就等同于使用ALTER DATABASE ... SET ...。

当角色随后开启一个新会话，指定的值变成了会话的默认值<!--，这些参数值会覆盖任何EdsSQLsql.conf中的设置或者从postgres 命令行接收到的参数值-->。 这仅在登录时发生；执行SET ROLE或者SET SESSION AUTHORIZATION不会引起新的配置值设置。 为所有数据库所设定的参数会被附加到一个角色上的特定数据库的参数所覆盖。为指定数据库或指定角色所设定的参数会覆盖为所有角色设定的参数。

超级用户可以更改任何一个会话默认值。有CREATEROLE权限的角色可以为非超级用户角色更改默认值。 普通的角色只能为自己设置默认值。 某些配置变量不能这样设置，或者只要超级用户才能执行。 只有超级用户才能为所有数据库中的所有角色更改参数设置。

<br/>**参数**

*name*：需要修改属性的角色的名称。

SUPERUSER<br/>
NOSUPERUSER<br/>
CREATEDB<br/>
NOCREATEDB<br/>
CREATEROLE<br/>
NOCREATEROLE<br/>
CREATEUSER<br/>
NOCREATEUSER<br/>
INHERIT<br/>
NOINHERIT<br/>
LOGIN<br/>
NOLOGIN<br/>
REPLICATION<br/>
NOREPLICATION<br/>
CONNECTION LIMIT *connlimit*<br/>
PASSWORD password<br/>
ENCRYPTED<br/>
UNENCRYPTED<br/>
VALID UNTIL *'timestamp'*<br/>

以上选项修改由CREATE ROLE初始设置的属性。 要获取更多详细信息，请参阅CREATE ROLE参考页。

*new_name*：角色的新名字。

*database_name*：要设置配置变量的数据库的名称。

*configuration_parameter value*：把该角色指定的参数缺省值设置为给定值。如果value是DEFAULT或是相当于使用RESET， 指定角色的参数值会被清除，这样该角色将在新的会话里继承系统级的参数缺省值。 使用RESET ALL会清除所有角色相关的设置。 使用SET FROM CURRENT会将会话的当前参数值保存为特定角色的缺省参数值。 如果使用了IN DATABASE选项，则仅仅指定的角色和数据库可以设置或者清除相关参数。

特定角色的参数设置仅仅在登录时起效；SET ROLE和SET SESSION AUTHORIZATION不能进行特定角色的参数设置。

参阅SET获取有关允许的参数名称和数值的更多信息。

<br/>**注意**

使用CREATE ROLE增加新角色，使用DROP ROLE删除旧角色。

ALTER ROLE不能改变角色的成员关系。可以使用GRANT和REVOKE做这个事情。

使用这个命令指定一个未加密的密码时必须小心，因为密码将以明文方式传送到服务器，并且可能被客户端命令历史记录或者被服务器日志记录。 psql包含一个可以用来安全修改角色密码的\password命令，这个命令不会暴露明文的密码。

也可以把会话缺省参数值与数据库绑定而不是与角色绑定；参阅ALTER DATABASE。 如果有冲突，那么指定角色加数据库的参数设置将覆盖指定角色的参数设置，而后者又可以覆盖指定数据库的参数设置。

<br/>**示例**

改变一个角色的口令：

```SQL
ALTER ROLE davide WITH PASSWORD 'hu8jmn3';```

清除一个角色的口令:

```SQL
ALTER ROLE davide WITH PASSWORD NULL;```

改变口令失效的日期，声明口令应该在2015年5月4日中午失效，时区比UTC早一个小时：

```SQL
ALTER ROLE chris VALID UNTIL 'May 4 12:00:00 2015 +1';```

设置一个口令永久有效：

```SQL
ALTER ROLE fred VALID UNTIL 'infinity';```

授予一个角色创建其它角色和新数据库的权限：

```SQL
ALTER ROLE miriam CREATEROLE CREATEDB;```

给一个角色设置非缺省的maintenance_work_mem参数值：

```SQL
ALTER ROLE worker_bee SET maintenance_work_mem = 100000;```

给一个角色设置非缺省的、指定数据库的client_min_messages参数值：

```SQL
ALTER ROLE fred IN DATABASE devel SET client_min_messages = DEBUG;```

<br/>**兼容性**

ALTER ROLE语句是一个EdsSQL扩展。

<br/>**参见**

CREATE ROLE, DROP ROLE, ALTER DATABASE, SET