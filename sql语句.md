## Sql语句

select auth_name from authors 

select id,name from member 
where id not between 120 and 123 

select * from student
where name like '阿_' /like '{刘-王}%'    

select * from student
where name is not null  

取别名
select student as ....

去掉重复的值
distinct 

select cno, name from student
where name = '阿%' and sex = '' or ''=''

select cno, name from student  
where name = ''or 1=1--' and sex = '' or ''='' 

select cno, name from student  
where name = 'fshugfu' and sex = '' or 1=1;drop table student -- '

select  * from authors
order by auth_gender desc,auth_id desc

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

--左外连接
select member.id, name, books
from member right join book
on member.id = book.id

--查询每门课与间接先修课
select A.cno,  B.cono
from course A, course B
where A.cono = B.cno

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