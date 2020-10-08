# 获取宝塔管理地址
## 脚本
- https://github.com/aaPanel/BaoTa/blob/master/init.sh
- 命令： /etc/init.d/bt default
```bash
panel_path=/www/server/panel
pidfile=$panel_path/logs/panel.pid
# 此段代码为执行/etc/init.d/bt default命令的过程
    'default')
        port=$(cat $panel_path/data/port.pl)
        password=$(cat $panel_path/default.pl)
        if [ -f $panel_path/data/domain.conf ];then
          address=$(cat $panel_path/data/domain.conf)
        fi
        if [ -f $panel_path/data/admin_path.pl ];then
          auth_path=$(cat $panel_path/data/admin_path.pl)
        fi
        if [ "$address" = "" ];then
          address=$(curl -sS --connect-timeout 10 -m 60 https://www.bt.cn/Api/getIpAddress)
        fi
				pool=http
				if [ -f $panel_path/data/ssl.pl ];then
					pool=https
				fi
        echo -e "=================================================================="
        echo -e "\033[32mBT-Panel default info!\033[0m"
        echo -e "=================================================================="
        echo  "Bt-Panel-URL: $pool://$address:$port$auth_path"
        echo -e `python $panel_path/tools.py username`
        echo -e "password: $password"
        echo -e "\033[33mWarning:\033[0m"
        echo -e "\033[33mIf you cannot access the panel, \033[0m"
        echo -e "\033[33mrelease the following port (8888|888|80|443|20|21) in the security group\033[0m"
        echo -e "=================================================================="
        ;;
```

## 安装脚本设置函数
```bash
Set_Bt_Panel(){
	password=$(cat /dev/urandom | head -n 16 | md5sum | head -c 8)
	sleep 1
	admin_auth="/www/server/panel/data/admin_path.pl"
	if [ ! -f ${admin_auth} ];then
		auth_path=$(cat /dev/urandom | head -n 16 | md5sum | head -c 8)
		echo "/${auth_path}" > ${admin_auth}
	fi
	auth_path=$(cat ${admin_auth})
	cd ${setup_path}/server/panel/
	/etc/init.d/bt start
	$python_bin -m py_compile tools.py
	$python_bin tools.py username
	username=$($python_bin tools.py panel ${password})
	cd ~
	echo "${password}" > ${setup_path}/server/panel/default.pl
	chmod 600 ${setup_path}/server/panel/default.pl
	sleep 3
	/etc/init.d/bt restart 	
	sleep 3
	isStart=$(ps aux |grep 'BT-Panel'|grep -v grep|awk '{print $2}')
	LOCAL_CURL=$(curl 127.0.0.1:8888/login 2>&1 |grep -i html)
	if [ -z "${isStart}" ] && [ -z "${LOCAL_CURL}" ];then
		/etc/init.d/bt 22
		Red_Error "ERROR: The BT-Panel service startup failed."
	fi
}
```
