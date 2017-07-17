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

