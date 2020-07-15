##               Centos7.4系统下安装httpd，mariadb，php7.2环境运行禅道            



[toc]



# **一、运行环境说明**

 推荐运行环境为Apache + PHP(7.0/7.1/7.2版本) + MySQL(5.5/5.6版本)/mariadb 组合，Nginx 其次。             

PHP需要使用 pdo, pdo_mysql, json, filter, openssl, mbstring, zlib, curl, gd, iconv 模块，需要确保 PHP 运行环境有加载上述模块。             

# **二、安装Apache服务**             

依次执行如下指令，安装并开启 Apache 服务

```js
yum -y install httpd           #安装Apache服务
systemctl start httpd.service  #开启Apache服务 
systemctl enable httpd.service #Apache服务开机启动
```

Apache 其他运维命令

```js
systemctl status httpd.service  #查看Apache服务状态 
systemctl stop httpd.service    #关闭Apache服务 
systemctl restart httpd.service #重启Apache服务
```

安装完成后，可以查看Apache运行状态，如下图表示已经启动

![img](https://www.zentao.net/file.php?f=201906/f_9b313dbf9449ee06a87a66b5671eb526&t=png&o=&s=&v=1559524235)               

**关闭防火墙**( 或者自行百度相关指令开启服务器的 80 端口 )，以便排除因为网络问题 , 无法访问 Apache 服务: 

```php
systemctl stop firewalld.service     # 关闭防火墙 
systemctl disable firewalld.service  # 禁止firewall开机启动 
systemctl status firewalld.service   # 查看防火墙状态 
systemctl start firewalld.service    # 开启防火墙
```

**关闭 SELINUX**，未关闭时可能有无法授予 /var 路径下文件的读写权限问题，关闭命令如下: 

```js
setenforce 0 # 临时关闭SELINUX, 重启服务器失效
```

编辑/etc/selinux/config** 文件，将 SELINUX 的值设置为 disabled , 下次开机 SELINUX 就不会启动了

```js
vi /etc/selinux/config # 修改文件中的 SELINUX=disabled 
```

关闭防火墙后，通过执行 ip addr 命令，查看服务器主机ip地址。

![img](https://www.zentao.net/file.php?f=201906/f_78b7d9a4df5e0f5337c7ed6e0da0440e&t=png&o=&s=&v=1559538164) 

浏览器访问 http://服务器ip地址 , 页面展示如下，则表示 Apache 运行成功了。

![img](https://www.zentao.net/file.php?f=201906/f_363c0f000b4cb82e1b2612e7ab8b087a&amp;t=png&amp;o=&amp;s=&amp;v=1559538219)               

**apache 会被安装到： /etc/httpd/**

**apache 配置文件地址： /etc/httpd/conf/httpd.conf**

**apache 网站文件默认访问路径：/var/www/html/**

# **三、安装PHP7.2版本**

PHP7.2 版本需要配置 yum 源 : 

```js
rpm -Uvh https://mirror.webtatic.com/yum/el7/epel-release.rpm
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
```

yum 安装 PHP7.2 所需组件 :

```js
yum -y install php72w php72w-pdo php72w-mysql php72w-mbstring php72w-ldap php72w-gd php72w-json
```

创建 phpinfo 文件, 测试 PHP 与 Apache 服务的整合:

```js
echo '<?php phpinfo();?>'  >  /var/www/html/index.php
```

访问前 , 重启 Apache 服务，浏览器访问http://服务器ip/index.php，页面展示如下, 则Apache 解析 PHP 成功。

![img](https://www.zentao.net/file.php?f=202007/f_0a31263aec20df3b188c85a33fd322f0&t=png&o=&s=&v=1593668271)
            

# **四、安装mariadb 数据库**             

安装并开启 mariadb 服务: 

```js
yum -y install mariadb mariadb-server 
systemctl start mariadb.service # 开启服务 
systemctl enable mariadb.service # 开机启动 
systemctl status mariadb.service # 查看服务状态
systemctl stop mariadb.service # 关闭服务
```

重置数据库 root 账号密码( 默认root密码为空 )，命令行执行如下命令 , 注意执行命令前必须开启 mariadb服务:              

```js
mysql_secure_installation 
Enter current password for root (enter for none): # 输入当前root账号密码，刚安装默认为空，直接回车即可 
Set root password? [Y/n] # 是否输入root密码，输入y 回车 
New password: # 输入密码 
Re-enter new password: # 重复输入 
Remove anonymous users? [Y/n] # 删除其他用户 y 
Disallow root login remotely? [Y/n]  # 允许root账号远程登录 y 
Remove test database and access to it? [Y/n]  # 删除测试表 y 
Reload privilege tables now? [Y/n] # 重新加载配置表 y
```

**五、安装禅道软件**             

1. 下载新版禅道软件源码包, 下载地址：http://www.zentao.net/download.html                     

2. 使用 rz 命令将禅道软件上传至服务器的 /var/www/html 目录，您也可以使用其他方式上传。             

```js
yum -y install lrzsz # 安装rz上传程序 
cd /var/www/html # 切换到apache根目录 
rz # 上传源码包禅道
```

3. 使用 unzip 命令解压禅道源码包程序。            

```js
yum -y install unzip # 安装unzip解压 
unzip ZenTaoPMS.*.zip -d /var/www/html # 解压禅道源码包
```

![img](https://www.zentao.net/file.php?f=201912/f_9470fecaa49c6237183ea495f71fea4b&t=png&o=&s=&v=1577175823)               

4. 修改 Apache 访问路径: vim /etc/httpd/conf/httpd.conf , 注意修改如下图 3处位置 :              

![img](https://www.zentao.net/file.php?f=201912/f_6d7cd61d46567e16b922fa8b1ea27601&t=png&o=&s=&v=1577175823)               

修改后 systemctl restart httpd 重启 Apache 服务, 使刚修改的配置生效 ;              

5. 解压完成后，浏览器访问http://服务器ip 地址，安装禅道即可。            