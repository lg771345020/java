##

### spring操作数据库

修改配制文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context.xsd
	http://www.springframework.org/schema/aop
	http://www.springframework.org/schema/aop/spring-aop.xsd
	http://www.springframework.org/schema/tx
	http://www.springframework.org/schema/tx/spring-tx.xsd">
    
    <!--1、Spring管理内置的连接池，导入如下2个包
        * Spring-jdbc.jar
        * Spring-tx.jar
    -->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver" />
        <property name="url" value="jdbc:mysql://localhost:3306/test" />
        <property name="username" value="root" />
        <property name="password" value="root" />
    </bean>

    <!--2、dbcp连接池，导入如下2个包
        * com.springsource.org.apache.commons.dbcp-1.2.2.osgi.jar
        * com.springsource.org.apache.commons.pool-1.5.3.jar
    -->
    <!--<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver" />
        <property name="url" value="jdbc:mysql://localhost:3306/test" />
        <property name="username" value="root" />
        <property name="password" value="root" />
    </bean>-->

    <!--3、C3P0连接池，导入如下包
        com.springsource.com.mchange.v2.c3p0-0.9.1.2.jar
    -->
    <!--<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/test"/>
        <property name="user" value="root"/>
        <property name="password" value="root"/>
    </bean>-->

    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource" />
    </bean>

</beans>
```

```java
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.datasource.DriverManagerDataSource;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import javax.annotation.Resource;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:demo5_jdbc/applicationContext.xml")
public class jdbcTest {

    @Resource(name="jdbcTemplate")
    private JdbcTemplate template;

    //1、将数据池交给spring管理
    @Test
    public void run1() {
        template.update("INSERT INTO users(name) VALUES (?)", "join2");
    }

    //2、自己new DriverManagerDataSource()
    @Test
    public void run2() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName("com.mysql.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/test");
        dataSource.setUsername("root");
        dataSource.setPassword("root");

        //设置模板类
        JdbcTemplate template = new JdbcTemplate();
        //设置连接池
        template.setDataSource(dataSource);
        //完成操作
        template.update("INSERT INTO users(name) VALUES (?)", "join");
    }
}
```

