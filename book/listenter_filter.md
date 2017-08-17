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

第一步：UserBean 实现 HttpSessionActivationListener 接口和 Serializable 序列化接口(必须)

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

第二步：新建 webapp/META-INF/context.xml 文件，设置 session 存储时间和存储地址

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

## Filter过滤器

一个实现了特殊接口的 Java 类.实现对请求资源的过滤的功能，如自动登录、解决网站乱码、进行页面静态化、响应压缩...

Filter 生命周期：过滤器从创建到销毁的过程.

* 创建：服务器启动的时候，服务器就会创建过滤器的对象
* 执行：每次访问被拦截目标资源，过滤器中的 doFilter 的方法就会执行
* 销毁：当服务器关闭的时候，服务器就会销毁 Filter 对象

注意：servlet 默认在第一次请求到来时创建

### FilterChain过滤器链

过滤器链中的过滤器的执行的顺序跟 <filter-mapping> 的配置顺序有关

例子

第一步：配制 web.xml 文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd" version="3.1">

    <filter>
        <filter-name>AFilter</filter-name>
        <filter-class>com.herolei.filter.AFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>AFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <filter>
        <filter-name>BFilter</filter-name>
        <filter-class>com.herolei.filter.BFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>BFilter</filter-name>
        <url-pattern>/index.jsp</url-pattern>
    </filter-mapping>
    
</web-app>
```

第二步：新建 com.herolei.filter.AFilter 类

```java
package com.herolei.filter;

import javax.servlet.*;
import java.io.IOException;

public class AFilter implements Filter {
    @Override
    //过滤器初始化
    public void init(FilterConfig filterConfig) throws ServletException { }

    @Override
    //业务处理
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        System.out.println("aFilter 收到请求");

        chain.doFilter(request, response);

        System.out.println("aFilter 收到响应");
    }

    @Override
    //过滤器销毁
    public void destroy() {}
}
```

第三步：新建 com.herolei.filter.BFilter 类

```java
package com.herolei.filter;

import javax.servlet.*;
import java.io.IOException;

public class BFilter implements Filter {
    @Override
    //过滤器初始化
    public void init(FilterConfig filterConfig) throws ServletException { }

    @Override
    //业务处理
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        System.out.println("bFilter 收到请求");

        chain.doFilter(request, response);

        System.out.println("bFilter 收到响应");
    }

    @Override
    //过滤器销毁
    public void destroy() {}
}
```

访问 index.jsp 页面，结果如下：

```
aFilter 收到请求
bFilter 收到请求
//处理请求
bFilter 收到响应
aFilter 收到响应
```

### Filter实现自动登陆

第一步：新建项目，目录如下:

```

```

第二步：修改配置文件 WEB-INF/web.xml ，添加过滤器

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
         http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         version="3.0">
  <filter>
    <filter-name>AutoLoginFilter</filter-name>
    <filter-class>com.herolei.filter.AutoLoginFilter</filter-class>
  </filter>
  <filter-mapping>
    <filter-name>AutoLoginFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>

  <servlet>
    <servlet-name>LoginServlet</servlet-name>
    <servlet-class>com.herolei.controller.LoginServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>LoginServlet</servlet-name>
    <url-pattern>/login</url-pattern>
  </servlet-mapping>
</web-app>
```

第三步：添加 com.herolei.utils.CookieUtils 工具类

```java
package com.herolei.utils;

import javax.servlet.http.Cookie;

public class CookieUtils {
    //获取cookie
    public static String getCookie(String name, Cookie[] cookies) {
        if(cookies != null) {
            for(Cookie c: cookies) {
                if(name.equals(c.getName())) {
                    return c.getValue();
                }
            }
        }
        return "";
    }
}
```

第四步：添加 com.herolei.bean.UserBean 用户类 和 com.herolei.bean.ConstantBean 常量类

```java
package com.herolei.bean;

public class UserBean {

    private String name;
    private String password;

    public UserBean(String name, String password) {
        this.name = name;
        this.password = password;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}
```

```java
package com.herolei.bean;

public interface ConstantBean {

    String ISAUTOLOGIN = "1";
    String ISREMEMBER = "1";
}
```

第五步：验证登陆 com.herolei.controller.LoginServlet

添加 com.herolei.service.LoginService ，Service 层验证登陆

```java
package com.herolei.service;

public class LoginService {
    //验证登陆是否成功
    public boolean loginCheck(String username, String password) {
        return "admin".equals(username) && "adminadmin".equals(password);
    }
}
```

添加 com.herolei.controller.LoginServlet ，Control 层验证登陆

```java
package com.herolei.controller;

import com.herolei.bean.ConstantBean;
import com.herolei.bean.UserBean;
import com.herolei.service.LoginService;

import javax.servlet.ServletException;
import javax.servlet.http.*;
import java.io.IOException;

public class LoginServlet extends HttpServlet {

    public void service(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException {
        req.setCharacterEncoding("utf-8");
        String username = req.getParameter("username");
        String password = req.getParameter("password");
        String isRemember = req.getParameter("isRemember");
        String isAutoLogin = req.getParameter("isAutoLogin");
        //cookie有效期单位为s
        int maxAge = 60 * 10;

        //勾选记住我，将用户名和密码存储到cookie中
        if(ConstantBean.ISREMEMBER .equals(isRemember)) {
            Cookie cusername = new Cookie("username", username);
            cusername.setMaxAge(maxAge);
            cusername.setPath(req.getContextPath());
            Cookie cpassword = new Cookie("password", password);
            cpassword.setMaxAge(maxAge);
            cpassword.setPath(req.getContextPath());
            res.addCookie(cusername);
            res.addCookie(cpassword);
        }

        //勾选自动登陆，将是否自动登陆存储到cookie中
        if(ConstantBean.ISAUTOLOGIN.equals(isAutoLogin)) {
            Cookie cisAutoLogin = new Cookie("isAutoLogin", isAutoLogin);
            cisAutoLogin.setMaxAge(maxAge);
            cisAutoLogin.setPath(req.getContextPath());
            res.addCookie(cisAutoLogin);
        }

        LoginService loginService = new LoginService();
        //登陆成功
        if(loginService.loginCheck(username, password)) {
            HttpSession session = req.getSession();
            session.setAttribute("user", new UserBean(username, password));
            res.sendRedirect("index.jsp");
        //登陆失败
        } else {
            String msg = "POST".equals(req.getMethod()) ? "用户名或密码错误！" : "";
            req.setAttribute("msg", msg);
            req.getRequestDispatcher("login.jsp").forward(req, res);
        };
    }
}
```

第六步：添加 com.herolei.filter.AutoLoginFilter ，实现自动登陆验证

```java
package com.herolei.filter;

import com.herolei.bean.ConstantBean;
import com.herolei.bean.UserBean;
import com.herolei.utils.CookieUtils;
import com.herolei.service.LoginService;

import javax.servlet.*;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;

public class AutoLoginFilter implements Filter {
    public void init(FilterConfig filterConfig) throws ServletException {}

    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest req = (HttpServletRequest) servletRequest;
        HttpServletResponse res = (HttpServletResponse) servletResponse;
        HttpSession session = req.getSession();
        Cookie[] cookies = req.getCookies();

        //用户已经登陆，放行
        if(session.getAttribute("user") != null) {
            filterChain.doFilter(req, res);
        //访问登陆注册页面，放行
        } else if(req.getRequestURI().contains("/login")) {
            filterChain.doFilter(req, res);
        //cookie不为空时，勾选自动登陆时，验证用户名密码是否正确，正确则放行，错误跳转到登陆页面
        //              没有勾选自动登陆，则跳转到登陆页面
        } else if(cookies != null) {

            String username = CookieUtils.getCookie("username", req.getCookies());
            String password = CookieUtils.getCookie("password", req.getCookies());
            String isAutoLogin = CookieUtils.getCookie("isAutoLogin", req.getCookies());
            //勾选自动登陆
            if(ConstantBean.ISAUTOLOGIN.equals(isAutoLogin)) {
                LoginService loginService = new LoginService();
                if(loginService.loginCheck(username, password)) {
                    filterChain.doFilter(req, res);
                    session.setAttribute("user", new UserBean(username, password));
                } else {
                    res.sendRedirect("/login");
                }
            } else {
                res.sendRedirect("/login");
            }
        //cookie为空时，放行
        } else {
            System.out.println(11111);
            res.sendRedirect("/login");
            //req.getRequestDispatcher("/login").forward(req, res);
        }
    }

    public void destroy() {}
}
```

第七步：添加 jsp 文件

添加 index.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=utf-8" %>
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Login</title>
</head>
<body>
欢迎来到首页
<a href="/logout.jsp">退出登陆</a>
</body>
</html>
```

添加 login.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=utf-8" %>
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Login</title>
</head>
<body>
<form action="/login" method="post">
    用户名：<input type="text" name="username"><br>
    密 码：<input type="text" name="password"><br>
    <input type="checkbox" name="isRemember" value="1">记住我
    <input type="checkbox" name="isAutoLogin" value="1">自动登陆<br>
    <input type="submit" value="提交">${msg}
</form>
</body>
</html>
```

添加 logout.jsp 

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%
    session.removeAttribute("user");
%>
退出登陆
```
