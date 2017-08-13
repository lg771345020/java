## cookie

### Cookie 使用方法

    //1、构造Cookie
    Cookie(String name,String value);
    
    //2、向浏览器回写Cookie：
    response.addCookie(Cookie cookie);
    
    //设置cookie
    cookie.setMaxAge(0);
    cookie.setPath(req.getContextPath() + "/");
    
    //3、获得Cookie:
    Cookie[] request.getCookies();
    
## Session

    
### session 生命周期

* 创建

  
    //1、获取session
    HttpSession session = req.getSession();

* 销毁 

    
    //1、服务器非正常关闭
    
    //2、session超时，默认超时时间：30分钟 
    ①web.xml中配置
    <session-config>
        <session-timeout>30</session-timeout>
    </session-config> 
    ②手动设置超时，单位s
    session.setMaxInactiveInterval(int timeout)  
    
    //3、手动销毁session
    session.invalidate();

