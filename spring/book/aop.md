## AOP

### AOP的概述

AOP 是 `Aspect Oriented Programming` 的缩写，即，面向切面编程

1. 什么是 AOP 的技术？
    * AOP 是一种编程范式，隶属于软工范畴，指导开发者如何组织程序结构
    * AOP 最早由 AOP 联盟的组织提出的,制定了一套规范. Spring 将 AOP 思想引入到框架中,必须遵守 AOP 联盟的规范
    * 通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术
    * AOP 是 OOP 的延续，是软件开发中的一个热点，也是 Spring 框架中的一个重要内容，是函数式编程的一种衍生范型
    * 利用 AOP 可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率

2. AOP 面向切面编程.(思想---解决 OOP 遇到一些问题)

3. AOP 采取横向抽取机制，取代了传统纵向继承体系重复性代码（性能监视、事务管理、安全检查、缓存）

4. 为什么要学习 AOP
    * 可以在不修改源代码的前提下，对程序进行增强！！  
    
### AOP注解使用方法

* `<aop:aspectj-autoproxy  />` 自动扫描切面注解

* `@Aspect` 切面

* `@Before` 通知类型

* `@Pointcut` 自定义切入点

例子：

```java

```

第一步：导入 jar 包

* Spring 框架基本开发包(4个核心包+2个日志包)

* Spring 框架的 AOP 的开发包

    * spring 的传统 AOP 的开发的包
        * spring-aop-4.2.4.RELEASE.jar
        * com.springsource.org.aopalliance-1.0.0.jar
    
    * aspectJ 的开发包
        * com.springsource.org.aspectj.weaver-1.6.8.RELEASE.jar
        * spring-aspects-4.2.4.RELEASE.jar

第二步：新建 demo4_aop/applicationContext.xml 配置文件，添加以下内容

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

    <!--自动扫描注解进行注入-->
    <context:component-scan base-package="demo4_aop" />
    <!--自动扫描切面注解-->
    <aop:aspectj-autoproxy  />
</beans>
```

第三步：新建 demo4_aop.AspactAnno.java 文件，添加以下内容

```java
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

//1、@Aspect 切面
@Aspect
@Component(value = "aspactAnno")
public class AspactAnno {

    //2、@Before 通知类型
    @Before(value="execution(* demo4_aop.UserDaoImpl.save())")
    public void f() {
        System.out.println("do something before save");
    }

    //3、使用自定义切入点 AspactAnno.save()
    @After(value="AspactAnno.save()")
    public void saveAfter() {
        System.out.println("do something after save");
    }

    //4、@Pointcut 自定义切入点
    @Pointcut(value="execution(* demo4_aop.UserDaoImpl.save())")
    public void save() {}

    //5、@Around 环绕
    @Around(value="execution(* demo4_aop.UserDaoImpl.update())")
    public void updateAround(ProceedingJoinPoint joinPoint) {
        System.out.println("do something around update1");
        //手动调用update方法，否则不会执行update
        try {
            joinPoint.proceed();
        } catch (Throwable throwable) {
            throwable.printStackTrace();
        }
        System.out.println("do something around update2");
    }
}
```

第四步：新建 emo4_aop.UserDao 接口，及实现接口类 demo4_aop.UserDaoImpl

```java
public interface UserDao {

    public void save();

    public void update();
}
```

```java
import org.springframework.stereotype.Component;

@Component(value="userDao")
public class UserDaoImpl implements UserDao {

    @Override
    public void save() {
        System.out.println("save");
    }

    @Override
    public void update() {
        System.out.println("update");
    }
}
```

第五步：新建 demo4_aop.TestCase 测试类

```java
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import javax.annotation.Resource;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:demo4_aop/applicationContext.xml")
public class TestCase {

    @Resource(name="userDao")
    private UserDao dao;

    @Test
    public void test1() {

        dao.save();
        dao.update();
    }
}
```

输出结果

```
do something before save
save
do something after save
do something around update1
update
do something around update2
```