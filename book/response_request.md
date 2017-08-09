## Response 对象

### Response 设置状态码：

```java
//1、设置状态码
response.setState(200)
//2、设置错误状态码，针对4xx和5xx
response.setError(404)
```
    
### Response 设置响应头：

方法 | 说明 
--- | ---
setHeader(String key, String value) | 设置响应头
setIntHeader(String key, int value) | 设置整数响应头
setDateHeader(String key, long value) | 设置时间响应头
addHeader(String key, String value) | 设置响应头，没有设置则设置，有则追加
addIntHeader(String key, int value) | 设置整数响应头
addDateHeader(String key, long value) | 设置时间响应头


```java

```