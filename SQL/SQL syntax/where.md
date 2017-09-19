# WHERE

可选的WHERE条件有如下常见的形式：
```SQL
WHERE condition
```
这里condition可以是任意生成类型为 boolean的表达式。任何不满足这个条件的行都会从输出中删除。 如果一个行的数值代入到条件中计算出来的结果为真，那么该行就算满足条件。

<br/>**示例**

```SQL
SELECT Column1
FROM table1
WHERE column2 <= 1;
```

<br/>**参见**

SELECT