# CREATE ROLE（企业版支持）

<br/>**命令**
```SQL
CREATE ROLE name [ [ WITH ] option [ ... ] ]

 这里的option可以是：

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
    | IN ROLE role_name [, ...]
    | IN GROUP role_name [, ...]
    | ROLE role_name [, ...]
    | ADMIN role_name [, ...]
    | USER role_name [, ...]
    | SYSID uid
```
<br/>**描述**

CREATE ROLE向一个数据库集群添加一个新角色。 一个角色是一个可以拥有数据库对象并且拥有数据库权限的实体； 角色可以认为是一个"用户"、一个"组"、或者是两者兼有。 获得有关管理用户和认证的信息。要使用这条命令，你必须拥有CREATEROLE 权限或者是数据库超级用户。

请注意角色是在数据库集群的范畴里定义的，因此对数据库集群里的所有数据库都有效。

<br/>**参数**

*name*：新角色的名称。

SUPERUSER<br/>
NOSUPERUSER

这些子句决定一个新角色是否为"超级用户"，这种用户可以超越数据库中的所有访问权限。 超级用户状态是非常危险的，除非真正需要，否则不应该使用。 你自己必须是一个超级用户才能创建一个新的超级用户。如果没有指定， NOSUPERUSER将是缺省。

CREATEDB<br/>
NOCREATEDB

这些子句定义一个角色是否能创建数据库。如果声明了CREATEDB， 那么正在创建的角色可以创建新数据库。声明NOCREATEDB 将不会赋与新角色创建数据库的能力。如果没有声明，那么缺省是NOCREATEDB。

CREATEROLE<br/>
NOCREATEROLE

这些子句决定一个角色是否可以创建新角色(也就是执行CREATE ROLE)。 一个拥有CREATEROLE权限的角色也可以修改和删除其它角色。 如果没有声明，缺省是NOCREATEROLE。

CREATEUSER<br/>
NOCREATEUSER

这些子句已经过时了，但是仍然接受，拼法为SUPERUSER和 NOSUPERUSER。请注意它们并不等于 CREATEROLE！人们很可能轻易地这么认为。

INHERIT<br/>
NOINHERIT

这些子句决定一个角色是否"继承"它所在组的角色的权限。 一个带有INHERIT属性的角色可以自动使用已经赋与它直接或间接所在组的任何权限。 没有INHERIT，其它角色的成员关系只赋与该角色SET ROLE 成其它角色的能力；其它角色的权限只是在这么做了之后才能获得。如果没有声明， 缺省是INHERIT。

LOGIN<br/>
NOLOGIN

这些子句决定一个角色是否可以登录；也就是说， 该角色在客户端连接的时候是否可以被给予初始化会话的认证名字。 一个拥有LOGIN属性的角色可以认为是一个用户。 没有这个属性的角色可以用于管理数据库权限，但是并不是平常概念的用户。 如果没有声明，除非是调用CREATE ROLE的别名CREATE USER， 否则缺省将是NOLOGIN。

REPLICATION<br/>
NOREPLICATION

这些子句决定一个角色是否可以初始化流复制或是否将系统放在备份模式。 一个拥有REPLICATION属性的角色是一个非常高特权的用户， 应该只用于实际用于复制的用户。如果没有声明，缺省是NOREPLICATION。

CONNECTION LIMIT *connlimit*：如果角色可以登录，这个参数声明该角色可以使用的并发连接数量。-1(缺省)意味着没有限制。

PASSWORD *password*：设置角色的口令。（口令只对那些拥有LOGIN属性的角色有意义， 不过你当然可以给没有这个属性的用户定义口令。）如果你不准备使用口令认证， 你可以忽略这个选项。如果没有指定口令，口令将被设为空并且该用户的密码认证将总是失败。 也可以明确的用PASSWORD NULL指定口令为空。

ENCRYPTED<br/>
UNENCRYPTED

这些关键字控制存储在系统表里面的口令是否加密。（如果没有指定， 那么缺省的行为由配置参数password_encryption控制。） 因为系统无法对指定的加密口令字符串进行解密，所以如果目前的口令字符串已经是用 MD5 加密的格式， 那么那就会继续照此存放，而不管是否声明了ENCRYPTED或UNENCRYPTED。 这样就允许在转储/回复的时候重新加载加密的口令。

请注意老的客户端可能缺乏对 MD5 认证机制的支持，以密文形式存储的口令需要这个机制来运作。

VALID UNTIL *'timestamp'*：VALID UNTIL子句设置角色的口令失效的时间戳。 如果忽略了这个子句，那么口令将永远有效。

IN ROLE *role_name*：IN ROLE子句列出一个或多个现有的角色，新角色将立即加入这些角色， 成为它们的成员。请注意没有任何选项可以把新角色添加为管理员； 必须使用独立的GRANT命令来做这件事情。

IN GROUP *role_name*：IN GROUP是IN ROLE的过时的拼法。

ROLE *role_name*：ROLE子句列出一个或多个现有的角色，它们将自动添加为这个新角色的成员。 这个动作实际上就是把新角色做成一个"组"。

ADMIN *role_name*：ADMIN子句类似ROLE，只是给出的角色被增加到新角色 WITH ADMIN OPTION，给他们以把这个角色的成员权限赋与其它角色的权力。

USER *role_name*：USER子句是ROLE子句的过时拼法。

SYSID *uid*：SYSID子句将被忽略，但是仍然接受之，主要为了向下兼容。

<br/>**注意**

使用ALTER ROLE改变一个角色的属性， 使用DROP ROLE删除一个角色。所有在CREATE ROLE 里声明的属性都可以在随后的ALTER ROLE命令里修改。

给一个当作组来使用的角色添加或删除成员的比较好的方法是使用 GRANT和REVOKE。

VALID UNTIL子句只是为口令定义一个失效时限，而不是角色自身的失效时限。 特别要注意的是，当以非口令为基础的认证方式登录的时候，这个失效时间将失去意义。

INHERIT属性管理那些可赋予的权限的继承关系， 也就是数据库对象的访问权限和角色成员的关系。它并不适用于CREATE ROLE 和ALTER ROLE设置的特殊角色属性。比如，做为一个带有CREATEDB 权限的角色成员，并不直接拥有创建数据库的能力，即使设置了INHERIT也如此； 该角色还是需要通过SET ROLE成为该角色，然后才能创建数据库。

INHERIT属性是缺省的，原因是为了向下兼容：在以前的 EdsSQL版本里，用户总是拥有他们所在组的所有权限。 不过，NOINHERIT提供了与 SQL 标准所定义的最接近的语意。

要注意CREATEROLE权限，因为对于CREATEROLE权限不存在继承的概念。 拥有这个权限的角色可以很容易的创建一个与自身权限不同的非超级用户角色。例如， 如果"user"角色拥有CREATEROLE权限而无CREATEDB权限， 那么他可以创建带有CREATEDB权限的新角色。因此具有CREATEROLE 权限的角色几乎相当于一个超级用户。

EdsSQL包括一个和CREATE ROLE 有一样功能的createuser程序(实际上就是调用这个命令)， 可以从命令行 shell 上运行。

CONNECTION LIMIT选项只是近似地强制；如果两个新的会话几乎同时启动， 而该角色只剩下一个"连接槽"，那么很可能两个连接都会失败。同样， 这个限制从来不会对超级用户强制。

在这个指令中指定未加密的口令时必须小心。口令将被以明文方式传递给服务器， 同时还可能在客户端的命令历史或服务端日志中被记录。 而createuser会将命令加密后传递给服务器。同样， psql包含一个\password可以用来安全的修改口令。

<br/>**示例**

创建一个可以登录的角色，但是不给他设置口令：
```SQL
CREATE ROLE jonathan LOGIN;
```
创建一个带口令的角色：
```SQL
CREATE USER davide WITH PASSWORD 'jw8s0F4';
(CREATE USER和CREATE ROLE一样，只不过它隐含LOGIN。)
```
创建一个带口令的角色，口令有效期到 2004 年底。在 2005 年跑了一秒之后， 口令就不再有效。
```SQL
CREATE ROLE miriam WITH LOGIN PASSWORD 'jw8s0F4' VALID UNTIL '2005-01-01';
```
创建一个可以创建数据库和管理角色的角色：
```SQL
CREATE ROLE admin WITH CREATEDB CREATEROLE;
```
<br/>**兼容性**

CREATE ROLE语句在 SQL 标准里存在，但是标准只要求下面的语法

CREATE ROLE name [ WITH ADMIN role_name ]
多个初始化管理员，以及所有其它CREATE ROLE的选项， 都是EdsSQL扩展。

SQL 标准定义了用户和角色的概念，但是它认为这两个概念是完全独立的概念， 并且要求定义用户的所有命令都针对每个数据库实现。在EdsSQL 里选择了把用户和角色统一成一类对象。因此角色比标准里的有更多的属性。

SQL 标准里声明的行为非常接近于给予用户NOINHERIT属性， 而给予角色INHERIT属性。

<br/>**参见**

<!--SET ROLE, -->ALTER ROLE, DROP ROLE<!--, GRANT, REVOKE, createuser-->