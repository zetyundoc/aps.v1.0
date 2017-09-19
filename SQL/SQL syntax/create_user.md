# CREATE USER
<br/>**命令**
```SQL
CREATE USER name [ [ WITH ] option [ ... ] ]

 这里的option可以是:

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

CREATE USER现在是CREATE ROLE的一个别名。 唯一的区别是CREATE USER命令缺省是LOGIN， 而CREATE ROLE命令缺省是NOLOGIN。

<br/>**兼容性**

CREATE USER语句是EdsSQL的一个扩展。 SQL 标准把用户的定义交给了实现来完成。

<br/>**参见**

CREATE ROLE