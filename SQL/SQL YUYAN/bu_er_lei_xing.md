# 布尔类型

EdsSQL支持SQL标准的 boolean数据类型。

boolean只能有"true"(真)或"false"(假)两个状态， 或第三种"unknown"(未知)状态，用 NULL 表示。

表1 布尔数据类型

| 名称 | 存储格式 | 描述 |
| -- | -- | -- |
| boolean | 1字节 | 真/假 |


"真"值的有效文本值是：

TRUE<br/>
't'<br/>
'true'<br/>
'y'<br/>
'yes'<br/>
'on'<br/>
'1'<br/>

对于"假"，你可以使用下面这些：

FALSE<br/>
'f'<br/>
'false'<br/>
'n'<br/>
'no'<br/>
'off'<br/>
'0'<br/>

前导或尾随空白将被忽略，大小写无关。使用TRUE和FALSE 这样的字眼比较好(也是SQL兼容的用法)。

<br/>**示例**

使用 boolean 类型
```SQL
CREATE TABLE test1 (a boolean, b text);
INSERT INTO test1 VALUES (TRUE, 'sic est');
INSERT INTO test1 VALUES (FALSE, 'non est');
SELECT * FROM test1;
 a |    b
---+---------
 t | sic est
 f | non est

SELECT * FROM test1 WHERE a;
 a |    b
---+---------
 t | sic est```