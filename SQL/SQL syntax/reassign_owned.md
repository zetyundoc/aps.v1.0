# REASSIGN OWNED（企业版支持）
<br/>**命令**
```SQL
REASSIGN OWNED BY old_role [, ...] TO new_role
```
<br/>**描述**

REASSIGN OWNED要求系统将所有 old_roles 拥有的数据库对象的属主更改为 new_role 。

<br/>**参数**

*old_role*：旧属主的角色名。当前数据库和所有共享对象(数据库，表空间) 中该角色所拥有的所有对象的属主将改为new_role。

*new_role*：将要成为这些对象属主的新角色的名字

<br/>**注意**

REASSIGN OWNED常用于在删除角色之前的准备工作。因为REASSIGN OWNED 不影响其他数据库中的对象，所以必须在即将删除的角色拥有对象的每一个数据库中执行该命令。

REASSIGN OWNED要求原角色和目标角色上的权限。

DROP OWNED可以用来删除角色所拥有的所有对象。 还请注意DROP OWNED只需要原角色的权限。

REASSIGN OWNED并不影响 old_roles 在不被其拥有的对象上的权限。 使用DROP OWNED来删除这些权限。

<br/>**兼容性**

REASSIGN OWNED语句是一个EdsSQL扩展。

<br/>**参见**

DROP OWNED, DROP ROLE, ALTER DATABASE
