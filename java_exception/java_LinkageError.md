##  java.lang.LinkageError: loader constraint violation

Java.lang.LinkageError: loader constraint violation 是由于容器提供的包和 web 应用引入的包冲突引起的。

```
j2ee.jar - interfaces are implemented by Tomcat
jasper-*.jar - causes Exceptions, or will do when version of Tomcat is upgraded
jboss.jar - part of jboss - should not be part of your app
jsp-api.jar - causes Exceptions, or will do when version of Tomcat is upgraded
gwt-user.jar - contains javax.servlet.Servlet, so gets ignored by Tomcat
gwt-dev-linux.jar - contains javax.servlet.Servlet, so gets ignored by Tomcat
gwt-dev-windows.jar - contains javax.servlet.Servlet, so gets ignored by Tomcat
rt.jar - not sure why you would, but some have...
servlet-api.jar - this is really old too, switch to compiling with j2ee.jar
servlet.jar - this is really old too, switch to compiling with j2ee.jar
tools.jar - part of VM
```

我之前将 servlet-api.jar 包拷贝到 C:\Program Files (x86)\Java\jdk1.8.0_131\jre\lib\ext 目录下，删除 servlet-api.jar 后，项目正常跑起来了。

### maven引入servlet依赖，并把其scope设置为provided

```xml
<dependency>  
    <groupId>javax.servlet</groupId>  
    <artifactId>servlet-api</artifactId>  
    <version>2.5</version>  
    <scope>provided</scope>  
</dependency> 
```
