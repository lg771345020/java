## jsp

jsp 本质就是一个 servlet

### jsp 指令：

### page指令: 声明jsp页面的一些属性

    <%@ page language="java" contentType="text/html; charset=utf-8" %>

* `contentType` 设置浏览器默认的打开的时候的编码。

* `pageEncoding` 设置 JSP 文件保存到硬盘，以及翻译成 Servlet 保存到硬盘的编码格式。

* `import` 设置JSP中引入其他的类，import 属性可以出现多次的。

* `language` JSP的语言的属性.现在只能是 java。

* `extends` 设置JSP被翻译成 Servlet 后继承的类。默认值 `org.apache.jasper.runtime.HttpJspBase` 。修改这个值得话一定要使这个类是一个 Servlet 才可以。

* `autoFlush` 设置自动刷出缓存

* `buffer` JSP 的缓冲区的大小。默认 8kb 。

* `session` 默认值是 true 。设置 JSP 的页面中是否可以直接使用session对象。

* `isELIgnored` 设置 JSP 是否忽略 EL 表达式.默认值 false 不忽略.可以在 JSP 中写 EL 表达式。

* `isErrorPage` 设置 JSP 页面中出现错误信息应该如何处理的。

* `errorPage` 设置 JSP 页面中出现错误信息应该如何处理的。

### include指令: 用于引入其他的页面

      <%@ include file="logo.jsp" %>

* `file` 属性中不能使用变量，不能传递参数

### taglib指令：用于在JSP中引入标签库

      <%@ taglib uri="名称空间" prefix="前缀" %>

### jsp 9大内置对象 ☆☆☆

jsp 页面上可以直接使用的对象

对象 | 说明 | 
---- | ---- | ----
`out` | JspWriter | 
`request` | HttpServletRequest |
`response` | HttpServletResponse |
`session` | HttpSession |
`exception` | Throwable |
`page` | Servlet(this) |
`pageContext` | pageContext |
`config` | ServletConfig |
`application` | ServletContext | 

### 	JSP的四个域对象：

对象 | 作用范围 | 说明
---- | ---- | ---
pageContext | pageScope | 页面范围			
request | requestScope | 请求范围
session | sessionScope | 会话范围
application | applicationScope | 应用范围


### jsp 脚本：

* `<%  ... %>` java 代码片段

* `<%= ... %>` 输出变量

* `<%! ... %>` 声明变量

访问index.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=utf-8" %>
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
<%
    int i = 4;
    System.out.println(i);
%>

<%
    out.print("哈哈");
%>

<%!
    int k =5;
%>

<%= k %>
</body>
</html>
```
访问 index.jsp ，生成一个 servlet ，IDEA 文件位置 `{user.home}\.IntelliJIdea2017.2\system\tomcat\web04\work\Catalina\localhost\ROOT\org\apache\jsp\index_jsp.java`

```java
package org.apache.jsp;

import javax.servlet.*;
import javax.servlet.http.*;
import javax.servlet.jsp.*;

public final class index_jsp extends org.apache.jasper.runtime.HttpJspBase
    implements org.apache.jasper.runtime.JspSourceDependent,
                 org.apache.jasper.runtime.JspSourceImports {


    int k =5;

  private static final javax.servlet.jsp.JspFactory _jspxFactory =
          javax.servlet.jsp.JspFactory.getDefaultFactory();

  private static java.util.Map<java.lang.String,java.lang.Long> _jspx_dependants;

  private static final java.util.Set<java.lang.String> _jspx_imports_packages;

  private static final java.util.Set<java.lang.String> _jspx_imports_classes;

  static {
    _jspx_imports_packages = new java.util.HashSet<>();
    _jspx_imports_packages.add("javax.servlet");
    _jspx_imports_packages.add("javax.servlet.jsp");
    _jspx_imports_packages.add("javax.servlet.http");
    _jspx_imports_classes = null;
  }

  private volatile javax.el.ExpressionFactory _el_expressionfactory;
  private volatile org.apache.tomcat.InstanceManager _jsp_instancemanager;

  public java.util.Map<java.lang.String,java.lang.Long> getDependants() {
    return _jspx_dependants;
  }

  public java.util.Set<java.lang.String> getPackageImports() {
    return _jspx_imports_packages;
  }

  public java.util.Set<java.lang.String> getClassImports() {
    return _jspx_imports_classes;
  }

  public javax.el.ExpressionFactory _jsp_getExpressionFactory() {
    if (_el_expressionfactory == null) {
      synchronized (this) {
        if (_el_expressionfactory == null) {
          _el_expressionfactory = _jspxFactory.getJspApplicationContext(getServletConfig().getServletContext()).getExpressionFactory();
        }
      }
    }
    return _el_expressionfactory;
  }

  public org.apache.tomcat.InstanceManager _jsp_getInstanceManager() {
    if (_jsp_instancemanager == null) {
      synchronized (this) {
        if (_jsp_instancemanager == null) {
          _jsp_instancemanager = org.apache.jasper.runtime.InstanceManagerFactory.getInstanceManager(getServletConfig());
        }
      }
    }
    return _jsp_instancemanager;
  }

  public void _jspInit() {
  }

  public void _jspDestroy() {
  }

  public void _jspService(final javax.servlet.http.HttpServletRequest request, final javax.servlet.http.HttpServletResponse response)
      throws java.io.IOException, javax.servlet.ServletException {

    final java.lang.String _jspx_method = request.getMethod();
    if (!"GET".equals(_jspx_method) && !"POST".equals(_jspx_method) && !"HEAD".equals(_jspx_method) && !javax.servlet.DispatcherType.ERROR.equals(request.getDispatcherType())) {
      response.sendError(HttpServletResponse.SC_METHOD_NOT_ALLOWED, "JSPs only permit GET POST or HEAD");
      return;
    }

    final javax.servlet.jsp.PageContext pageContext;
    javax.servlet.http.HttpSession session = null;
    final javax.servlet.ServletContext application;
    final javax.servlet.ServletConfig config;
    javax.servlet.jsp.JspWriter out = null;
    final java.lang.Object page = this;
    javax.servlet.jsp.JspWriter _jspx_out = null;
    javax.servlet.jsp.PageContext _jspx_page_context = null;


    try {
      response.setContentType("text/html; charset=utf-8");
      pageContext = _jspxFactory.getPageContext(this, request, response,
      			null, true, 8192, true);
      _jspx_page_context = pageContext;
      application = pageContext.getServletContext();
      config = pageContext.getServletConfig();
      session = pageContext.getSession();
      out = pageContext.getOut();
      _jspx_out = out;

      out.write("\r\n");
      out.write("<!doctype html>\r\n");
      out.write("<html lang=\"en\">\r\n");
      out.write("<head>\r\n");
      out.write("    <meta charset=\"UTF-8\">\r\n");
      out.write("    <meta name=\"viewport\"\r\n");
      out.write("          content=\"width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0\">\r\n");
      out.write("    <meta http-equiv=\"X-UA-Compatible\" content=\"ie=edge\">\r\n");
      out.write("    <title>Document</title>\r\n");
      out.write("</head>\r\n");
      out.write("<body>\r\n");

    int i = 4;
    System.out.println(i);

      out.write("\r\n");
      out.write("\r\n");

    out.print("哈哈");

      out.write("\r\n");
      out.write("\r\n");
      out.write("\r\n");
      out.write("\r\n");
      out.print( k );
      out.write("\r\n");
      out.write("</body>\r\n");
      out.write("</html>");
    } catch (java.lang.Throwable t) {
      if (!(t instanceof javax.servlet.jsp.SkipPageException)){
        out = _jspx_out;
        if (out != null && out.getBufferSize() != 0)
          try {
            if (response.isCommitted()) {
              out.flush();
            } else {
              out.clearBuffer();
            }
          } catch (java.io.IOException e) {}
        if (_jspx_page_context != null) _jspx_page_context.handlePageException(t);
        else throw new ServletException(t);
      }
    } finally {
      _jspxFactory.releasePageContext(_jspx_page_context);
    }
  }
}
```

