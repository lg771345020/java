## jsp

jsp 本质就是一个 servlet

### jsp 指令：

#### page指令: 声明jsp页面的一些属性

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

#### include指令: 用于引入其他的页面

      <%@ include file="logo.jsp" %>

* `file` 属性中不能使用变量，不能传递参数

#### taglib指令：用于在JSP中引入标签库

      <%@ taglib uri="名称空间" prefix="前缀" %>

### jsp内置对象和域对象

#### jsp 9大内置对象 ☆☆☆

jsp 页面上可以直接使用的对象

对象 | 说明 
---- | ----
`out` | JspWriter 
`request` | HttpServletRequest
`response` | HttpServletResponse
`session` | HttpSession
`exception` | Throwable
`page` | Servlet(this)
`pageContext` | pageContext
`config` | ServletConfig
`application` | ServletContext 

#### JSP的四个域对象：

对象 | 作用范围 | 说明
---- | ---- | ---
pageContext | pageScope | 页面范围			
request | requestScope | 请求范围
session | sessionScope | 会话范围
application | applicationScope | 应用范围

### JSP的动作标签：

* 请求转发：`<jsp:forward page="/demo1/demo5.jsp"></jsp:forward>`

* 动态包含：`<jsp:include page="/demo1/include2/logo.jsp"></jsp:include>`


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

## EL表达式

* 获取简单数据

```
${pageScopt|requestScope|sessionScope|applicationScope.属性名} 
${属性名}依次从pageContext, request, session, application查找指定的属性，若无则返回""
```

* 获取数组中的数据 `${域中的名称[index]}`

* 获取list中的数据 `${域中的名称[index]}`

* 获取map中的数据 `${域中的名称.键名}`
    
* javabean 导航 `${域中javabean名称.bean属性}`

例子：

```java
<%@ page import="java.util.Map" %>
<%@ page import="java.util.List" %>
<%@ page import="java.util.ArrayList" %>
<%@ page import="java.util.HashMap" %>
<%@ page import="com.herolei.bean.Person" %>
<%@ page language="java" contentType="text/html; charset=utf-8" %>
<h1>EL获取数据</h1>
<%
    pageContext.setAttribute("pname", "王守义");
    request.setAttribute("rname", "王凤儿");
    session.setAttribute("sname", "王如花");
    application.setAttribute("aname", "王芙蓉");
%>
<h3>传统方式</h3>
<%= pageContext.getAttribute("pname")%>
<%= request.getAttribute("rname")%>
<%= session.getAttribute("sname")%>
<%= application.getAttribute("aname")%>
<h3>EL的方式</h3>
${ pageScope.pname }
${ requestScope.rname }
${ sessionScope.sname }
${ applicationScope.aname }
<hr/>
<%
    //pageContext.setAttribute("name", "王守义");
    //request.setAttribute("name", "王凤儿");
    session.setAttribute("name", "王如花");
    application.setAttribute("name", "王芙蓉");
%>
${ name }
<h3>EL获得数组的数据</h3>
<%
    String[] arrs = {"王守义","王如花","王凤儿"};
    pageContext.setAttribute("arrs", arrs);
%>
${ arrs[1] }

<h3>EL获得List集合的数据</h3>
<%
    List<String> list = new ArrayList<String>();
    list.add("aaa");
    list.add("bbb");
    list.add("ccc");
    pageContext.setAttribute("list", list);
%>
${ list[1] }

<h3>获得Map集合的数据</h3>
<%
    Map<String,String> map = new HashMap<String,String>();
    map.put("aaa", "111");
    map.put("bbb", "222");
    map.put("ccc.ddd", "333");
    pageContext.setAttribute("map", map);
%>
${ map["ccc.ddd"] }

<h3>EL获得JavaBean中的数据</h3>
<%
    Person person = new Person();
    person.setId(1);
    person.setName("王美丽");
    pageContext.setAttribute("person", person);
%>
${ person.name }
```

Person 即为一个 javabean 对象

```java
public class Person {
    private int id;
    private String name;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

**补充：javabean 规范：**

* 必须是一个公共的具体的类 public class

* 提供私有的字段的方法 

```
    //username 即为一个字段
    private String username; 
```

* 提供公共访问字段的方法 get|set|is 方法

```
    //一旦有公共的方法后，get|set|is后的内容，将首字母小写，称之为bean属性
    //name 就是一个bean属性
    public String getName() { return username; }
```   

* 提供一个无参构造器

* 一般实现序列化接口 Serializable

### EL执行运算

* 算数运算：`+`、`-`、`*`、`/`、`%`

* 逻辑运算：`>`、`>=`、`<`、`<=`、`==`、`!=`、`empty`

* 关系运算：`&&`、`||`、`!`

例子：

```jsp
<%@ page import="com.herolei.controller.Person" %>
<%@ page language="java" contentType="text/html; charset=utf-8" %>
<h1>EL执行运算</h1>

<h3>EL执行算数运算</h3>
<%
    pageContext.setAttribute("n1", "10");
    pageContext.setAttribute("n2", "20");
    pageContext.setAttribute("n3", "40");
    pageContext.setAttribute("n4", "60");
%>
${ n1+n2+n3 }

<h3>EL执行逻辑运算</h3>
${ n1 < n2 }
${ n1 > n2 }

<h3>EL执行关系运算</h3>
${ n1<n2 && n3<n4 }
${ n1<n2 || n3<n4 }
<%
    Person person = new Person();
    pageContext.setAttribute("person", person);
%>
${ empty person }
${ not empty person }
```

### EL内置对象

对象 | 说明
---- | ----
${pageScope} | pageScope
${requestScope} | requestScope
${sessionScope} | sessionScope
${applicationScope} | applicationScope
${ param } | 相当于 request.getParameter()
${ paramValues } | 相当于 request.getParameterValues()
${ header } | 获得请求头，一个 key 对应一个 value
${ headerValues } | 获得请求头，一个 key 对应多个v alue 
${ initParam } | 获得初始化参数
${ cookie } | 获得 Cookie 的信息
${pageContext} | 相当于 pageContext 对象

例子：

```jsp
//项目名称
${pageContext.request.contextPath}
```

## JSTL的标签库

使用步骤：

1. 导入 jar 包(jstl.jar 和 standard.jar)

2. 在页面上导入标签库 `<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>`

例子：

```jsp
<%@ page import="java.util.*" %>
<%@ page language="java" contentType="text/html; charset=utf-8" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/functions" prefix="fn" %>
<h1>1、if标签</h1>
<c:if test="${ 6 > 5 }">
    哈哈
</c:if>

<h1>2、forEach标签</h1>
<c:forEach begin="1" end="20" step="2" var="i" varStatus="vs">
    ${i}-${vs.count}-${vs.current}<br>
</c:forEach>

<h1>forEach遍历list</h1>
<%
    List list = new ArrayList();
    list.add(1);
    list.add(2);
    list.add(3);
    request.setAttribute("list", list);
%>
<c:forEach items="${list}" var="n">
    ${n}<br>
</c:forEach>

<h1>forEach遍历set</h1>
<%
    Set set = new HashSet();
    set.add(1);
    set.add(2);
    set.add(3);
    request.setAttribute("set", set);
%>
<c:forEach items="${set}" var="n">
    ${n}<br>
</c:forEach>

<h1>forEach遍历map</h1>
<%
    Map map = new HashMap();
    map.put("name", "join");
    map.put("age", "20");
    map.put("salary", "2000");
    request.setAttribute("map", map);
%>
<c:forEach items="${map}" var="en" varStatus="vs">
    ${en.key}-${en.value}-${vs.count}<br>
</c:forEach>

<h1>3、set</h1>
<%--默认相当于pageContext.setAttribute("name", "Tom")--%>
<c:set var="name" value="Tom" scope="page"/>

<h1>choose</h1>
<c:choose>
    <c:when test="${name=='join'}">
        join
    </c:when>
    <c:when test="${name=='tom'}">
        tom
    </c:when>
    <c:otherwise>
        default
    </c:otherwise>
</c:choose>

<h1>4、fn函数库</h1>
${ fn:length("HelloWorld") }
${ fn:toLowerCase("ABCDEFG") }
${ fn:toUpperCase("abcdefg") }
${ fn:contains("www.baidu.com","baidu") }
```