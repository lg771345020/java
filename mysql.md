# MySQL 安装

以 Centos7 下安装 MySQL 5.6 为例。

## 一、环境准备

__(1) 下载__

    
下载地址: https://dev.mysql.com/get/Downloads/MySQL-5.6/mysql-5.6.40-linux-glibc2.12-x86_64.tar.gz

__(2) 安装依赖__

    yum -y install perl perl-devel autoconf libaio

__(3) 卸载已安装的 MySQL__

    yum list installed | grep mysql  # 查询已安装的 MySQL
    yum -y remove mysql-libs.x86_64  # 卸载 

__(4) 创建 mysql 用户__

```text
userdel mysql           # 删除 mysql 用户
groupdel mysql          # 删除 mysql 用户组
groupadd mysql          # 创建 mysql 用户组
useradd -g mysql mysql  # 创建 mysql 用户并加入 mysql 用户组
passwd mysql            # mysq 添加密码
```

__(5) sudo 免密码操作__

```text
visudo

mysql   ALL=(ALL)         ALL
%wheel  ALL=(ALL)         NOPASSWD: ALL

# 用户加入 wheel 组
gpasswd -a mysql wheel
```

## 二、MySQL 安装

__(1) 切换到 mysql 帐号__

    # 切换到 mysql 帐号
    su mysql
    # 切换到 mysql 目录
    cd

__(2) 将下载的二进制安装包解压后放到 /usr/local/mysql 目录下__

    tar -zxvf mysql-5.6.38-linux-glibc2.12-x86_64.tar.gz
    sudo mv mysql-5.6.38-linux-glibc2.12-x86_64 /usr/local/mysql

__(3) 环境变量__

    sudo vim /etc/profile.d/start.sh

    export PATH=$PATH:/usr/local/mysql/bin

    source /etc/profile.d/start.sh     # 立即生效

__(4) 在 etc 下新建配置文件 my.cnf__

```
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8 
socket=/var/lib/mysql/mysql.sock

[mysqld]
user=mysql
skip-name-resolve
#设置3306端口
port = 3306 
socket=/var/lib/mysql/mysql.sock
# 设置mysql的安装目录
basedir=/usr/local/mysql
# 设置mysql数据库的数据的存放目录
datadir=/usr/local/mysql/data
# 允许最大连接数
max_connections=200
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB 
lower_case_table_names=1
max_allowed_packet=16M
```

__(5) 创建 /var/lib/mysql 并将用户修改为 mysql__
 
    # my.cnf 文件中配制的 socket 目录
    sudo mkdir /var/lib/mysql
    sudo chown -R mysql:mysql /var/lib/mysql


__(6) 初始化 MySQL__

    ./scripts/mysql_install_db --user=mysql

到此 MySQL 安装完毕！下面介绍 MySQL 作为服务启动方式。

## 三、MySQL 启动

1、添加mysql服务开机自启动

```
sudo cp ./support-files/mysql.server /etc/rc.d/init.d/mysqld    # 复制启动脚本到资源目录
sudo chmod +x /etc/rc.d/init.d/mysqld    # 增加 mysqld 服务控制脚本执行权限
sudo chkconfig --add mysqld              # 将 mysqld 服务加入到系统服务
sudo chkconfig --list mysqld             # 检查 mysqld 服务是否已经生效
```

命令输出类似下面的结果：

    mysqld          0:off   1:off   2:on    3:on    4:on    5:on    6:off

表明 mysqld 服务已经生效，在2、3、4、5运行级别随系统启动而自动启动，以后可以使用 service 命令控制 mysql 的启动和停止。

2、启动msql

```
service mysqld start    # 启动 msql
service mysqld stop     # 停止msql
```

## 四、权限控制

__(1) 设置 mysql 帐号和密码__

以 root 账户登陆 mysql，默认是没有密码

```mysql
update user set password=password('you password') where user='root' and host='localhost';
GRANT ALL PRIVILEGES ON *.* TO 'your username'@'%' IDENTIFIED BY 'your password' WITH GRANT OPTION;

GRANT USAGE ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
GRANT ALL PRIVILEGES ON *.* TO root@'%' IDENTIFIED BY 'root';
FLUSH PRIVILEGES;
```

__(2) 去除匿名用户__

```mysql
delete from mysql.user where User='';   # 删除匿名用户，使用root用户登录数据库
flush privileges;
```

## 五、错误处理

MySQL 日志存储目录： my.cnf 中配制了 datadir 目录，如 datadir=/usr/local/mysql/data，在 datadir 目录下会新建一个 “主机名.err” 的文件

__(1) 问题 1：Can't connect to local MySQL server through socket '/tmp/mysql.sock'__

```text
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)
```

解决方法：

打开 /etc/my.cnf，看看里面配置的 socket 位置是什么目录，如 socket=/var/lib/mysql/mysql.sock

MySQL 服务端启动时报错，则很可能是 mysql 用户没有操作 /var/lib/mysql 的权限。一般将 /var/lib/mysql 用户修改为 mysql 用户可以解决问题。

    chown -R mysql:mysql /var/lib/mysql

MySQL 客户端连接时报错，则很可能是 socket 路径和 “/tmp/mysql.sock” 不一致。建立一个软连接：

    ln -s /var/lib/mysql/mysql.sock /tmp/mysql.sock
    

