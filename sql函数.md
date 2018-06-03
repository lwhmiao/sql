# Sql函数

### 1.字符串函数

```sql
--ASCII()：查看指定字符的ASCII值
select ASCII('a') ,ASCII ('ba') , ASCII(11)
--char()：将ASCII值转换为字符
select char(97)
--left()：返回左边字符串左边开始的指定个数的字符
select left('English' , 3)
--right()：返回右边字符串左边开始的指定个数的字符
select right('English' , 3)
--ltrim()：去除字符串左边多余的空格
select ltrim('  look  ')
--rtrim()：去除字符串右边多余的空格
select rtrim('  look  ')
--str()：指定小数返回
select str(3.14159,6,4)--6指6位数，4指小数位为4
--reverse()：字符串逆序
select reverse('xyz')
--len()：计算字符串长度
select len('shdah')
--charindex()：匹配子字符串开始位置
select charindex('b','abc')
--substring()：返回指定的子字符串
select substring('book',1,3)--从1到3的子字符串
--lower()：将大写转为小写
select lower('AhashD')
--upper()：将小写转为大写
select upper('facad')
--replace()：替换某字符串
select replace('xyxyxy','x','y')--将x换为y
```

### 2.数学函数

```sql
--ABS()：绝对值函数
select ABS(-3.3)
--sqrt()：平方根函数
select sqrt(9)
--rand()：产生随机数(带参数的rand每次返回值相同)
select rand(),rand(1),rand(1)
--round()：四舍五入函数
select round(1.465,1)--1指小数位数
--sign()：符号函数(负，零，正分别返回-1，0，1)
select sign(-22) , sign(0) , sign(2121)
--ceiling():返回不小于该数的最小整数
select ceiling(6.8)
--floor()：返回不大于该数的最大整数
select floor(6.8)
--power()：乘方运算
select power(2,3)--2的3次方
--square()：平方运算
select square(8)
--exp()：e的乘方运算
select exp(4)
--log()：计算自然对数
select log(2.7)
--log10()：计算基数为10的对数
select log10(10)
--radians()：角度转为弧度
select radians(90.0)
--degrees()：弧度转为角度
select degrees(PI())
--sin()：计算正弦值
select sin(PI()/2)
--asin()：计算反正弦值
select asin(1)
--cos()：计算余弦值
select cos(PI())
--acos()：计算反余弦值
select acos(-1)
--tan()：计算正切值
select tan(0)
--atan()：计算反正切值
select atan(PI()/2)
--cot()：计算余切值
select cot(0)
```

### 3.数据类型转换函数

```sql
--cast()：数据类型转换函数
select cast('20180917' as DATE)
```

### 4.日期时间函数

```sql
--getdate()：返回当前日期时间
select getdate()
--getutcdate()：返回UTC(世界标准时间)
select getutcdate()
--day()：获取天数
select day('2018-08-07')
--month()：获取月数
select month('2018-08-07')
--year()：获取年份
select year('2018-08-07')
--datename()：返回日期中相应部分的值(字符串类型)
select datename(weekday ,'2018-04-21') , datename(dayofyear , '2018-04-21')
--datepart()：返回日期中相应部分的值(整型)
select datepart(weekday ,'2018-04-21') , datepart(dayofyear , '2018-04-21')
--dateadd()：计算日期和时间
select dateadd(year ,1,'2018-09-19')--年份加1
--DateDiff()：计算两个日期相差的天数
select DateDiff(d , GetDate() , '2018-06-01')
```

### 5.系统函数

```sql
--col_lengeh()：返回表中指定字段的长度
select col_length('Book','bkID')
--col_name()：返回表中指定字段的名称
select col_name(OBJECT_ID('Borrow'),2)
--datalength()：返回数据表达式的数据实际长度
select datalength(bkID) from Borrow 
where rdId = 'rd2017001'
--DB_ID()：返回数据库的编号
select DB_ID('master'),DB_ID('BooksDB')
--DB_NAME()：返回数据库的名称
select DB_NAME()
--getansinull()：返回当前数据库默认是否允许空值(数据库为空或没有显式定义或数据类型为空，返回0)
select  getansinull()
--HOST_ID()：返回服务端计算机标识号
select HOST_ID()
--HOST_NAME()：返回服务端计算机名称
select HOST_NAME()
--SUSER_SID()：返回用户的SID(安全标识号)
select SUSER_SID('sa')
--SUSER_NAME()：返回用户的登录名
select SUSER_NAME()
--OBJECT_ID()：返回数据库对象的编号
select OBJECT_ID('Book')
--OBJECT_NAME()：返回数据库对象名称
select OBJECT_NAME(357576312)
--USER_ID()：返回数据库用户的标识号
select USER_ID()
--USER_NAME()：返回数据库用户名
select USER_NAME()
```



