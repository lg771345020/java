## Response 对象

### Response 设置状态码：

```
//1、设置状态码
response.setState(200)
//2、设置错误状态码，针对4xx和5xx
response.setError(404)
```
    
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

例子：

```
//1、重定向方式一：
//res.setStatus(302);
//res.setHeader("location", "https://github.com/");

//2、重定向方式二：
//res.sendRedirect("https://github.com/");

//3、刷新
//res.setHeader("refresh", "3;url=https://github.com/");

//4、中文乱码
//res.setHeader("Content-Type", "text/html;charset=UTF-8");
res.setContentType("text/html;charset=UTF-8");

//5、输出
//PrintWriter out = res.getWriter();//字符流
//out.println("哈哈");

OutputStream out = res.getOutputStream();//字节流，两个流只能使用一个
out.write("哈哈".getBytes("utf-8"));
```

### Response 文件下载：

```java
import javax.servlet.ServletContext;
import javax.servlet.ServletOutputStream;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;

public class DownloadServlet extends HttpServlet {

    public void service(HttpServletRequest req, HttpServletResponse res) throws IOException {
        String filename = "1.txt";
        ServletContext context = this.getServletContext();
        //文件下载
        //1、设置文件mimeType
        String mimeType = context.getMimeType(filename);
        System.out.println(mimeType);
        res.setContentType(mimeType);

        //2、设置下载的头信息
        res.setHeader("content-disposition", "attachment;filename=" + filename);

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