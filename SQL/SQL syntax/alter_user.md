# ALTER USER（企业版支持）

**命令**
```SQL
ALTER USER name [ [ WITH ] option [ ... ] ]

where option can be:

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

ALTER USER name RENAME TO new_name

ALTER USER name SET configuration_parameter { TO | = } { value | DEFAULT }
ALTER USER name SET configuration_parameter FROM CURRENT
ALTER USER name RESET configuration_parameter
ALTER USER name RESET ALL```

<br/>**描述**

ALTER USER现在是ALTER ROLE命令的别名（即两者相同）。

<br/>**兼容性**

ALTER USER语句是一个EdsSQL的扩展，SQL标准中还没有完成用户定义相关的语法。

<br/>**参见**

ALTER ROLE