# centos搭建shadowsocks  

**1.安装pip**  
由于安装的是python 版本的 shadowsocks，所以首先安装pip  
 ```
 curl "https://bootstrap.pypa.io/get-pip.py" -o "get-pip.py"
 python get-pip.py
 ```  
**2.安装shadowsocks**  
```
 pip install --upgrade pip  
 pip install shadowsocks  
 ```
 **3.创建配置文件**  
创建文件所在目录:/etc   
 vi /etc/shadowsocks.json  
  将配置文件复制进去修改即可：  
```
单端口：
 {
 "server":"0.0.0.0",            --服务器IP，直接用0.0.0.0或机器的外网IP
 "server_port":8888,            --端口端口
 "local_address": "127.0.0.1",  --本地地址，可省略
 "local_port":1080,             --本地端口，可省略
 "password":"password",         --密码
 "timeout":300,                 --超时时间，可省略
 "method":"aes-256-cfb",        --加密策略，有多重策略，具体自查
}
多端口：
{
    "server":"0.0.0.0",
    "local_address":"127.0.0.1",
    "local_port":1080,
    "port_password":{           --每个端口对应一个密码
        "1111":"password1",
        "1112":"password2",
        "1113":"password3"
    },
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open":false
}
```
**管理shadowsocks**
#启动
```
ssserver -c /etc/shadowsocks.json -d start
```
#停止
```
ssserver -c /etc/shadowsocks.json -d stop
```
#重启
```
ssserver -c /etc/shadowsocks.json -d restart
```
启动成功即可通过ss客户端使用。

**5.配置自启动(可选择)**  
新建启动脚本文件/etc/systemd/system/shadowsocks.service，内容如下：  
```
[Unit]
Description=Shadowsocks

[Service]
TimeoutStartSec=0
ExecStart=/usr/bin/ssserver -c /etc/shadowsocks.json

[Install]
WantedBy=multi-user.target
```
通过以下命令注册，启动服务
```
 systemctl enable shadowsocks
 systemctl start shadowsocks
 systemctl status shadowsocks
 ```

若启动成功：
```
[root@iz8pse9ii2woqoj6l73am3z ~]# systemctl status shadowsocks
● shadowsocks.service - Shadowsocks
   Loaded: loaded (/etc/systemd/system/shadowsocks.service; enabled; vendor preset: disabled)
   Active: active (running) since Tue 2019-01-15 19:06:41 CST; 16s ago
 Main PID: 11924 (ssserver)
   CGroup: /system.slice/shadowsocks.service
           └─11924 /usr/bin/python /usr/bin/ssserver -c /etc/shadowsocks.json
```
另外防火墙或者安全组要打开相应端口
