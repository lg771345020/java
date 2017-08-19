## 注解

程序中有注释(给开发人员看)和注解(给计算机看)。注解使用：学习框架支持注解开发。

### JKD提供的注解 

* `@Override` 描述方法的重写
* `@SuppressWarnings` 压制警告
* `@Deprecated` 标记过时

### 自定义注解

`class` 定义一个类
`interface` 定义一个借口
`enum` 定义一个枚举
`@interface` 定义一个注解

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

//注解使用位置①TYPE 类、接口 ②METHOD 方法 ③FIELD 字段
@Target(ElementType.METHOD)
//注解有效期①SOURCE 源码 ②CLASS 源码和字节码 ③RUNTIME 有码、字节码和运行时
@Retention(RetentionPolicy.RUNTIME)
public @interface MyTest {
    //1、基本数据类型
    int a() default 1;
    //2、基本数据类型
    String b();
    //3、Class
    Class clazz();
    //4、枚举
    Color c();
    //5、以上类型的一维数组
    String[] arrs();
    //6、如果属性名称为value，那么使用时value可省略(只有value属性时)
    String[] value() default "/*";
    enum Color{RED, GREEN, YELLOW}
}
```

例子：判断方法上有无注解

```java
public class CoreRunner {

	public static void main(String[] args) {
		// 反射：获得类的字节码对象.Class
		Class clazz = AnnotationDemo3.class;
		// 获得测试类中的所有的方法:
		Method[] methods = clazz.getMethods();
		// 遍历数组:
		for (Method method : methods) {
			// System.out.println(method.getName());
			// 判断方法上是否有@MyTest注解：
			boolean flag = method.isAnnotationPresent(MyTest.class);
			// System.out.println(method.getName()+"   "+flag);
			if(flag){
				// 让这个方法执行:
				try {
					method.invoke(clazz.newInstance(), null);
				} catch (Exception e) {
					e.printStackTrace();
				}
			}
		}
	}
}
```

jdbc 实现注解开发：

```java
public class JDBCUtils {
	private static  String driverClass;
	private static  String url;
	private static  String username;
	private static  String password;
	
	@JDBCInfo
	public static Connection getConnection() throws Exception{
		// 反射：
		Class clazz = JDBCUtils.class;
		
		Method method = clazz.getMethod("getConnection", null);
		
		// 获得方法上的注解：
		JDBCInfo jdbcInfo = method.getAnnotation(JDBCInfo.class);
		driverClass = jdbcInfo.driverClass();
		url = jdbcInfo.url();
		username = jdbcInfo.username();
		password = jdbcInfo.password();
		
		Class.forName(driverClass);
		Connection conn = DriverManager.getConnection(url, username, password);
		
		return conn;
	}
}
```

### web3.0注解开发

Tomcat7以上版本，并且 web3.0 以上，查看 web 版本

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">
</web-app>
```

* 使用 @WebServlet 替换 web.xml 中配置的 Servlet：

    
    @WebServlet(urlPatterns="/ServletDemo1",loadOnStartup=2,initParams=@WebInitParam(name="username",value="root"))

* 使用 @WebListener 替换 web.xml 中监听器的配置：

    
    @WebListener

* 使用 @WebFilter 替换 web.xml 中的过滤器的配置:

    
    @WebFilter(urlPatterns="/*")或@WebFilter("/*")

例子：

```java
package com.herolei.controller;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet("/hello")
public class MyHelloServlet extends HttpServlet {

    public void service(HttpServletRequest req, HttpServletResponse res) throws IOException {
        String name = req.getParameter("name");
        System.out.println(name);

        res.setCharacterEncoding("utf-8");
        PrintWriter out = res.getWriter();
        out.write("hello");
    }
}
```



