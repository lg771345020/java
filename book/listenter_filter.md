## Listener

监听器就是一个 Java 类用来监听其他的 JavaBean 的变化

监听器的术语:

* 事件源		：被监听的对象.汽车
* 监听器对象	：监听的对象.汽车上报警器
* 事件源与监听器绑定：
* 事件		：指的是事件源的改变.
* 获得事件源对象.

### Servlet的监听器

监听 `ServletContext`, `HttpSession`, `ServletRequest`。事件源和监听器绑定通过配置完成。

Servlet 中的监听器:提供了8个监听器.

一类(3)：监听三个域对象的创建和销毁的监听器

* [ServletContextListener](#servletcontextlistener案例)
    * 创建：服务器启动时候，服务器可以为每个 WEB 应用创建一个单独的 ServletContext
    * 销毁：服务器关闭的时候，或者项目从服务器中移除
    * 作用：用来加载配置文件
    
* `HttpSessionListener`
* `ServletRequestListener`

二类(3)：监听三个域对象的属性变更的监听器(属性添加、移除、替换)

* `ServletContextAttributeListener`
* `HttpSessionAttributeListener`
* `ServletRequestAttributeListener`

三类(2)：监听HttpSession对象中的JavaBean的状态的改变.(绑定,解除绑定,钝化和活化)

* [HttpSessionBindingListener](#httpsessionbindinglistener案例) 监听 HttpSession 中的 JavaBean 的绑定和解绑状态
* [HttpSessionActivationListener](#httpsessionactivationlistener案例) 监听 HttpSession 中的 JavaBean 的钝化和活化的状态

#### ServletContextListener案例

第一步：web.xml 中添加监听器

```xml
<listener>
    <listener-class>com.herolei.listener.MyServletContextListener</listener-class>
</listener>
```

第二步：编写监听类，实现 ServletContextListener 接口

```java
import javax.servlet.ServletContext;
import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;

public class MyServletContextListener implements ServletContextListener
{
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        ServletContext context = sce.getServletContext();
        context.setAttribute("name", "join");
        System.out.println("ServletContext创建...");
    }

    @Override
    public void contextDestroyed(ServletContextEvent servletContextEvent) {
        System.out.println("ServletContext销毁...");
    }
}
```

第二步：index.jsp 页面添加如下内容

```jsp
${name}

<%
    String name = (String) application.getAttribute("name");
    out.print(name);
%>
```

#### HttpSessionBindingListener案例

第一步：UserBean 实现 HttpSessionBindingListener 接口

```java
package com.herolei.bean;

import javax.servlet.http.HttpSessionBindingEvent;
import javax.servlet.http.HttpSessionBindingListener;

public class UserBean implements HttpSessionBindingListener {

    private String name;

    public UserBean(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public void valueBound(HttpSessionBindingEvent event) {
        System.out.println("user 被绑定到 session 中");

    }

    @Override
    public void valueUnbound(HttpSessionBindingEvent event) {
        System.out.println("user 解绑");
    }
}
```

第二步：修改 session_bound.jsp 页面将 user 绑定到 session 中

```jsp
<%@ page import="com.herolei.bean.UserBean" %>
<%@ page language="java" contentType="text/html; charset=utf-8" %>

<%
    //user 绑定到 session 中
    session.setAttribute("user", new UserBean("join"));
%>

<a href="/session_unbound.jsp">解绑session</a>
```

第三步：修改 session_unbound.jsp 页面将 user 解绑

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>

<%
    //解绑 user
    session.removeAttribute("user");
%>
```

访问 session_bound.jsp 页面，再访问 session_unbound.jsp 页面，控制台依次输出 `user 被绑定到 session 中` 和 `user 解绑`

#### HttpSessionActivationListener案例

第一步：UserBean 实现 HttpSessionActivationListener 接口和 Serializable 序列化接口

```java
package com.herolei.bean;

import javax.servlet.http.HttpSessionActivationListener;
import javax.servlet.http.HttpSessionBindingEvent;
import javax.servlet.http.HttpSessionBindingListener;
import javax.servlet.http.HttpSessionEvent;
import java.io.Serializable;

public class UserBean implements HttpSessionBindingListener, HttpSessionActivationListener, Serializable {

    private String name;

    public UserBean(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    //UserBean绑定到session中
    public void valueBound(HttpSessionBindingEvent event) {
        System.out.println("user 被绑定到 session 中");

    }

    @Override
    //UserBean解绑
    public void valueUnbound(HttpSessionBindingEvent event) {
        System.out.println("user 解绑");
    }

    @Override
    //UserBean从session写入磁盘
    public void sessionWillPassivate(HttpSessionEvent httpSessionEvent) {
        System.out.println("user 从 session 写入磁盘");
    }

    @Override
    //UserBean从磁盘写入session
    public void sessionDidActivate(HttpSessionEvent httpSessionEvent) {
        System.out.println("user 从磁盘写入 session");
    }
}
```

第二步：新建 webapp/META-INF/context.xml 文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Context>
	<!-- maxIdleSwap :1分钟 如果session不使用就会序列化到硬盘. directory :session 序列化到硬盘的文件存放的位置. -->
	<!-- session 存放在 tomcat/项目名/work/Catalina/localhost/ROOT/session中 -->
	<Manager className="org.apache.catalina.session.PersistentManager"
		maxIdleSwap="1">
		<Store className="org.apache.catalina.session.FileStore"
			directory="session" />
	</Manager>
</Context>
```

