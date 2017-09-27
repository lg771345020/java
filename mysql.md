# Centos7下完美安装并配置mysql5.6

## 一、MySQL安装

1、下载安装包 mysql-5.6.37-linux-glibc2.12-x86_64.tar.gz
 
下载地址: https://dev.mysql.com/downloads/mysql/5.6.html#downloads

2、安装依赖

    yum -y install perl perl-devel autoconf libaio
 
3、卸载已安装的mysql

    [root@localhost~]# yum list installed | grep mysql  //查询已安装的mysql
    [root@localhost~]# yum -y remove mysql-libs.x86_64  //卸载 

4、创建mysql用户组和mysql的用户

```
#创建mysql用户组
[root@localhost~]# groupadd mysql
#创建一个用户名为mysql的用户并加入mysql用户组
[root@localhost ~]# useradd -g mysql mysql 
```

5、将下载的二进制解压缩包放到/usr/local/目录下，并重命名为mysql。

```
[root@localhost ~]# tar -zxvf mysql-5.6.37-linux-glibc2.12-x86_64.tar.gz
[root@localhost ~]# mv mysql-5.6.37-linux-glibc2.12-x86_64 /usr/local/mysql
```

6、在etc下新建配置文件my.cnf，并在该文件内添加以下代码：

```
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8 
socket=/var/lib/mysql/mysql.sock
[mysqld]
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

7、进入安装mysql安装目录

```
[root@localhost ~]# cd /usr/local/mysql
#修改当前目录拥有者为mysql用户
[root@localhost mysql]# chown -R mysql:mysql ./
#安装数据库
[root@localhost mysql]# ./scripts/mysql_install_db --user=mysql
#修改当前data目录拥有者为mysql用户
[root@localhost mysql]# chown -R mysql:mysql data
``` 

到此mysql安装完毕！

## 二、MySQL配置

1、添加mysql服务开机自启动

```
#授予my.cnf的最大权限
[root@localhost ~]# chown 777 /etc/my.cnf
#复制启动脚本到资源目录
[root@localhost mysql]# cp ./support-files/mysql.server /etc/rc.d/init.d/mysqld
#增加mysqld服务控制脚本执行权限
[root@localhost mysql]# chmod +x /etc/rc.d/init.d/mysqld
#将mysqld服务加入到系统服务
[root@localhost mysql]# chkconfig --add mysqld
检查mysqld服务是否已经生效
[root@localhost mysql]# chkconfig --list mysqld
```

命令输出类似下面的结果：

mysqld 0:off 1:off 2:on 3:on 4:on 5:on 6:off
表明mysqld服务已经生效，在2、3、4、5运行级别随系统启动而自动启动，以后可以使用service命令控制mysql的启动和停止。

2、启动msql

```
#启动msql
[root@localhost mysql]# service mysqld start
停止msql
[root@localhost mysql]# service mysqld stop
```
     
3、将mysql的bin目录加入PATH环境变量，编辑/etc/profile文件

```
[root@localhost mysql]# vi /etc/profile
#在文件最后添加如下信息：
[root@localhost mysql]# export PATH=$PATH:/usr/local/mysql/bin
#执行下面的命令使所做的更改生效：
[root@localhost mysql]# . /etc/profile
```

或

```
#把mysql客户端放到默认路径
ln -s /usr/local/mysql/bin/mysql /usr/local/bin/mysql
```

## 三、权限控制

1、设置mysql帐号和密码，以root账户登陆mysql，默认是没有密码

```mysql
update user set password=password('you password') where user='root' and host='localhost';
GRANT ALL PRIVILEGES ON *.* TO 'your username'@'%' IDENTIFIED BY 'your password' WITH GRANT OPTION;
```
2、去除匿名用户

```mysql
# 删除匿名用户，使用root用户登录数据库
delete from mysql.user where User='';
flush privileges;
```

    
