# 数学函数和操作符

EdsSQL为许多类型提供了数学操作符。 对于那些没有标准的数学传统的类型(比如日期/时间类型)， 我们在随后的章节里描述实际的行为。

表-1显示了可用的数学操作符。

表-1 数学操作符

| 操作符 | 描述 | 例子 | 结果 |
| -- | -- | -- | -- |
| + | 加 | 2 + 3 | 5 |
| - | 减 | 2 - 3 | -1 |
| * | 乘 | 2 * 3 | 6 |
| / | 除(整数除法将截断结果) | 4 / 2 | 2 |
| % | 模(求余) | 5 % 4 | 1 |
| ^ | 幂(指数运算) | 2.0 ^ 3.0 | 8 |
| I/ | 平方根 | I/ 25.0 | 5 |
| II/ | 立方根 | II/ 27.0 | 3 |
| ! | 阶乘 | 5 ! | 120 |
| !! | 阶乘(前缀操作符) | !! 5 | 120 |
| @ | 绝对值 | @ -5.0 | 5 |
| & | 二进制 AND | 91 & 15 | 11 |
| I | 二进制 OR | 32 I 3 | 35 |
| # | 二进制 XOR | 17 # 5 | 20 |
| ~ | 二进制 NOT | ~1 | -2 |
| << | 二进制左移 | 1 << 4 | 16 |
| >> | 二进制右移 | 8 >> 2 | 2 |
	
位操作符只能用于整数类型，而其它的操作符可以用于全部数值类型。 位操作符还可以用于位串类型bit和bit varying， 参见位串函数和操作符。

表-2显示了可用的数学函数。在该表中， dp表示double precision。 这些函数中有许多都有多种不同的形式，区别是参数不同。除非特别指明， 任何特定形式的函数都返回和它的参数相同的数据类型。处理double precision 数据的函数大多数是在宿主系统的C库的基础上实现的；因此， 精度和数值范围方面的行为是根据宿主系统而变化的。

表-2 数学函数

|函数|	返回类型|	描述|	例子|	结果|
| -- | -- | -- | -- | -- |
|abs(x)|	(与输入相同)|	绝对值|	abs(-17.4)|	17.4|
|cbrt(dp)|	dp	|立方根	|cbrt(27.0)|	3|
|ceil(dp 或 numeric)|	(与输入相同)|	不小于参数的最小的整数|	ceil(-42.8)|	-42|
|ceiling(dp 或 numeric)|	(与输入相同)|	不小于参数的最小整数(ceil的别名)|	ceiling(-95.3)|	-95|
|degrees(dp)|	dp|	把弧度转为角度|	degrees(0.5)|	28.6478897565412|
|div(y numeric, x numeric)|	numeric|	integer quotient of y/x	|div(9,4)|	2|
|exp(dp 或 numeric)	|(与输入相同)|	自然指数|	exp(1.0)|	2.71828182845905|
|floor(dp 或 numeric)|	(与输入相同)|	不大于参数的最大整数|	floor(-42.8)|	-43|
|ln(dp 或 numeric)|	(与输入相同)|	自然对数|	ln(2.0)|	0.693147180559945
|log(dp 或 numeric)	|(与输入相同)|	以 10 为底的对数|	log(100.0)|	2
|log(b numeric, x numeric)|	numeric	|以b为底数的对数|	log(2.0, 64.0)|	6.0000000000|
|mod(y, x)|	(与参数类型相同)|	y/x的余数(模)|	mod(9,4)|	1|
|pi()|	dp|	"π" 常量|	pi()|	3.14159265358979|
|power(a dp, b dp)|	dp|	a的b次幂|	power(9.0, 3.0)	|729|
|power(a numeric, b numeric)|	numeric	|a的b次幂|	power(9.0, 3.0)|729|
|radians(dp)|	dp|	把角度转为弧度|	radians(45.0)|	0.785398163397448|
|random()|	dp|	0.0 到 1.0 之间的随机数|	random()| |	 
|round(dp 或 numeric)|	(与输入相同)|	圆整为最接近的整数|	round(42.4)|42|
|round(v numeric, s int)|	numeric|	圆整为s 位小数|	round(42.4382, 2)|	42.44|
|setseed(dp)|	void|	为随后的random()调用设置种子( -1.0 到 1.0 之间，包含)|	setseed(0.54823)| |	 
|sign(dp 或 numeric)|	(与输入相同)|	参数的符号(-1, 0, +1)|	sign(-8.4)|	-1|
|sqrt(dp 或 numeric)|	(与输入相同)|	平方根|	sqrt(2.0)|	1.4142135623731|
|trunc(dp 或 numeric)|	(与输入相同)|	截断(向零靠近)|	trunc(42.8)|	42|
|trunc(v numeric, s int)|	numeric	|截断为s位小数|	trunc(42.4382, 2)|	42.43|
|width_bucket(op numeric, b1 numeric, b2 numeric, count int)|	int|	返回一个桶，这个桶是在一个有count个桶， 上界为b1下界为b2的等深柱图中 operand将被赋予的那个桶。|	width_bucket(5.35, 0.024, 10.06, 5)|	3|
|width_bucket(op dp, b1 dp, b2 dp, count int)|	int|	返回一个桶，这个桶是在一个有count个桶， 上界为b1下界为b2的等深柱图中 operand将被赋予的那个桶。|	width_bucket(5.35, 0.024, 10.06, 5)|	3|

最后，表-3显示了可用的三角函数。 所有三角函数都使用类型为double precision 的参数和返回类型。 三角函数参数用弧度来表达。反函数的返回值也是用弧度来表达的。 参阅上面的单元转换函数radians() 和degrees()。

表-3 三角函数

| 函数 | 描述 |
| -- | -- |
| acos(x) | 反余弦 |
| asin(x) | 反正弦 |
| atan(x) | 反正切 |
| atan2(y, x) | y/x的反正切 |
| cos(x) | 余弦 |
| cot(x) | 余切 |
| sin(x) | 正弦 |
| tan(x) | 正切 |
