## Listener

监听器就是一个 Java 类用来监听其他的 JavaBean 的变化

监听器的术语:

* 事件源		：被监听的对象.汽车
* 监听器对象	：监听的对象.汽车上报警器
* 事件源与监听器绑定：
* 事件		：指的是事件源的改变.
* 获得事件源对象.

### Servlet的监听器

监听 `ServletContext`, `HttpSession`, `ServletRequest` 。事件源和监听器绑定通过配置完成。

Servlet 中的监听器:提供了8个监听器.

一类(3)：监听三个域对象的创建和销毁的监听器

* `ServletContextListener` 监听 ServletContext 对象的创建和销毁
    * 创建：服务器启动时候，服务器可以为每个 WEB 应用创建一个单独的 ServletContext
    * 销毁：服务器关闭的时候，或者项目从服务器中移除
    * 作用：用来加载配置文件

* `HttpSessionListener` 监听 HttpSession 对象的创建和销毁
* `ServletRequestListener` 监听 ServletRequest 对象的创建和销毁

二类(3)：监听三个域对象的属性变更的监听器.(属性添加,属性移除,属性替换)

三类(2)：监听HttpSession对象中的JavaBean的状态的改变.(绑定,解除绑定,钝化和活化)

