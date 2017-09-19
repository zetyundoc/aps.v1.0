# WITH

WITH子句允许声明一个或多个可以在主查询中通过名字引用的子查询。

子查询在主查询期间有效的充当临时表或视图。每个子查询可以是SELECT, VALUES, INSERT<!--, UPDATE 或 DELETE-->语句。当在WITH中写一个数据修改语句时 （<!--INSERT或 DELETE-->）， 通常包含一个RETURNING子句。RETURNING的输出而不是 语句修改的底层表的输出形成被主查询读取的临时表。如果省略了RETURNING， 该语句仍然执行，但是不会产生输出，所以不能作为一个表被主查询引用。

必须为每个WITH查询声明一个名字（没有模式修饰）。 可选的，可以指定字段名的列表；如果省略了，那么字段名从子查询中推断出。

如果声明了RECURSIVE，那么允许SELECT子查询通过名字引用它自己。 比如一个有下面形式的子查询：
```SQL
non_recursive_term UNION [ ALL | DISTINCT ] recursive_term
```
递归的自我引用必须在UNION的右边出现。每个查询只允许一个递归的自我引用。 不支持递归的数据修改语句，但是可以在数据修改语句中使用递归的SELECT 查询的结果。

RECURSIVE的另外一个作用是WITH查询不需要排序： 一个查询可以引用另外一个稍后出现在列表中的查询。（但是，循环引用或相互递归没有实现。） 没有RECURSIVE，WITH查询只能引用 更早出现在WITH列表中的同层级的WITH查询。

WITH查询的主要特性是他们只在主查询的每次执行中评估一次， 即使主查询引用了他们多次也是如此。特别的，保证数据修改语句只被执行一次， 不管主查询读取他们的所有或任意的输出。

主查询和WITH查询（理论上）同时执行。这意味着WITH 中的数据修改语句的影响不能从查询的其他部分看到，除非读取它的RETURNING 输出。如果两个这样的数据修改语句尝试修改相同的行，那么结果是未知的。

<br/>**示例**

这个例子显示了如何使用一个简单的WITH子句：
```SQL
WITH t AS (
    SELECT random() as x FROM generate_series(1, 3)
  )
SELECT * FROM t
UNION ALL
SELECT * FROM t

         x          
--------------------
  0.534150459803641
  0.520092216785997
 0.0735620250925422
  0.534150459803641
  0.520092216785997
 0.0735620250925422
```
请注意WITH查询只评估一次，所以我们获得两组相同的三个随机值。

这个例子使用WITH RECURSIVE 从一个只显示直接属下的表中找到所有职员Mary的属下（直接或间接），和他们的间接级别，
```SQL
WITH RECURSIVE employee_recursive(distance, employee_name, manager_name) AS (
    SELECT 1, employee_name, manager_name
    FROM employee
    WHERE manager_name = 'Mary'
  UNION ALL
    SELECT er.distance + 1, e.employee_name, e.manager_name
    FROM employee_recursive er, employee e
    WHERE er.employee_name = e.manager_name
  )
SELECT distance, employee_name FROM employee_recursive;
```
请注意递归查询的典型形式：一个初始条件，紧接着是UNION， 然后是查询的递归部分。确定查询的递归部分最终将不会返回元组，否则查询将无限循环下去。 

<br/>**兼容性**

**WITH中的数据修改语句**

EdsSQL允许INSERT。这在SQL标准中是没有的。

<br/>**参见**

SELECT