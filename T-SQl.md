# T-SQL

### 1.常量

字符串类型的常量，用单引号括起来

Datetime类型的常量，用单引号括起来

bit类型：0 或 1

### 2.变量

**全局变量(@@...)**

- 以@@开头
- 由系统自定义和维护
- 对用户而言，是只读的
- 在任何时候都是可以访问的

例如，全局变量error可以用来记录最近执行语句的错误号(若无错误，其值为0)，rowcount可以访问最近执行的语句所受影响的行数

**局部变量(@...)**

- 以@开头
- 由用户定义和维护
- 作用域从变量声明的地方开始到变量所在的批处理结束(结束符为go)

声明变量：`declare @abc char(7) ,@age int`

为变量赋值： `set @abc = '2018'`(直接赋值) 或 `select  @abc = abc from 表名`(从表中取值)

显示变量：`print @abc `(在消息列显示) 或 `select @abc`(在结果列显示)

### 3.语句块

begin ... end :相当于C中的{}

while 结构：

```sql
declare @i int ,@sum int
set @i = 1
set @sum = 0
while @i <= 100
begin
    set @sum = @sum + @i
	set @i = @i +1
end
print @sum
```

选择结构：

```sql
--简单case
select bkId,bkName,
   case bkID
       when 'bk2017001' then '2018-5-23'
	   when 'bk2017002' then '2018-4-14'
	   when 'bk2017003' then '2018-6-01'
	   else '未知'
	end as 借出日期
from Book
--搜索case
select bkID , bkName , bkPrice ,
    case
	when bkPrice >35 then '贵'
	when bkPrice >25 then '有点贵'
	else '可以接受'
	end as '价格评价'
from Book
```

```sql
--waitfor：时间控制函数
waitfor time'10:16'
select * from Book
--备份
backup database BooksDB to disk = 'D:\BooksDB'
```

exec(...) ：动态sql，把括号中的代码当成sql语句执行

批处理：一组Sql语句作为一个整体发送到服务器编译，若编译无错误，则会生成一个执行计划(对表名的解析有延迟)

### 4.用户自定义函数

```sql
--标量函数
create function func_GetavgPrice()
returns decimal(5,2)
as
begin 
     declare @avgPrice decimal(5,2)
	 select @avgPrice = avg(bkPrice) from Book 
	 return @avgPrice
end
--表值函数
create function func_GetBook(@rdID char(9))
returns table
as
   return
   select * from Borrow where rdID = @rdID
--调用
select dbo.func_GetavgPrice()
select * from dbo.func_GetBook('rd2017001')
```

### 5.视图

- 是从一个或多个**基本表或视图**中导出的(数据库是存放在基本表中的)
- 是一张虚表，不存储数据
- 可以简化操作
- 对视图的操作都会转换为对基本表的操作(增删改查)
- 增加了数据的安全性
- 对应于三级模式中的外模式

```sql
--创建
create view 表名
as
   select * from ...
   with check option --约束
--删除
drop view 表名
```

> 并不是所有的视图都可以更新(当涉及到多个表的操作时一般不能更新)

### 6.索引

SQL server中数据存储的基本单位是**页**，页是SQL server可以读写的最小基本单位，其大小为8KB。根据所使用的页面类型，可分为数据页、索引页、text、image等。

**定义**：

- 是与表或视图关联的磁盘结构
- 提高查找速度

**聚集索引**(clustered)：把正文内容本身就是按照一定规则有序排列的目录，即逻辑顺序与物理顺序一致，例如字典的正文本身就是一个索引

- 数据行基于聚集索引按顺序存储
- 每个表只有一个聚集索引
- 聚集索引表的B树(根据索引建立的二叉树)的叶子节点直接存放数据

```sql
create table Student
(
	sno char(7) primary key  clustered,
	sname char(7),
	sage int
) 
insert into Student values
('2017006', '安好', 19),
('2017002', '好', 20),
('2017004', '安', 18)
select * from Student
```

**非聚集索引**(nonclustered)：逻辑顺序与物理顺序不一致，由指针指向相应的物理内容

堆表：没有聚集索引的表，其B树的叶子节点存放的是相应数据的地址

```sql
...
	sno char(7) primary key  nonclustered
...
```

**创建索引**

```sql
create clustered index index1 on Student(sage) desc
```

**删除索引**

```sql
drop index Student.index1 
```

### 7.存储过程(SP)

**定义**：完成特定功能的一组sql命令，分为系统存储过程(以`sp_`开头)、扩展存储过程(`xp_`)与用户自定义存储过程(`usp_`)

- 实现模块化编程
- 提高执行速度
- 减少网络流量
- 提高安全性

**创建与调用**

```sql
--创建用户自定义sp
--不带输入参数和输出
--功能：查看计算机科学院学生的信息
create procedure usp_GetReader
as 
  select * from Reader
  where rdDept  = '计算机科学学院'
--调用
execute usp_GetReader
exec usp_GetReader --简写
go
usp_GetReader --简写

--带输入参数，不带输出参数
--功能：查询借了书且为计算机科学学院的学生
create procedure usp_GetCSReader
  @SDept varchar(40)
  with encryption --加密源码
as
  select * from Reader
  where rdDept = @SDept and rdBorrowQty > 0
--调用
usp_GetCSReader '计算机科学学院'
usp_GetCSReader @Sdept = '计算机科学学院'
--查看存储过程
sp_help 'usp_GetCSReader'--查看所属信息
sp_helptext 'usp_GetCSReader' --查看代码

--创建带输入参数和输出参数的存储过程
--功能：从读者表中修改指定学生的信息，同时返回该生的姓名
--输入参数：学号，输出参数：姓名
alter procedure usp_alterReader
  @Rid char(9),
  @Rname varchar(20) output
as 
  select @Rname = rdName  from Reader
  where rdID = @Rid
  update Reader
  set rdType = 3 
  where rdID = @Rid
--调用
declare @name varchar(20)
exec usp_alterReader 'rd2017007',@name output
select  @name as 被修改读者姓名
```

### 8.触发器

定义：是一种特殊的存储过程，是通过事件**触发**而执行(存储过程是由用户调用执行)

触发器可以实现比实体完整性等约束更复杂的约束

**DML触发器**分为：**after触发器**(先向表中修改数据，再触发触发器)

​                                **instead of触发器**(只向触发器表中修改数据)，可用于通过视图修改基本表

- 每个DML触发器都有两个特殊的表：inserted 表和deleted表
- 表是存储在内存中的，不是存储在数据库中
- 表动态驻留在内存中，当触发器工作完成，表被删除
- 两个表的结构总是与该触发器作用的表的结构相同

```sql
--创建after触发器
create trigger tri_insert on student
for insert ,delete ,update--三个事件均可触发
as
  print '触发器执行'
  select * from inserted
  select * from deleted
--触发触发器
insert into student values('2017003', 59)
delete from student where ID = '2017003'
update student set grade = 63
where ID = '2017003'
--删除触发器
drop trigger tri_insert
```

```sql
--创建instead of触发器
create trigger tri_insert2 on student
instead of insert 
as
  print '触发器执行啦啦'
  select * from inserted
  select * from deleted
--触发触发器
insert into student values('2017003', 59)--执行完后student中没有插入'2017003'的数据
--删除触发器
drop trigger tri_insert2
```

向student表插入数据，在avgTable中也有相应更新，代码如下：

```sql
alter trigger tri_avg on student
for insert
as
  declare @id char(10)
  select @id = ID from inserted
  if not exists(select * from avgTable where @id = ID)
    insert into avgTable
	select ID , grade from inserted
  else 
    update avgTable 
	set avGrade = (select avg(grade) from student where @id = ID) 
	where ID = @id
insert into student values('2017004', 65)
insert into student values('2017003', 60)
```

还可用于外键参照的级联删除

**DDL触发器**：作用于数据库

```sql
--创建
create trigger tri_DDL on database
for create_view, drop_table, create_table
as
  print '无法在数据库'+ db_name() +'创建表和删除表'
  rollback --回滚
--测试
create view v1
as
  select * from student
  where ID = '2017001'
drop table avgTable
--create_table ,alter_table, drop_table相当于ddl_table_events

--登录触发器(针对服务器)
create trigger tri_login on all server
for ddl_login_events
as
  rollback
  print '创建用户失败'
--测试
create Login miao with password = 'miaomi'
--禁用触发器
disable trigger all on all server
```

### 9.游标

游标总是与一条`select`语句相关联，游标是由一条`select`语句得到的结果集和指向结果集中特定记录的游标位置组成。使用游标可用逐行地读取`select`结果集。

游标操作分为一下步骤：声明游标，打开游标，读取数据，关闭游标，释放游标资源

```sql
--声明游标
declare curstudent cursor scroll --不加scroll为只进游标
for select ID, grade from student
--打开游标
open curstudent
--读取数据
--Fetch frist|last|prior|next|absolute n|relative n
--from 游标名称
--（into @变量……）
declare @id char(10), @grade int
fetch next from curstudent into @id ,@grade
while @@FETCH_STATUS = 0
begin
   print 'ID = '+@id+',grade = '+cast(@grade as char)
   fetch next from curstudent into @id ,@grade
end
fetch first from curstudent into @id ,@grade
print 'ID = '+@id+',grade = '+cast(@grade as char)
fetch absolute 3 from curstudent into @id ,@grade
print 'ID = '+@id+',grade = '+cast(@grade as char)
--关闭游标
close curstudent
--释放游标资源
deallocate curstudent
--利用游标处理触发器一次插入多条记录
select * from student
select * from avgTable
alter trigger [dbo].[tri_avg] on [dbo].[student]
for insert
as
  declare curgrade cursor for select ID, grade from student
  open curgrade
  declare @id char(10),@grade int
  fetch next from curgrade into @id ,@grade
  while @@FETCH_STATUS = 0
  begin
    if not exists(select * from avgTable where @id = ID)
      insert into avgTable
	  select ID , grade from inserted
    else 
      update avgTable 
	  set avGrade = (select avg(grade) from student where @id = ID) 
	  where ID = @id
  end
  close curgrade
  deallocate curgrade
  
insert into student values('2017004',60)
```

作业：利用游标，将成绩小于50的提高30%，成绩大于50的提高15%，提高后改的成绩不能超过100

```sql
--声明游标
declare curgrade cursor
for select ID, grade from student
--打开游标
open curgrade
--读取数据
declare @id char(10), @grade int
fetch next from curgrade into @id ,@grade
while @@FETCH_STATUS = 0
   if @grade < 50
     update student set grade = grade +grade*0.3 where ID = @id
   else if @grade >=50
     update student set grade = grade +grade*0.15 where ID = @id
   if @grade >= 100
     update student set grade = 100 where ID = @id
   fetch next from curgrade into @id ,@grade
--关闭游标
close curgrade
--释放游标资源
deallocate curgrade
```

### 10.事务

事务(transaction)由一系列的sql语句组成，是SQL server的**执行单元**，要么都执行，要么都不执行

事务的四个特点：**ACID**

- A(atomicity)：原子性，要么都执行，要么都不执行
- C(consistency)：一致性，事务执行完毕后，数据库从一个一致性状态变成另一个一致性状态
- I(isolation)：隔离性，事务之间相互独立
- D(durability)：永久性，执行状态不可逆

**自动提交事务**：默认事务类型，每条单独的sql语句都是一个事务，要么成功执行，要么都不执行

**显式事务**：开始事务：begin transaction；提交事务(要么都执行)：commit transaction；回滚事务(要么都不执行)：rollback transaction

```sql
begin tran
  delete from student
  go
  insert into student values('2017005',78)
  go
commit/rollback
--保存点
begin tran
  save tran sp1
  insert into student values('2017001',68)
  go
  save tran sp2
  go
rollback tran sp1
```

利用事务实现转账业务

```sql
begin tran
  update account set balance = balance +500 where cardID = '3272'
  if @@error <> 0
  begin
    rollback
	return
  end
  update account set balance = balance -500 where cardID = '1276'
  if @@error <> 0
  begin
    rollback
	return
  end
  commit
--另一种实现方式
begin tran
begin try
  update account set balance = balance +500 where cardID = '3272'
  update account set balance = balance -500 where cardID = '1276'
end try
begin catch
  rollback
end catch
```

用存储过程+事务实现转账业务

```sql
create procedure bd_Getborrow
  @cardfrom char(4),
  @cardto char(4),
  @balance int
as
  begin tran
  begin try
    update account set balance = balance +@balance where cardID = @cardto
    update account set balance = balance -@balance where cardID = @cardfrom 
	commit
  end try
  begin catch
    rollback
    raiserror('转账失败！账户余额要大于0！',16,1)
  end catch
--调用
bd_Getborrow '1276','3272',500
```

**隐式事务**：无需写开始，只需写结束，即可自动开始事务。提交事务(要么都执行)：commit transaction；回滚事务(要么都不执行)：rollback transaction

```sql
create trigger tri_ddl on database
for create_view
as
  rollback
create view v2 as select * from student

set implicit_transactions on/off
create table test(aa int)
rollback
select * from test
```

###11.并发事务

四类并发问题：

- 丢失修改：事务的并发执行，从而导致对数据的修改丢失。
- 脏读：由于数据的回滚，导致在回滚之前读取到“脏”数据。
- 不可重复读：由于其他修改导致数据的更新，数据与之前不等。
- 幻读：由于对数据的修改，导致之前读到的数据再次读时消失或增加。

根本原因：对数据的**并发操作**

改进：

- 排他锁(X锁)：T对A加排他锁时，其他事务不能对A进行任何操作，直至T释放A的锁。
- 共享锁(S锁)：T对A加共享锁时，T可以读A，不可修改A，其他事务只能对A加共享锁。

封锁协议：

- 一级封锁协议(对应read uncommited)：事务T在修改事务前，必须要加排他锁，事务结束后释放排他锁——可防止丢失修改，在一级封锁协议中，如果仅仅是读数据不对其进行修改，是不需要加锁的，所以它不能保证可重复读和脏读。
- 二级封锁协议(对应read commited)：在一级封锁协议的基础上，事务T读取数据时对A加共享锁，读完后释放共享锁——防止丢失修改、脏读。
- 三级封锁协议(对应reapetable read)：与二级封锁协议相同，但在事务执行完毕后才释放共享锁——防止丢失修改、脏读、不可重复读。
- 四级封锁协议(对应serialization)：对事务T中所读取或者更改的数据所在的表加表锁，也就是说，其他事务不能读写该表中的任何数据 。可防止上述所有问题。

### 12.安全性

安全机制 = 身份验证 + 访问许可

身份验证：登录用户

访问许可：数据库用户

### 13.数据库的备份与还原

备份设备：用来存放数据库备份的存储介质——命名管道、磁带、磁盘

备份数据库

- 完整备份：用时长，空间占用大
```sql
backup database Test to device1 --with init --备份设备,with init为覆盖备份，默认为追加
backup database Test to disk='E:\数据库\Test.bak'
```
- 差异备份：先做一个完整备份，以完整备份为基点，备份发生改变的备份`with differencetial`
- 事务日志备份
```sql
backup log Test to disk='E:\数据库\Testlog.bak'
```
- 文件和文件组备份

还原数据库

```sql
restore database Test from disk='E:\数据库\Test.bak'
```

### 14.分离与附加

```sql
--分离
sp_detach_db 'Test'
--附加
sp_attach_db 'Test','D:Test.mdf','...'
```

