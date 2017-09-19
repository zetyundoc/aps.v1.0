# SET ROLE（企业版支持）
<br/>**命令**
```SQL
SET [ SESSION | LOCAL ] ROLE role_name
SET [ SESSION | LOCAL ] ROLE NONE
RESET ROLE
```
<br/>**描述**

这条命令将当前会话的当前用户标识为*role_name*。 角色名可以写成表识符或者是字符串文本。在SET ROLE之后， SQL命令的权限检查就是针对这个用户了，就像当初用这个用户登录一样。

当前会话的用户必须是指定的*role_name* 角色的成员。但超级用户可以选择任何角色。

SESSION和LOCAL修饰词的作用和普通的SET命令一样。

NONE和RESET形式重置当前用户标识为当前会话用户标识符。任何用户都可以执行这种形式。

<br/>**注意**

使用这条命令，它可能会增加一个用户的权限，也可能会限制一个用户的权限。 如果会话用户的角色有INHERITS属性，那么它自动拥有它能SET ROLE 变成的角色的所有权限；在这种情况下，SET ROLE实际上是删除了所有直接赋予会话用户的权限， 以及它的所属角色的权限，只剩下指定角色的权限。另一方面，如果会话用户的角色有 NOINHERITS属性，SET ROLE删除直接赋予会话用户的权限，而获取指定角色的权限。

实际上，如果一个超级用户SET ROLE为一个非超级用户，它会失去其超级用户权限。

SET ROLE有和SET SESSION AUTHORIZATION类似的效果， 但是其中涉及的权限检查有区别。还有，SET SESSION AUTHORIZATION 决定其后有什么角色可以执行SET ROLE命令，而用SET ROLE 并不修改稍后的SET ROLE可以设置的角色集。

SET ROLE并不如同角色的ALTER ROLE设置所声明的那样处理会话变量； 这只在登陆时发生。

SET ROLE不能在SECURITY DEFINER函数内使用。

<br/>**示例**
```SQL
SELECT SESSION_USER, CURRENT_USER;

 session_user | current_user 
--------------+--------------
 peter        | peter

SET ROLE 'paul';

SELECT SESSION_USER, CURRENT_USER;

 session_user | current_user 
--------------+--------------
 peter        | paul
```
<br/>**兼容性**

EdsSQL允许标识符语法("rolename")， 而SQL标准要求角色名字写成字符串文本。 SQL并不允许在事务里面执行这条命令； EdsSQL并未做此限制，因为没有理由限制。 SESSION和LOCAL修饰词是EdsSQL 的扩展，还有RESET语法也一样。

<!--<br/>**参见**

SET SESSION AUTHORIZATION-->