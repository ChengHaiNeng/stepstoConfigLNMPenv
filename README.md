# stepstoConfigLNMPenv
Step to Config Linux+Nginx+Mysql+PHP environment
# 示例: yum 安装 gcc 编译环境,为编译 lnmp 做准备
yum -y install gcc gcc-c++ autoconf libjpeg libjpeg-devel libpng libpng-devel
freetype freetype-devel libxml2 libxml2-devel zlib zlib-devel glibc glibcdevel glib2 glib2-devel bzip2 bzip2-devel ncurses ncurses-devel curl curldevel e2fsprogs e2fsprogs-devel krb5 krb5-devel libidn libidn-devel openssl
openssl-devel openldap openldap-devel nss_ldap openldap-clients openldapservers gd gd2 gd-devel gd2-devel perl-CPAN pcre-devel
# ⼿动编译安装软件*
软件编译就是把 源代码(如 c,c++)编译成⼆进制的过程
注意要下载源代码
以 memcached 为例,来编译,到 memcached.org 下载源码.
下载到/usr/local/src下
编译软件分为4步
1: 将软件包 解压到制定⽬录；
2: 配置编译参数
./configure --prefix=/安装/路径如果还有其他选项,./configure --help 来查看
3: make 编译 [⽣成 2 进制]
4: make install [把⽣成的 2 进制复制到 prefix 指定的安装路径⾥]
以 memcached 为例
./configure prefix=/usr/local/memcached
configure: error: libevent is required. You can get it from http://www.monkey.org/~provos
If it's already installed, specify its path using --with-libevent=/dir/
注： 在编译安装软件时， 经常会遇到此类问题， 其实就是缺少相应的软件或者系统组件
因此
我们可以选择下载 libevent 的源码,然后编译安装 libevent；
也可以 yum install libevent 安装；
然后在编译memcached；
以 yum 为例:
yum install libevent 安装后问题仍然存在；
对于库来说,不仅要装库本身,往往还要装库的源码 xx-devel：
yum install libevent-devel
./configure prefix=/usr/local/memcached
make
mak install
启动
memcached -d -u root
查看
netstat -tupln | grep memcached
杀死
kill -9 进程号 / pkill 进程名
编译安装LNMP环境LNMP环境：
linux+Nginx+MySQL+PHP
# 编译nginx
1: 下载
http://nginx.org/en/download.html
选择 stable 版本下载,最新稳定版
2: 解压
tar zxf nginx.xxxx.tar.gz
3: 配置编译参数
./configure --prefix=/usr/local/nginx
如果提示缺少 pcre 库,
则从 http://www.pcre.org/
假设解压在/usr/local/src/pcre-source
假设安装在/usr/local/pcre
4: 再次配置
1.6.x以上版本,要求指定 pcre 的源码⽬录,即:
./configure --prefix=/usr/local/nginx
--with-pcre=/usr/local/src/pcre-source
之前的版本, 指定 pcre 的安装⽬录,即:
./configure --prefix=/usr/local/nginx
--with-pcre=/usr/local/pcre
5: 编译安装
make
make install
6: 启动 nginx
./sbin/nginx
7.连接从局域⽹内连接 nginx,
如果连接失败,先 ping 测试,2 台机器之间⽹络是否通,
再在服务器上 telnet localhost 80
如果 2 者都能,但外界连不上 80 端⼝,则是防⽕墙的原因.
service iptables stop
centos7关闭防⽕墙:
systemctl stop firewalld.service
再次连接,出现以下界⾯,则安装成功
# 编译安装PHP
安装PHP⽤到的系统组件：
yum install gd zlib zlib-devel openssl openssl-devel libxml2 libxml2-devel
libjpeg libjpeg-devel libpng libpng-devel
下载并解压， 切换到相应⽬录
设置编译参数：
./configure --prefix=/usr/local/php
--with-config-file-path=/etc/php
--with-gd
--enable-gd-native-ttf
--enable-gd-jis-conv
--enable-mysqlnd
--with-mysql=mysqlnd
--with-pdo-mysql=mysqlnd--with-openssl
--enable-mbstring
--enable-fpm
cd /usr/local/php
cp ./etc/php-fpm.conf.default ./etc/php-fpm.conf
cp /usr/local/src/php-5.5.13/php.ini-development /etc/php/php.ini
./sbin/php-fpm
# 整合 nginx 和 PHP
Vim /path/to/nginx.conf
让 nginx 的最新配置⽂件⽣效
./sbin/nginx -s reload
再次请求 xx.php
# 安装MySQL
编译安装 *下载MySQL程序包并解压
官⽹： http://dev.mysql.com/downloads/mysql/
sudo wget http://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.17.tar.gz
tar -zxvf mysql-5.7.17.tar.gz
创建⽤户及组
sudo groupadd mysql
sudo useradd -g mysql mysql -s /bin/false
创建安装⽬录和数据存放⽬录， 并修改权限
sudo mkdir -p /usr/local/mysql/data
sudo chown -R mysql:mysql /usr/local/mysql
进⼊解压后的⽬录
cd mysql-5.6.26/
存在这个⽂件就删除
rm CMakeCache.txt
cmake编译
cmake -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -
DMYSQL_DATADIR=/usr/local/mysql/data -DMYSQL_UNIX_ADDR=/tmp/mysql.sock -
DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci -DWITH_DEBUG=0
安装
sudo make
sudo make install
修改安装完成后⽂件的所有者和组
sudo chown -R mysql:mysql /usr/local/mysql
复制配置⽂件模板到/etc
sudo cp /usr/local/mysqlsupport-files/my-default.cnf /etc/my.cnf
修改配置⽂件
sudo vim /etc/my.cnfbasedir = /usr/local/mysql/
datadir = /usr/local/mysql/data
socket = /tmp/mysql.sock
将启动和cli脚本软连接到系统变量， ⽅便使⽤
sudo ln -s /usr/local/mysql/bin/mysql /usr/bin
sudo ln -s /usr/local/mysql/bin/mysqladmin /usr/bin
sudo ln -s /usr/local/mysql/support-files/mysql.server /usr/bin
初始化数据库， 创建基本数据库
sudo /usr/local/mysql/scripts/mysql_install_db --user=mysql --
basedir=/usr/local/mysql --datadir=/usr/local/mysql/data --no-defaults
启动服务
sudo mysql.server start
为root⽤户创建密码
sudo /usr/local/mysql/bin/mysqladmin -u root password '123'
查看启动进程
sudo netstat -lntup | grep 3306
登录MySQL服务器
mysql -uroot -p
⼆进制安装
http://ftp.nchu.edu.tw/Unix/Database/MySQL/Downloads/MySQL-5.5/
mysql-5.5.30-linux2.6-x86_64.tar.gz
MySQL 的安装稍复杂⼀些(主要是编译后的配置及初始化),⼤家注意,碰到开源软件
1:官⽹的安装介绍
2: 下载源码后,⼀般有 README/INSTALL
3: ./configure --help
我们可以下载 2 进制版本来安装:
官⽅示例:
shell> groupadd mysqlshell> useradd -r -g mysql mysql
shell> cd /usr/local
shell> tar zxvf /path/to/mysql-VERSION-OS.tar.gz
shell> ln -s full-path-to-mysql-VERSION-OS mysql
shell> cd mysql
shell> chown -R mysql .
shell> chgrp -R mysql .
shell> scripts/mysql_install_db --user=mysql # 安装初始化数据
shell> chown -R root .
shell> chown -R mysql data
具体安装流程:
groupadd mysql
useradd -g mysql mysql
cd /usr/local/mysql5.5/
chown -R mysql .
chgrp -R mysql .
./scripts/mysql_install_db --user=mysql
如果提示如下错误:
/bin/mysqld: error while loading shared libraries: libaio.so.1: cannot open
shared object file: No such file or directory
则 yum install libaioso.1 libaio
然后再次执⾏
chown -R root .
chown -R mysql data
mkdir /var/run/mysqld
chown mysql /var/run/mysqld
chgrp mysql /var/run/mysqld
./bin/mysqld_safe --user=mysql &
mysql 连接
Mysqld 安装后,连接经常出现找不到 sock 的情况
ERROR 2002 (HY000): Can't connect to local MySQL server through socket
'/tmp/mysql.sock' (2).sock linux通过内存数据共享的⽅式替换⽹络数据通信
我们⽤ 2 个办法来解决
1: 建⽴软件链接
ln /var/lib/mysql/mysql.sock /tmp/mysql.sock
2: 查看 mysql --help
Mysql -S /path/to/mysql.sock
mysql 修改密码
Mysql ⽤户的密码,存储在⼀个系统库⾥的---mysql
注意: mysql ⽤户权限检测,检测 Host,User,Password
mysql> update user set Password=password('123456') where Host='localhost' and
User='root';
mysql> delete from user where Password='';
mysql> flush privileges
