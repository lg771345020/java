# servlet

sun 公司制订的一种用来扩展 web 服务器功能的组件规范。

1. 实现 Servlet 接口或者继承 HttpServlet 抽象类。

2. 编译

3. 打包，即创建一个具有如下结构的文件夹：

```
    appname(应用名)
      ├──WEB-INF
          ├──classes    .class文件
          ├──lib        .jar文件，lib文件夹可选
          └──web.xml    部署描述文件
```

4. 将 step3 创建的文件夹拷贝到 servlet 窗口特定的文件夹下。也可以将 step3 创建的文件夹使用 jar 命令埋压缩，生成 .war 为后缀的文件。
 
5. 启动 servlet 容器，访问 servlet。http://ip:port/appname/url-pattern
 