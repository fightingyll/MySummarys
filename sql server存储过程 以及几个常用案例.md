转载自http://www.cnblogs.com/hoojo/archive/2011/07/19/2110862.html

SQL Server 存储过程

Transact-SQL中的存储过程，非常类似于Java语言中的方法，它可以重复调用。当存储过程执行一次后，可以将语句缓存中，这样下次执行的时候直接使用缓存中的语句。这样就可以提高存储过程的性能。

Ø 存储过程的概念

    存储过程Procedure是一组为了完成特定功能的SQL语句集合，经编译后存储在数据库中，用户通过指定存储过程的名称并给出参数来执行。

    存储过程中可以包含逻辑控制语句和数据操纵语句，它可以接受参数、输出参数、返回单个或多个结果集以及返回值。

    由于存储过程在创建时即在数据库服务器上进行了编译并存储在数据库中，所以存储过程运行要比单个的SQL语句块要快。同时由于在调用时只需用提供存储过程名和必要的参数信息，所以在一定程度上也可以减少网络流量、简单网络负担。

 

    1、 存储过程的优点

        A、 存储过程允许标准组件式编程

        存储过程创建后可以在程序中被多次调用执行，而不必重新编写该存储过程的SQL语句。而且数据库专业人员可以随时对存储过程进行修改，但对应用程序源代码却毫无影响，从而极大的提高了程序的可移植性。

        B、 存储过程能够实现较快的执行速度

        如果某一操作包含大量的T-SQL语句代码，分别被多次执行，那么存储过程要比批处理的执行速度快得多。因为存储过程是预编译的，在首次运行一个存储过程时，查询优化器对其进行分析、优化，并给出最终被存在系统表中的存储计划。而批处理的T-SQL语句每次运行都需要预编译和优化，所以速度就要慢一些。

        C、 存储过程减轻网络流量

        对于同一个针对数据库对象的操作，如果这一操作所涉及到的T-SQL语句被组织成一存储过程，那么当在客户机上调用该存储过程时，网络中传递的只是该调用语句，否则将会是多条SQL语句。从而减轻了网络流量，降低了网络负载。

        D、 存储过程可被作为一种安全机制来充分利用

        系统管理员可以对执行的某一个存储过程进行权限限制，从而能够实现对某些数据访问的限制，避免非授权用户对数据的访问，保证数据的安全。

 

Ø 系统存储过程

    系统存储过程是系统创建的存储过程，目的在于能够方便的从系统表中查询信息或完成与更新数据库表相关的管理任务或其他的系统管理任务。系统存储过程主要存储在master数据库中，以“sp”下划线开头的存储过程。尽管这些系统存储过程在master数据库中，但我们在其他数据库还是可以调用系统存储过程。有一些系统存储过程会在创建新的数据库的时候被自动创建在当前数据库中。

    常用系统存储过程有：

exec sp_databases; --查看数据库
exec sp_tables;        --查看表
exec sp_columns student;--查看列
exec sp_helpIndex student;--查看索引
exec sp_helpConstraint student;--约束
exec sp_stored_procedures;
exec sp_helptext 'sp_stored_procedures';--查看存储过程创建、定义语句
exec sp_rename student, stuInfo;--修改表、索引、列的名称
exec sp_renamedb myTempDB, myDB;--更改数据库名称
exec sp_defaultdb 'master', 'myDB';--更改登录名的默认数据库
exec sp_helpdb;--数据库帮助，查询数据库信息
exec sp_helpdb master;

    系统存储过程示例：
--表重命名
exec sp_rename 'stu', 'stud';
select * from stud;
--列重命名
exec sp_rename 'stud.name', 'sName', 'column';
exec sp_help 'stud';
--重命名索引
exec sp_rename N'student.idx_cid', N'idx_cidd', N'index';
exec sp_help 'student';

--查询所有存储过程
select * from sys.objects where type = 'P';
select * from sys.objects where type_desc like '%pro%' and name like 'sp%';
 

Ø 用户自定义存储过程

   1、 创建语法

create proc | procedure pro_name
    [{@参数数据类型} [=默认值] [output],
     {@参数数据类型} [=默认值] [output],
     ....
    ]
as
    SQL_statements
 

   2、 创建不带参数存储过程

--创建存储过程
if (exists (select * from sys.objects where name = 'proc_get_student'))
    drop proc proc_get_student
go
create proc proc_get_student
as
    select * from student;

--调用、执行存储过程
exec proc_get_student;

   3、 修改存储过程

--修改存储过程
alter proc proc_get_student
as
select * from student;

   4、 带参存储过程

--带参存储过程
if (object_id('proc_find_stu', 'P') is not null)
    drop proc proc_find_stu
go
create proc proc_find_stu(@startId int, @endId int)
as
    select * from student where id between @startId and @endId
go

exec proc_find_stu 2, 4;

   5、 带通配符参数存储过程

--带通配符参数存储过程
if (object_id('proc_findStudentByName', 'P') is not null)
    drop proc proc_findStudentByName
go
create proc proc_findStudentByName(@name varchar(20) = '%j%', @nextName varchar(20) = '%')
as
    select * from student where name like @name and name like @nextName;
go

exec proc_findStudentByName;
exec proc_findStudentByName '%o%', 't%';

   6、 带输出参数存储过程

if (object_id('proc_getStudentRecord', 'P') is not null)
    drop proc proc_getStudentRecord
go
create proc proc_getStudentRecord(
    @id int, --默认输入参数
    @name varchar(20) out, --输出参数
    @age varchar(20) output--输入输出参数
)
as
    select @name = name, @age = age  from student where id = @id and sex = @age;
go

-- 
declare @id int,
        @name varchar(20),
        @temp varchar(20);
set @id = 7; 
set @temp = 1;
exec proc_getStudentRecord @id, @name out, @temp output;
select @name, @temp;
print @name + '#' + @temp;


   7、 不缓存存储过程

--WITH RECOMPILE 不缓存
if (object_id('proc_temp', 'P') is not null)
    drop proc proc_temp
go
create proc proc_temp
with recompile
as
    select * from student;
go

exec proc_temp;

   8、 加密存储过程

--加密WITH ENCRYPTION 
if (object_id('proc_temp_encryption', 'P') is not null)
    drop proc proc_temp_encryption
go
create proc proc_temp_encryption
with encryption
as
    select * from student;
go

exec proc_temp_encryption;
exec sp_helptext 'proc_temp';
exec sp_helptext 'proc_temp_encryption';

   9、 带游标参数存储过程

if (object_id('proc_cursor', 'P') is not null)
    drop proc proc_cursor
go
create proc proc_cursor
    @cur cursor varying output
as
    set @cur = cursor forward_only static for
    select id, name, age from student;
    open @cur;
go
--调用
declare @exec_cur cursor;
declare @id int,
        @name varchar(20),
        @age int;
exec proc_cursor @cur = @exec_cur output;--调用存储过程
fetch next from @exec_cur into @id, @name, @age;
while (@@fetch_status = 0)
begin
    fetch next from @exec_cur into @id, @name, @age;
    print 'id: ' + convert(varchar, @id) + ', name: ' + @name + ', age: ' + convert(char, @age);
end
close @exec_cur;
deallocate @exec_cur;--删除游标


   10、 分页存储过程

---存储过程、row_number完成分页
if (object_id('pro_page', 'P') is not null)
    drop proc proc_cursor
go
create proc pro_page
    @startIndex int,
    @endIndex int
as
    select count(*) from product
;    
    select * from (
        select row_number() over(order by pid) as rowId, * from product 
    ) temp
    where temp.rowId between @startIndex and @endIndex
go
--drop proc pro_page
exec pro_page 1, 4
--
--分页存储过程
if (object_id('pro_page', 'P') is not null)
    drop proc pro_stu
go
create procedure pro_stu(
    @pageIndex int,
    @pageSize int
)
as
    declare @startRow int, @endRow int
    set @startRow = (@pageIndex - 1) * @pageSize +1
    set @endRow = @startRow + @pageSize -1
    select * from (
        select *, row_number() over (order by id asc) as number from student 
    ) t
    where t.number between @startRow and @endRow;

exec pro_stu 2, 2;


Ø Raiserror

Raiserror返回用户定义的错误信息，可以指定严重级别，设置系统变量记录所发生的错误。

   语法如下：

Raiserror({msg_id | msg_str | @local_variable}
  {, severity, state}
  [,argument[,…n]]
  [with option[,…n]]
)

   # msg_id:在sysmessages系统表中指定的用户定义错误信息

   # msg_str:用户定义的信息，信息最大长度在2047个字符。

   # severity：用户定义与该消息关联的严重级别。当使用msg_id引发使用sp_addmessage创建的用户定义消息时，raiserror上指定严重性将覆盖sp_addmessage中定义的严重性。

    任何用户可以指定0-18直接的严重级别。只有sysadmin固定服务器角色常用或具有alter trace权限的用户才能指定19-25直接的严重级别。19-25之间的安全级别需要使用with log选项。

   # state：介于1至127直接的任何整数。State默认值是1。

raiserror('is error', 16, 1);
select * from sys.messages;
--使用sysmessages中定义的消息
raiserror(33003, 16, 1);
raiserror(33006, 16, 1);






导读：sql存储是数据库操作过程中比较重要的一个环节，对于一些初学者来说也是比较抽象难理解的，本文我将通过几个实例来解析数据库中的sql存储过程，这样就将抽象的事物形象化，比较容易理解。
例1：
create proc proc_stu 
@sname varchar(20), 
@pwd varchar(20) 
as 
select * from ren where sname=@sname and pwd=@pwd 
go
查看结果：proc_stu 'admin','admin'
例2：
下面的存储过程实现用户验证的功能，如果不成功，返回0，成功则返回1.
CREATE PROCEDURE VALIDATE @USERNAME CHAR(20),@PASSWORD CHAR(20),@LEGAL BIT OUTPUT
AS
IF EXISTS(SELECT * FROM REN WHERE SNAME = @USERNAME AND PWD = @PASSWORD) 
SELECT @LEGAL = 1 
ELSE 
SELECT @LEGAL = 0
在程序中调用该存储过程，并根据@LEGAL参数的值判断用户是否合法。
例3：一个高效的数据分页的存储过程 可以轻松应付百万数据
CREATE PROCEDURE pageTest --用于翻页的测试
--需要把排序字段放在第一列
(
@FirstID nvarchar(20)=null, --当前页面里的第一条记录的排序字段的值
@LastID nvarchar(20)=null, --当前页面里的最后一条记录的排序字段的值
@isNext bit=null, --true 1 :下一页；false 0:上一页
@allCount int output, --返回总记录数
@pageSize int output, --返回一页的记录数
@CurPage int --页号（第几页）0：第一页；-1最后一页。
)
AS
if @CurPage=0--表示第一页
begin
--统计总记录数
select @allCount=count(ProductId) from Product_test 

set @pageSize=10
--返回第一页的数据
select top 10 
ProductId,
ProductName,
Introduction 
from Product_test order by ProductId 
end
else if @CurPage=-1--表示最后一页
select * from 
(select top 10 ProductId,
ProductName,
Introduction
from Product_test order by ProductId desc ) as aa 
order by ProductId
else
begin 
if @isNext=1
--翻到下一页
select top 10 ProductId,
ProductName,
Introduction
from Product_test where ProductId > @LastID order by ProductId 
else
--翻到上一页
select * from
(select top 10 ProductId,
ProductName,
Introduction
from Product_test where ProductId < @FirstID order by ProductId desc) as bb order by ProductId
end
上文中讲到的这三个例子都是sql存储过程比较典型的例子，希望大家好好学习，都能够学到大家各自需要的东西。

分页
代码如下:

/*分页查找数据*/ 
CREATE PROCEDURE [dbo].[GetRecordSet] 
@strSql varchar(8000),--查询sql,如select * from [user] 
@PageIndex int,--查询当页号 
@PageSize int--每页显示记录 
AS 
set nocount on 
declare @p1 int 
declare @currentPage int 
set @currentPage = 0 
declare @RowCount int 
set @RowCount = 0 
declare @PageCount int 
set @PageCount = 0 
exec sp_cursoropen @p1 output,@strSql,@scrollopt=1,@ccopt=1,@rowcount=@rowCount output --得到总记录数 
select @PageCount=ceiling(1.0*@rowCount/@pagesize) --得到总页数 
,@currentPage=(@PageIndex-1)*@PageSize+1 
select @RowCount,@PageCount 
exec sp_cursorfetch @p1,16,@currentPage,@PageSize 
exec sp_cursorclose @p1 
set nocount off 
GO 

用户注册：
/* 
用户注册，也算是添加吧 
*/ 
Create proc [dbo].[UserAdd] 
( 
@loginID nvarchar(50), 　　　　--登录帐号 
@password nvarchar(50), --密码 
@email nvarchar(200) --电子信箱 
) 
as 
declare @userID int --用户编号 
--登录账号已经被注册 
if exists(select loginID from tableName where loginID = @loginID) 
begin 
return -1; 
end 
--邮箱已经被注册 
else if exists(select email from tableName where email = @email) 
begin 
return -2; 
end 
--注册成功 
else 
begin 
select @userID = isnull(max(userID),100000)+1 from tableName 
insert into tableName 
(userID,loginID,[password],userName,linkNum,address,email,createTime,status) 
values 
(@userID,@loginID,@password,'','','',@email,getdate(),1) 
return @userID 
end 

SQL Server 系统存储过程
1. 给表中字段添加描述信息


Create table T2 (id int , name char (20)) 
GO 
EXEC sp_addextendedproperty 'MS_Description', 'Employee ID', 'user', dbo, 'table', T2, 'column', id 
EXEC sp_updateextendedproperty 'MS_Description', 'this is a test', 'user', dbo, 'table', T2, 'column', id 

2.修改数据库名称



EXEC sp_renamedb 'old_db_name', 'new_db_name' 

3.修改数据表名称和字段名称


EXEC sp_rename 'old_table_name', 'new_table_name'–修改数据表名称 
EXEC sp_rename 'table_name.[old_column_name]', 'new_column_name', 'COLUMN'–修改字段名称

4.给定存储过程名，获取存储过程内容


exec sp_helptext sp_name

以下是关于数据库控制的
*以下是有关安全控制的系统存储过程或 SQL 语句，详细语法查阅《联机丛书》相关内容*/ 
–创建新的 SQL Server 登录，使用户得以连接使用 SQL Server 身份验证的 SQL Server。 
EXEC sp_addlogin @loginame = '', @passwd = '', @defdb = '', @deflanguage = NULL, @sid = NULL, @encryptopt = NULL 
–使 Windows NT 用户或组帐户得以使用 Windows 身份验证连接到 SQL Server。 
EXEC sp_grantlogin @loginame = '' 
–删除 SQL Server 登录，以阻止使用该登录名访问 SQL Server。 
EXEC sp_droplogin @loginame = '' 
–阻止 Windows NT 用户或组连接到 SQL Server。 
EXEC sp_denylogin @loginame = '' 
–从 SQL Server 中删除用 sp_grantlogin 或 sp_denylogin 创建的 Windows NT 用户或组的登录项。 
EXEC sp_revokelogin @loginame = '' 
–更改登录的默认数据库。 
EXEC sp_defaultdb @loginame = '', @defdb = '' 
–更改登录的默认语言。 
EXEC sp_defaultlanguage @loginame = '', @language = '' 
–添加或更改 SQL Server 登录密码。 
EXEC sp_password @old = '', @new = '', @loginame = '' 
–添加服务器角色新成员。 
EXEC sp_addsrvrolemember @loginame = '', @rolename = '' 
–添加服务器角色某成员。 
EXEC sp_dropsrvrolemember @loginame = '' , @rolename = '' 
–为 SQL Server 登录或 Windows NT 用户或组在当前数据库中添加一个安全帐户，并使其能够被授予在数据库中执行活动的权限（授予默认的“public”数据库角色）。 
EXEC sp_grantdbaccess @loginame = '', @name_in_db = NULL 
–或 
EXEC sp_adduser @loginame = '', @name_in_db = NULL, @grpname = '' 
–从当前数据库中删除安全帐户。 
EXEC sp_revokedbaccess @name_in_db = '' 
–或 
EXEC sp_dropuser @name_in_db = '' 
–在当前数据库创建新数据库角色。 
EXEC sp_addrole @rolename = '', @ownername = '' 
–在当前数据库删除某数据库角色。 
EXEC sp_droprole @rolename = '' 
–在当前数据库中添加数据库角色新成员。 
EXEC sp_addrolemember @rolename = '', @membername = '' 
–在当前数据库中删除数据库角色某成员。 
EXEC sp_droprolemember @rolename = '', @membername = '' 
–权限分配给数据库角色、表、存储过程等对象 
–1、授权访问 
GRANT 
–2、拒绝访问 
DENY 
–3、取消授权或拒绝 
REVOKE 
–4、Sample(pubs): 
GRANT SELECT ON authors TO Limperator 
DENY SELECT ON authors TO Limperator 
REVOKE SELECT ON authors TO Limperator






