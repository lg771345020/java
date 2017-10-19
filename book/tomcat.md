## Tomcat 安装

1、下载Tomcat

登录网址：http://tomcat.apache.org/download-80.cgi

2、将 apache-tomcat-8.5.20.tar.gz 解压，并复制到 /usr/local/tomcat 目录下。

```
#①解压
tar -zxvf apache-tomcat-8.5.20.tar.gz
#②复制
mv apache-tomcat-8.5.20 /usr/local/tomcat
```

tomcat 安装完毕！

## Tomcat 配置

1、修改 Tomcat/conf/tomcat-users.xml ，添加用户 manager-gui 和 admin-gui 用户

```xml
<tomcat-users>
  <role rolename="manager-gui"/>
  <role rolename="admin-gui"/>
  <user username="tomcat" password="tomcat" roles="manager-gui,admin-gui"/>
</tomcat-users>
```

2、修改 Tomcat/conf/context.xml 和 Tomcat/webapps/manager/META-INF/context.xml，注释 <Value /> 标签

```xml
<Context antiResourceLocking="false" privileged="true" >
  <!--
    限制来访IP的，127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1，是正则表达式，表示IPv4和IPv6的本机环回地址
    修改正则表达式，比如只允许内网网段192.168.180.*，或注释 <Value /> 标签
  -->
  <!-- 
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> 
 -->
</Context>
```

3、启动 Tomcat ，访问 localhost:8080/manager，输入用户名密码，即可跳转到 `localhost:8080/manager/html` 管理页面

如若仍不能访问，可按以上方法修改 Tomcat/webapps/manager/META-INF/context.xml 即可。

```
#进入tomcat/bin目录
[root@localhost ~]# cd /usr/local/tomcat/apache-tomcat-8.5.20/bin
#启动tomcat
[root@localhost bin]# ./startup.sh 
#关闭tomcat
[root@localhost bin]# ./shutdown.sh
#启动tomcat并显示控制台日志信息
[root@localhost bin]# ./catalina.sh run
#进入tomcat/logs目录
[root@localhost bin]# cd /usr/local/tomcat/apache-tomcat-8.5.20/logs
#查看tomcat日志
[root@localhost logs]# tail -f catalina.out
```

**补充：** tail命令
```
head -6 catalina.out  ## 前 6 行
tail -25 catalina.out ## 最后的 25 行
head -20 file | tail -10   ##第 11 行到第 20 行
tail -f catalina.out  ##参数-f使tail不停地去读最新的内容，有实时监视的效果
```
```
vim /etc/profile.d/tomcat.sh
export CATALINA_HOME=/usr/local/tomcat
export PATH=$CATALINA_HOME/bin:$PATH
. /etc/profile.d/tomcat.sh
```
