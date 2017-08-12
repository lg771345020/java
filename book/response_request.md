## Response 对象

### Response 设置状态码：

    //1、设置状态码
    response.setState(200)
    //2、设置错误状态码，针对4xx和5xx
    response.setError(404)
    
### Response 设置响应头：

常用的方法:

方法 | 说明 
--- | ---
setHeader(String key, String value) | 设置响应头
setIntHeader(String key, int value) | 设置整数响应头
setDateHeader(String key, long value) | 设置时间响应头
addHeader(String key, String value) | 设置响应头，没有设置则设置，有则追加
addIntHeader(String key, int value) | 设置整数响应头
addDateHeader(String key, long value) | 设置时间响应头

### Response 重定向：

    1、重定向方式一：
    res.setStatus(302);
    res.setHeader("location", "https://github.com/");
  
    2、重定向方式二：
    res.sendRedirect("https://github.com/");

### Response 定时刷新：

    res.setHeader("refresh", "3;url=https://github.com/");
    
### Response 操作响应体：
    
    //字符流
    PrintWriter out = res.getWriter();
    out.println("哈哈");
    
    //字节流，两个流只能使用一个
    OutputStream out = res.getOutputStream();
    out.write("哈哈".getBytes("utf-8"));
    
### Response 中文乱码：

    res.setHeader("Content-Type", "text/html;charset=UTF-8");
    res.setContentType("text/html;charset=UTF-8");

### Response 文件下载：

```java
import sun.misc.BASE64Encoder;
import javax.servlet.ServletContext;
import javax.servlet.ServletOutputStream;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.FileInputStream;
import java.io.IOException;
import java.net.URLEncoder;

public class DownloadServlet extends HttpServlet {

    public void service(HttpServletRequest req, HttpServletResponse res) throws IOException {
        String filename = req.getParameter("filename");
        filename = new String(filename.getBytes("iso8859-1"),"utf-8");
        
        ServletContext context = this.getServletContext();
        //文件下载
        //1、设置文件mimeType
        String mimeType = context.getMimeType(filename);
        res.setContentType(mimeType);

        //2、设置下载的头信息
        String agent = req.getHeader("user-agent");
        System.out.println(agent);
        if (agent.contains("MSIE")) {
            // IE浏览器
            filename = URLEncoder.encode(filename, "utf-8");
            filename = filename.replace("+", " ");
        } else if (agent.contains("Firefox")) {
            // 火狐浏览器
            BASE64Encoder base64Encoder = new BASE64Encoder();
            filename = "=?utf-8?B?" + base64Encoder.encode(filename.getBytes("utf-8")) + "?=";
        } else {
            // 其它浏览器
            filename = URLEncoder.encode(filename, "utf-8");
        }

        res.setHeader("Content-Disposition", "attachment;filename=" + filename);

        //3、对拷流
        //输入流
        //InputStream in = context.getResourceAsStream("static/download/1.txt");
        FileInputStream in = new FileInputStream("F:\\test\\java_test\\web04\\src\\test\\java\\1.txt");
        //输出流
        ServletOutputStream out = res.getOutputStream();

        int len = -1;
        byte[] b = new byte[1024];
        while ((len = in.read(b)) != -1) {
            out.write(b, 0, len);
        }

        in.close();
        out.close();
    }
}
```


## Request 对象

### Request 操作请求行

    //获取请求方式
    String method = req.getMethod();

    //获取ip地址
    String ip = req.getRemoteAddr();

    //在java中获取项目名称
    String path = req.getContextPath();

    //获取从项目名到参数之前的内容 /login/check
    String uri = req.getRequestURI();

    //获取带协议的完整路径 http://login/check?name=join
    String url = req.getRequestURL().toString();

    //获取请求所有参数
    String queryString = req.getQueryString();

    //获取版本
    String protocol = req.getProtocol();
    
### Request 请求参数

获取参数的三种形式：

```java
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Arrays;
import java.util.Map;

public class Servlet1 extends HttpServlet {

    public void service(HttpServletRequest req, HttpServletResponse res) throws IOException {
        //http://localhost:8080/hello?name=join&pic=a&pic=b
        String name = req.getParameter("name");
        System.out.println("name: " + name);
        //name: join

        String[] pics = req.getParameterValues("pic");
        System.out.println("pics: " + Arrays.toString(pics));
        //pics: [a, b]

        Map<String, String[]> params = req.getParameterMap();

        for(String key: params.keySet()) {
            System.out.println(key + ": " + Arrays.toString(params.get(key)));
            //name: [join]  pic: [a, b]
        }
    }
}
```

### Request 中文乱码

GET/POST 请求，参数使用 `utf-8` 编码，服务器(Tomcat)接受请求后使用 `ISO-8859-1` 解码

    //1、get/post请求
    new String(name.getBytes("ISO-8859-1"), "utf-8");
    
    //2、post请求
    req.setCharacterEncoding("UTF-8");
    
    //3、URLEncoder/URLDecoder
    String encode = URLEncoder.encode("王五", "utf-8")
    //%E7%8E%8B%E4%BA%94
    String decode = URLDecoder.decode("%E7%8E%8B%E4%BA%94", "utf-8")
    //王五
    
**补充：URLEncoder/URLDecoder 使用方法：**

    String s="哈哈";
    String s8 = URLEncoder.encode(s, "utf-8");
    System.out.println(s8);
    //%E5%93%88%E5%93%88

    String so = URLDecoder.decode(s8, "iso-8859-1");
    System.out.println(so);
    //åå

    byte[] b = so.getBytes("iso-8859-1");
    String _s = new String(b, "utf-8");
    System.out.println(_s);
    //哈哈

**补充：设置Tomcat编码方式**

修改 Tomcat 配置文件 `conf/server.xml` 
    
    <Connector port="8080" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443"
               URIEncoding="UTF-8" useBodyEncodingForURI="true"/>
               
* `URIEncoding` GET 请求编码方式，默认 iso-8859-1 

* `useBodyEncodingForURI` 在 Tomcat4 中 GET/POST 的编码是一样的，所以只要在过滤器中通过 request.setCharacterEncoding 设定一次就可以解决 GET/POST 的问题。然而，在 Tomcat 高版本中，GET/POST 的处理是分开进行的，GET 请求通过 URIEncoding 进行处理，POST 请求通过 request.setCharacterEncoding 处理，为了保持兼容，就有了这个设定。

### 转发和重定向的区别：

**重定向**

    response.sendRedirect(String path);

**转发**

    request.getRequestDispatcher(String path).forward(request,response);
    
**区别**

1. 转发的地址栏不变的；重定向的地址栏发生变化的。

2. 转发是一次请求一次响应；重定向是两次请求两次响应。

3. request 域对象存取的值在转发中是有效的；在重定向无效的。

4. 转发的路径不需要加工程名；重定向的路径需要加工程名。
