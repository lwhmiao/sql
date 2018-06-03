#Sql语句

###1.查找指定条件的信息

```sql
select auth_name from authors 

select id,name from member 
where id not between 120 and 123

select * from student
where name like '阿_' /like '{刘-王}%'  

select * from student
where name is not null  

select cno, name from student
where name = '阿%' and sex = '' or ''=''

select cno, name from student  
where name = ''or 1=1--' and sex = '' or ''='' 

select cno, name from student  
where name = 'fshugfu' and sex = '' or 1=1;
drop table student -- '

--查询每门课与间接先修课
select A.cno,  B.cono
from course A, course B
where A.cono = B.cno

--降序
select  * from authors
order by auth_gender desc,auth_id desc
```

### 2.取别名

```sql
select student as ....
--去掉重复的值
distinct 
```

### 3.求相应值

```sql
--计数
select count(distinct sex) from member
--平均值
select avg(auth_gender) from authors
--取最大值
select top 1 auth_gender from authors 
order by auth_gender desc
--每一列所选值的人数（分组）
select auth_gender,count(*) 
from authors
group by auth_gender
--从中选取大于1的人数
select auth_gender,count(*) 
from authors
group by auth_gender
having count(*) >1
--where和having的区别：where是对整个表的数据做筛选，having是对分组的结果做筛选
```

### 4.连接运算

**内连接** (INNER JOIN)：结果集仅包含那些满足连接条件的数据行；使用INNER JOIN 连接运算符，使用ON 关键字指定连接条件。
**左外连接**( LEFT [OUTER] JOIN)：左外连接对连接条件中左边的表不加限制；
**右外连接**( RIGHT [OUTER] JOIN)：右外连接对连接条件中右边的表不加限制；
**全外连接**( FULL [OUTER] JOIN)：全外连接对两个表都不加限制，所有两个表中的行都会包括在结果集中；
**交叉连接**( CROSS JOIN)：称为笛卡尔成绩，返回两个表的乘积，结果集中包含了所连接的两个表中所有行的全部组合；

```sql
--内连接
select Sname, Cno, Grade
from Student inner join SC
on Student.Sno = SC.Sno and Cno = '1'
--左外连接
select member.id, name, books
from member right join book
on member.id = book.id
```

### 5.嵌套查询

```sql
--嵌套查询
select * from member 
where info = (select info from member
              where name='胡')
--不用嵌套查询
select member.id, name, books
from member,book
where member.id = book.id and books = '3'
--用嵌套查询
select id, name from member
where id in (select id from book
             where books='3')
--并集 union
--交集 intersect
--差集 except
select * from student
union/intersect/excpet
select * from student where id ='323'
```

### 6.数据的更新与删除

```sql
--同时插入多行
insert into ...  values
('2017002' , '女' ,null) 
('2017002' , '男' ,null) 
--把查询结果直接插入到另一个表中(前提是表存在)
insert into ... (select ...)
--直接插入(表可以不存在)
select ... into 表名
from ...
group by ...
--更新
update 表名
 set ...
where ...
--删除
delete from ... 
drop table ...
truncate table ...--不会触发触发器执行
```

### 7.三级模式

- 模式：全体数据(基本表)的集合，只能有一个
- 外模式：模式的子集，可以有多个
- 内模式：


