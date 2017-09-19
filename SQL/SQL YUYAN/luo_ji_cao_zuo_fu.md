# 逻辑操作符

常用的逻辑操作符有：

AND<br/>
OR<br/>
NOT

SQL使用三值的逻辑体系，真，假和null， 这时null代表"未知"。观察下面真值表：

| a | b | a AND b | a OR b |
| -- | -- | -- | -- |
| TRUE | TRUE | TRUE | TRUE |
| TRUE | FALSE | FALSE | TRUE |
| TRUE | NULL | NULL | TRUE |
| FALSE | FALSE | FALSE | FALSE |
| FALSE | NULL | FALSE | NULL |
| NULL | NULL | NULL | NULL |

| a | NOT a |
| -- | -- |
| TRUE | FALSE |
| FALSE | TRUE |
| NULL | NULL |


操作符AND和OR都是可交换的，也就是说， 你可以交换左右操作数而不影响结果。