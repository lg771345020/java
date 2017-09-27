## Tomcat 部署

第一步：修改 Tomcat/conf/tomcat-users.xml ，添加用户 manager-gui 和 admin-gui 用户

```xml
<tomcat-users>
  <role rolename="manager-gui"/>
  <role rolename="admin-gui"/>
  <user username="tomcat" password="tomcat" roles="manager-gui,admin-gui"/>
</tomcat-users>
```

第二步：修改 Tomcat/conf/context.xml 和 Tomcat/webapps/manager/META-INF/context.xml，注释 <Value /> 标签

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

第三步，启动 Tomcat ，访问 localhost:8080/manager，输入用户名密码，即可跳转到 `localhost:8080/manager/html` 管理页面

如若仍不能访问，可按以上方法修改 Tomcat/webapps/manager/META-INF/context.xml 即可。
