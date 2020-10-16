# infomations

## Domains 
- yijiu520.com
- yijiu520.cn
- guofengcaipiao.cc

## IP address
- 156.236.101.222

## Open port
- 22 # openssh version 6.6.1
- 80 # nginx
- 443 # nginx
- 8888 # bt.cn

## use hydra get user list for /usr/share/dict/theHarvester/names_small.txt
- www

## use hydra get root password for /usr/share/wordlist/dirb/(small|common).txt
Not get any password

## use uniscan get all directory path on 443 and 8888
```bash
~$ sudo uniscan -u http://yijiu520.com -qweds
~$ cat /usr/share/uniscan/report.yijiu520.com.html

~$ sudo uniscan -u http://yijiu520.com:8888 -qweds
~$ cat /usr/share/uniscan/report.yijiu520.com.html # ALL url return http code is 200. But it's only say jump to login page.
```

# use hydra attack ssh password
- Get password failed.
