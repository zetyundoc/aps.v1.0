# SHOW（企业版支持）
<br/>**命令**
```SQL
SHOW name
SHOW ALL
```
<br/>**描述**

SHOW将显示当前运行时参数的数值。<!--这些变量可以通过 SET语句、编辑postgresql.conf文件、 PGOPTIONS环境变量(在使用基于libpq的应用程序的时候)、 启动postgres服务器的命令行参数来设置。-->

<br/>**参数**

*name*：运行时参数的名称。可用的参数在SET 手册页里面有文档。另外，还有一些参数可以显示，但是不能设置：

<ul><li>SERVER_VERSION：显示服务器的版本号。</li>

<li>SERVER_ENCODING：显示服务器端的字符集编码。目前，这个参数只能显示但不能设置， 因为编码是在创建数据库的时候决定的。</li>

<li>LC_COLLATE：显示数据库的排序规则区域设置(文本顺序)。目前，这个参数只能显示但不能设置， 因为它是在数据库创建的时候设置的。</li>

<li>LC_CTYPE：显示数据库字符集分类的区域设置。目前，这个参数只能显示但不能设置， 因为它是在数据库创建的时候设置的。</li>

<li>IS_SUPERUSER：如果当前角色拥有超级用户权限，则为真。</li></ul>

ALL：显示所有配置参数值以及其描述。

<br/>**注意**

函数current_setting生成相同的输出。还有， eds_settings 系统视图生成同样的信息。

<br/>**示例**

显示参数DateStyle的当前设置：
```SQL
SHOW DateStyle;
 DateStyle
-----------
 ISO, MDY
(1 row)
```
显示参数geqo的当前设置：
```SQL
SHOW geqo;
 geqo
------
 on
(1 row)
```
显示所有设置：
```SQL
SHOW ALL;
            name         | setting |                description                                                          
-------------------------+---------+-------------------------------------------------
 allow_system_table_mods | off     | Allows modifications of the structure of ...
    .
    .
    .
 xmloption               | content | Sets whether XML data in implicit parsing ...
 zero_damaged_pages      | off     | Continues processing past damaged page headers.
(196 rows)
```
<br/>**兼容性**

SHOW命令是EdsSQL扩展。

<br/>**参见**

SET<!--, RESET-->