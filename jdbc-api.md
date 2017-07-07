DriverManager: 管理一组 jdbc 的操作

将类加载到内存中方法：

* Class.forName("全限定名");//包名 + 类名  com.mysql.jdbc.Driver

* 类名.class

* 对象.getClass()


## getConnection()

## Connection 类

* (了解)Statement createStatement(): 获取普通的语句执行者，会出现sql注入

* ☆PreparedStatement prepareStatement(String sql): 获取预编译语句执行者

* (了解)CallableStatement prepareCall(String sql): 获取调用存储过程的语句执行者 

* (了解)setAuotCommit(false): 手动开户事务
* (了解)Commit(): 提交事务
* (了解)rollback(): 事务回滚

## Statement 语句执行者 接口 有注入
## PreparedStatement 预编译的语句执行者 接口

* setString(int 第几个？号, object)
* setInt(int 第几个？号, object)
* setObject(int 第几个？号, object)
* executeQuery(sql): 执行 r 语句，返回值：结果集
* executeUpdate(): 执行 cud 语句，返回值：影响的行数

## ResultSet: 结果集 接口

* next(): 判断是否有下一条记录，若有返回true且将光标移动到下一行，若没有则返回false。光标一开始处于第一条记录的上面。

* getString(int|string): 若参数为int: 第几列，若参数为 string: 列名(字段名) getString getInt getObject


## 常见配置文件格式

* .properties 内容格式为 key=value

* .xml

## 获取配置文件信息

```java
import java.util.ResourceBundle;

ResourceBundle bundle = ResourceBundle.getBundle("jdbc");
String driverClass = bundle.getString("driverClass");
```


