# servlet

sun 公司制订的一种用来扩展 web 服务器功能的组件规范。

1. 实现 Servlet 接口或者继承 HttpServlet 抽象类。

2. 编译

3. 打包，即创建一个具有如下结构的文件夹：

```
    appname(应用名)
      └──WEB-INF
          ├──classes    .class文件
          ├──lib        .jar文件，lib文件夹可选
          └──web.xml    部署描述文件
```

4. 将 step3 创建的文件夹拷贝到 servlet 窗口特定的文件夹下。也可以将 step3 创建的文件夹使用 jar 命令压缩，生成 .war 为后缀的文件。
 
5. 启动 servlet 容器，访问 servlet。`http://ip:port/appname/url-pattern`


### 完成第一个 servlet 程序

1. 新建一个 web 工程，目录结构如下

```
    web01(工程文件存放目录)
      ├──src
      │   └──com.herolei            .class文件
      │       └──HelloWorldServlet  部署描述文件 
      ├──web
      │   ├──WEB-INF
      │   │   └──web.xml            web配置文件
      │   └──index.jsp
      └──web01.iml
```

2. 在 `src -> com.herolei -> HelloWorldServlet.java` 文件中中添加如下代码

```java
package com.herolei;

import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

public class HelloWorldServlet extends HttpServlet {

    public void service(HttpServletRequest req, HttpServletResponse res) throws IOException {
        //设置一个消息头content-type，告诉浏览器返回的数据类型是一个html文档
        res.setContentType("text/html");
        //获得一个输出流
        PrintWriter out = res.getWriter();
        //调用流的方法进行输出，其实质是将处理结果写到了res对象上
        out.println("<h1>Hello Word</h1>");
        out.close();
    }
}
```

3. 修改 `web -> WEB-INF -> web.xml` 配制文件 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">
    <servlet>
        <servlet-name>helloWord</servlet-name>
        <!--类名要完整(包名，类名都要全)-->
        <servlet-class>com.herolei.HelloWorldServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>helloWord</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
</web-app>
```

4. 启动程序，访问 http://localhost:8080/hello 


 