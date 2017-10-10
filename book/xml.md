# xml

`xml` 可扩展的标签语言。用于存储数据、配置文件。

书写规范：

1. 区分大小写。

2. 应该有一个根标签。

3. 标签必须关闭。 `<xx></xx> <xx/>`

4. 属性必须用引号引起来。
    <xx value="tt"></xx>

5. 标签休中的空格、者换行、制表符等内容都是作为数据内容存在的。`<xx>aa</xx> <xx>  aa  </xx>`

6. 特殊字符必须转义。`< > &`


特殊字符 | 替换字符
---- | ----
`&` | `&amp;`
`<` | `&lt;`
`>` | `&gt;`
`"` | `&quot;`
`'` | `&apos;`


满足上面规范的文件是一个格式良好的 xml 文件，后缀名 `.xml` 。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<beans>
    <bean id="a" class="com.itheima.a">
        id为a
    </bean>
    <bean>
        没有任何属性
    </bean>
    <bean>
        我们在学习&lt;a&gt;标签
        <!--CDATA原义输出-->
        <![CDATA[
            <img>标签，
        ]]>
    </bean>
</beans>
```

xml 解析：

dom4j 解析开发包，hibernate 底层采用。

使用步骤：

1. 导入 jar 包。

2. 创建一个核心对象 SAXReader。

    `new SAXReader()`

3. 将 xml 文档加载到内存中形成一棵树。

    `Document doc = read.read(文件)`

4. 获取根节点。

    `Element root = doc.getRootElement()`

5. 通过根节点获取其它节点(文本节点，属性节点，元素节点)。

```
//获取所有的子元素
List<> list = root.elements();
//获取元素的指定属性内容
String value = root.attrbuteValue("属性名");
//获取子标签内容
String text = ele.elementText(子标签名称);
```

### 使用配制文件解耦合

创建 BeanFactory.java 文件

```java
import org.dom4j.Document;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;

public class BeanFactory {

    public static Object getBean(String id) {
        try {
            //1.获取document
            Document document = new SAXReader().read(BeanFactory.class.getClassLoader().getResourceAsStream("beans.xml"));
            //2.获取要初始化的元素
            Element ele = (Element) document.selectSingleNode("//bean[@id='" + id + "']");
            //3.获取类名
            String aClass = ele.attributeValue("class");
            //4.反射
            return Class.forName(aClass).newInstance();
        } catch (Exception e) {
            e.printStackTrace();
        }
        //没有获取该对象则返回null
        return null;
    }
}
```
测试：

第一步：新建配制文件 src/main/resources/beans.xml

```xml
<beans>
    <bean id="user" class="com.herolei.bean.UserBean"></bean>
</beans>
```

第二步：创建 com.herolei.bean.UserBean 类

```java
package com.herolei.bean;

public class UserBean {

    private String username;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }
}
```

第三步：新建 TestBeanFactory.java 测试类

```java
import com.herolei.bean.UserBean;
import org.junit.Test;

public class TestBeanFactory {

    @Test
    public void test1() {
        UserBean user = (UserBean) BeanFactory.getBean("user");
        user.setUsername("join");
        System.out.println(user.getUsername());
        // => join
    }
}
```

### 升级：Service层add方法实现增强

动态代理实现 Service 层 add() 方法增强，Proxy 必须有实现接口才能实现动态代理

```java
import org.dom4j.Document;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class BeanFactory {

    public static Object getBean(String id) {
        try {
            //1.获取document
            Document document = new SAXReader().read(BeanFactory.class.getClassLoader().getResourceAsStream("beans.xml"));
            //2.获取指定的bean对象
            Element ele = (Element) document.selectSingleNode("//bean[@id='" + id + "']");
            //3.获取类名
            String aClass = ele.attributeValue("class");
            //4.反射
            //return Class.forName(aClass).newInstance();

            //5.动态代理，对service层的add方法进行增强
            final Object obj = Class.forName(aClass).newInstance();
            if(id.endsWith("Service")) {
                return Proxy.newProxyInstance(obj.getClass().getClassLoader(), obj.getClass().getInterfaces(), new InvocationHandler() {
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        //增强add方法
                        if(method.getName().startsWith("add")) {
                            System.out.println("add 方法增强开始...");
                            Object invoke = method.invoke(obj, args);
                            System.out.println("add 方法增强结束...");
                            return invoke;
                        }
                        return method.invoke(obj, args);
                    }
                });
            } else {
                return obj;
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
        //没有获取该对象则返回null
        return null;
    }
}
```

