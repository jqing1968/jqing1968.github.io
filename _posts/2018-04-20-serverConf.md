---
layout: post
title:  "阿里云服务器相关配置"
date:   2018-04-20 14:34
categories: server
permalink: /archivers/serverconf
---
记录一下踩过的坑,菜鸡的忧伤,所以涉及到的端口都要去阿里云安全组配置,被这点坑过好几次了,防火墙开放端口和重载。
```js
sudo firewall-cmd --zone=public --add-port=端口号/tcp --permanent
sudo firewall-cmd --reload
```

基于centos7操作系统

## vsFTPd

**安装**
    `yum  -y  install  vsftpd`

**修改配置**
    `vim /etc/vsftpd.conf`
```js
// 取消注释
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list
// 增加
userlist_enable=YES
```

**重启ftp**
    `systemctl restart vsftpd.service`

**创建用户**

使用以下命令创建用户。使用`/usr/sbin/nologinshell`来阻止访问ftp用户的bash shell
```js
useradd -d /var/www/ -s /usr/sbin/nologin www
passwd www
echo www >> /etc/vsftpd/chroot_list
chmod a-w www
```
允许nologin shell的登录访问权限。打开/etc/shells并在末尾添加以下行。
`/usr/sbin/nologin`

**卸载**

停止服务器 `sytemctl stop vsftpd.service`

检查 `rpm -qa | grep vsftpd`

清除 `rpm -e vsfted版本`

检查残余 `find / -name '*vsftpd*'`

清除残余 `rm -rf 文件夹`

## nginx
**安装**
    `yum install nginx`

**修改配置**
    `vim /etc/nginx/nginx.conf`

```js
// 修改user nginx
user root
// 修改 http>server下的根目录和和入口文件
root    /var/www;
index   index.html index.htm index.php default.html default.htm default.php;
```

**上传** `scp -r local_dir user@ip:/reomte_dir`
    (或采用上面所述FTP上传)

## mySql
**获取repo源**
    `wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm`

**安装**
    `rpm -ivh mysql-community-release-el7-5.noarch.rpm`

**重启服务**
    `systemctl restart mysql.service`

**配置远程登录**
```js
use mysql;
update user set password=password('密码') where user='root';
GRANT ALL PRIVILEGES ON *.* TO root@"%" IDENTIFIED BY "密码";
```

## mongoDB
**配置包管理系统（yum）**
    `vim /etc/yum.repos.d/mongodb-org-4.0.repo`
```
[mongodb-org-4.0]
name = MongoDB Repository
baseurl = https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.0/x86_64/
gpgcheck = 1
enabled = 1
gpgkey = https：// www.mongodb.org/static/pgp/server-4.0.asc
```

**安装**
    `sudo yum install -y mongodb-org`

**修改配置**

设置远程访问
```js
bind_ip=0.0.0.0
```
## node.js
**下载源码**
    `wget http://nodejs.org/dist/v6.9.5/node-v6.9.5-linux-x64.tar.gz`

**解压**
    `tar xzvf node-v6.9.5-linux-x64.tar.gz && cd node-v6.9.5-linux-x64`

**编译安装**
```js
./configure
make
sudo make install
```

**配置环境变量**

设置全局访问
```js
ln -s /root/node-v6.9.5-linux-x64/bin/node /usr/local/bin/node
ln -s /root/node-v6.9.5-linux-x64/bin/npm /usr/local/bin/npm
```
测试 `node -v && npm -v`

查看环境变量 `echo $PATH`

修改配置文件 `vim /etc/profile`
```js
// 新增两行
export NODE_HOME=/usr/local/node_global
export PATH=${NODE_HOME}/bin:$PATH
```

移动到
    `cd /usr/local`

设置全局模块存放路径和缓存
```js
npm config set prefix "node_global"
npm config set cache "node_cache"
```

刷新配置
    `source /etc/profile`


