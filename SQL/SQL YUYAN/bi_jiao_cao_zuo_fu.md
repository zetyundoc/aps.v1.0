# 比较操作符

可用的比较操作符在 表 1 中显示。

表 1 比较操作符

| 操作符 | 描述 |
| -- | -- |
| < | 小于 |
| > | 大于 |
| <= | 小于或等于 |
| >= | 大于或等于 |
| = | 等于 |
| <>或!= | 不等于|

<p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >注意: !=操作符在分析器阶段被转换成<>。 !=和<>操作符是完全等价的。</p>

比较操作符可以用于所有相关的数据类型。所有比较操作符都是双目操作符， 返回boolean类型数值；像1 < 2 < 3 这样的表达式是非法的(因为布尔值和3之间不能做比较)。

除了比较操作符以外，我们还可以使用BETWEEN构造。
```SQL
a BETWEEN x AND y
```
等效于
```SQL
a >= x AND a <= y
```
注意BETWEEN认为端点值是包含在范围内的。NOT BETWEEN 做相反的比较：
```SQL
a NOT BETWEEN x AND y
```
等效于
```SQL
a < x OR a > y
```
BETWEEN SYMMETRIC和BETWEEN一样，只是没有要求 AND左边的参数小于或等于右边的参数。如果左面的参数不小于或等于右面的参数， 那么两个参数是自动交换的，所以非空范围总是适用。

要检查一个值是否为 NULL ，使用下面的构造：
```
expression IS NULL
expression IS NOT NULL
```
或者等效，但并不标准的构造：
```SQL
expression ISNULL
expression NOTNULL
```
不要写*expression* = NULL 因为NULL是不"等于"NULL的。 NULL 代表一个未知的数值，因此我们无法知道两个未知的数值是否相等。这个行为遵循 SQL 标准。

<p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >提示: 有些应用可能要求表达式expression = NULL 在expression为 NULL 时候返回真。 我们强烈建议这样的应用修改成遵循 SQL 标准。但是，如果这样修改是不可能的， 那么我们可以打开transform_null_equals配置参数， 让EdsSQL将x = NULL 自动转换成x IS NULL。</p>

<p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >注意: 如果expression是行值， 那么当行表达式本身为 NULL 或该行的所有字段都为 NULL 时，IS NULL将为真； 当行表达式本身不为 NULL 并且该行的所有字段都不为 NULL 时，IS NOT NULL 也将为真。因为这个行为，IS NULL和IS NOT NULL 并不总是为行值表达式返回相反的值，也就是， 一个同时包含NULL和non-null值的行值表达式将在两种情况下都返回false。</p>

如果有任何一个输入是 NULL ，那么普通的比较操作符生成 NULL(表示"未知")， 而不是true或false。例如，7 = NULL生成null，7 <> NULL 也生成null。当这种行为不适用时，使用IS [ NOT ] DISTINCT FROM构造：
```SQL
expression IS DISTINCT FROM expression
expression IS NOT DISTINCT FROM expression
```
对于非 NULL 的输入IS DISTINCT FROM与<> 操作符相同。但是，如果两个输入都是 NULL ，那么它将返回假；如果只有一个输入是 NULL ， 那么它将返回真。类似的，对于非 NULL 的输入IS NOT DISTINCT FROM 与=操作符相同。但是，如果两个输入都是 NULL ，那么它将返回真； 如果只有一个输入是 NULL ，那么它将返回假。这样就很有效地把 NULL 当作一个普通数据值看待， 而不是"未知"。

布尔数值可以用下面的构造进行测试
```SQL
expression IS TRUE
expression IS NOT TRUE
expression IS FALSE
expression IS NOT FALSE
expression IS UNKNOWN
expression IS NOT UNKNOWN
```
这些构造将总是返回真或假，从来不返回 NULL ，即使操作数是 NULL 也如此。 NULL 输入被当做逻辑数值"未知"。请注意实际上IS UNKNOWN 和IS NOT UNKNOWN分别与IS NULL和IS NOT NULL 相同，只是输入表达式必须是布尔类型。