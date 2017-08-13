## Jdbc 基本操作

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import org.junit.Test;

public class TestJdbc {

    @Test
    public void run1() throws Exception{
        //1、注册驱动
        Class.forName("com.mysql.jdbc.Driver");
        //DriverManager.registerDriver(new Driver());

        //2、获取连接
        //DriverManager 管理一组 jdbc 的操作
        Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/test", "root", "root");

        //3、编写sql
        String  sql="select * from users where name=?";

        //4、创建语句执行者
        PreparedStatement st = conn.prepareStatement(sql);

        //5、设置参数
        st.setString(1, "aaaa");

        //6、执行sql
        ResultSet rs = st.executeQuery();

        //7、处理结果
        while(rs.next()){
            System.out.println(rs.getString("id") + "=>" + rs.getString("name"));
        }

        //8、释放资源.
        rs.close();
        st.close();
        conn.close();
    }
}
```

**补充：将类加载到内存中方法：**

* Class.forName("全限定名");//包名 + 类名  com.mysql.jdbc.Driver

* 类名.class

* 对象.getClass()


### DriverManager.getConnection()

管理一组 jdbc 的操作

### Connection 类

* (了解)Statement createStatement(): 获取普通的语句执行者，会出现sql注入

* ☆PreparedStatement prepareStatement(String sql): 获取预编译语句执行者

* (了解)CallableStatement prepareCall(String sql): 获取调用存储过程的语句执行者 

* (了解)setAuotCommit(false): 手动开户事务
* (了解)Commit(): 提交事务
* (了解)rollback(): 事务回滚

### PreparedStatement 预编译的语句执行者 接口

父类 `Statement` 语句执行者 接口 有注入

* setString(int 第几个？号, object)
* setInt(int 第几个？号, object)
* setObject(int 第几个？号, object)
* executeQuery(sql): 执行 r 语句，返回值：结果集
* executeUpdate(): 执行 cud 语句，返回值：影响的行数

### ResultSet: 结果集 接口

* next(): 判断是否有下一条记录，若有返回true且将光标移动到下一行，若没有则返回false。光标一开始处于第一条记录的上面。

* getString(int|string): 若参数为int: 第几列，若参数为 string: 列名(字段名) getString getInt getObject


### 实例，对 jdbc 进行简单的封闭

第一步：创建 jdbc.properties 文件

```
driverClass=com.mysql.jdbc.Driver
url=jdbc\:mysql\://localhost\:3306/test
user=root
password=root
```

第二步：创建 jdbcUtils.java 文件

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ResourceBundle;

public class jdbcUtils {

	static final String DRIVERCLASS, URL, USER, PASSWORD;

	//静态代码块，加载代码时运行，只执行一次人
	static {
		ResourceBundle bundle = ResourceBundle.getBundle("jdbc");
		DRIVERCLASS = bundle.getString("driverClass");
		URL = bundle.getString("url");
		USER = bundle.getString("user");
		PASSWORD = bundle.getString("password");
		System.out.println(DRIVERCLASS);
	}

	static {
		//连接驱动
		try {
			Class.forName(DRIVERCLASS);
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	public static Connection getConnection() throws SQLException {
		//建立连接
		return DriverManager.getConnection(URL, USER, PASSWORD);
	}

	public static void closeResource(Connection conn, Statement st, ResultSet rs) {
		closeResultSet(rs);
		closeStatement(st);
		closeConn(conn);
	}

	/**
	 * 释放连接
	 * @param conn
	 */
	public static void closeConn(Connection conn) {
		if(conn != null) {
			try {
				conn.close();
			} catch (SQLException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			conn = null;
		}
	}

	/**
	 * 释放语句执行者
	 * @param st
	 */
	public static void closeStatement(Statement st) {
		if(st != null) {
			try {
				st.close();
			} catch (SQLException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			st = null;
		}
	}

	/**
	 * 释放结果集
	 * @param rs
	 */
	public static void closeResultSet(ResultSet rs) {
		if(rs != null) {
			try {
				rs.close();
			} catch (SQLException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			rs = null;
		}
	}
}
```

第三步：测试

```java
import org.junit.Test;
public class jdbcUtils {
    
    @Test
    public void run2() {
        Connection conn = null;
        PreparedStatement st = null;
        ResultSet rs = null;

        try {
            conn = jdbcUtils.getConnection();

            //创建 sql
            String sql = "insert into users (name) values (?)";

            //获取语句执行者
            st = conn.prepareStatement(sql);

            //设置参数
            st.setString(1, "sss");

            //执行 sql
            int i = st.executeUpdate();

            //处理结果
            if(i == 1) {
                System.out.println("success");
            } else {
                System.out.println("fail");
            }

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //关闭连接资源
            jdbcUtils.closeResource(conn, st, rs);
        }
    }
}
```

**补充：properties 配置文件读取：**

常见配置文件格式有 `properties`和`xml`两种，`properties` 文件读取方式如下：

```java
import java.util.ResourceBundle;

public class TestProperties {
    @Test
    public void read() throws Exception{
        //①获取 ResourceBundle 对象
        ResourceBundle bundle = ResourceBundle.getBundle("jdbc");
        
        //①通过 ResourceBundle 对象获取配置信息
        String driverClass = bundle.getString("driverClass");
    }
}
```

## 连接池

使用 jdbc 时，每次操作都需要获取连接(创建)，用完之后把连接释放(销毁)，通过连接池来优化

**实现一个自定义的最简单连接池**

```java
import java.sql.Connection;
import java.sql.SQLException;
import java.util.LinkedList;

/**
 * 最简单的连接池
 */
public class MyDataSource {
    //LinkedList 增删快，查询慢
    static LinkedList<Connection> pool = new LinkedList<>();

    //初始化连接池，放入3个连接
    static {
        for(int i = 0; i< 3; i++) {
            try {
                Connection conn = jdbcUtils.getConnection();
                pool.addLast(conn);
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

    //获取连接
    public static Connection getConnection() {
        if(pool.isEmpty()) {
            for(int i = 0; i< 3; i++) {
                try {
                    Connection conn = jdbcUtils.getConnection();
                    pool.addLast(conn);
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
        System.out.println("获取连接");
        return pool.removeFirst();
    }

    //归还连接
    public static void addBack(Connection conn) {
        System.out.println("已经归还连接");
        pool.addLast(conn);
    }
}
```

### 增强方法

* 继承/实现

* 装饰者模式(静态代理)

    * 装饰者和被装饰者实现同一个接口或者继承同一个类

    * 装饰者中要有被装饰者的引用

    * 对需要的增强的方法进行加强

    * 对不需要的方法调用原来的方法

* 动态代理

### 增强连接池，实现 conn.close() 归还连接池：

第一步：修改 MyDataSource.java 中 getConnection() 方法的返回值

```
System.out.println("获取连接");
return new ConnectionWrap(pool.removeFirst(), pool);
```

第二步：新建 ConnectionWrap.java 对 Connection 的 close() 方法进行增强

```java
import java.sql.*;
import java.util.LinkedList;

public class ConnectionWrap implements Connection {
    //连接池
    private LinkedList pool;
    private Connection conn;

    public ConnectionWrap(Connection conn) {
        this.conn = conn;
    }

    public ConnectionWrap(Connection conn, LinkedList pool) {
        this.conn = conn;
        this.pool = pool;
    }

    @Override
    //修改close方法为归还连接池
    public void close() throws SQLException {
        this.pool.addLast(this.conn);
        System.out.println("连接已经归还！");
    }

    @Override
    //不用增强的方法调用原来的方法
    public Statement createStatement() throws SQLException {
        return conn.createStatement();
    }

    @Override
    //不用增强的方法调用原来的方法
    public PreparedStatement prepareStatement(String sql) throws SQLException {
        return conn.prepareStatement(sql);
    }
    
    //...
}
```

第三步：测试

```java
import org.junit.Test;

public class TestJdbc {

    @Test
    public void run3() throws Exception{

        MyDataSource dataSource = new MyDataSource();

        ConnectionWrap conn = (ConnectionWrap) dataSource.getConnection();
        conn.close();
        //获取连接
        //连接已经归还！
    }
}
```

### dbcp连接池

第一步：导入 jar 包

```
commons-dbcp-1.4.jar
commons-pool-1.5.6.jar
```

第二步：新建 src/main/resources/jdcp.properties 配置文件 

```
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/test
username=root
password=root
```

第三步：dbcp-api

```java
import org.apache.commons.dbcp.BasicDataSource;
import org.apache.commons.dbcp.BasicDataSourceFactory;
import org.junit.Test;
import java.io.FileInputStream;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.util.Properties;

public class TestJdbc {
    @Test
    public void dbcp() throws Exception{
        //方法一：
        /*BasicDataSource  dataSource = new BasicDataSource();
        dataSource.setDriverClassName("com.mysql.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/test");
        dataSource.setUsername("root");
        dataSource.setPassword("root");*/

        //方法二：
        Properties prop = new Properties();
        /*prop.setProperty("driverClassName", "com.mysql.jdbc.Driver");
        prop.setProperty("url", "jdbc:mysql://localhost:3306/test");
        prop.setProperty("username", "root");
        prop.setProperty("password", "root");*/

        prop.load(new FileInputStream("src/main/resources/jdcp.properties"));
        BasicDataSource dataSource = (BasicDataSource) new BasicDataSourceFactory().createDataSource(prop);

        //1、获得连接:
        Connection conn = dataSource.getConnection();
        //2、编写SQL语句.
        String sql = "insert into users values (null, ?, null)";
        //3、预编译SQL:
        PreparedStatement st = conn.prepareStatement(sql);
        //4、设置参数:
        st.setString(1, "Tom");
        //5、执行SQL
        st.execute();
        //6、关闭连接
        st.close();
        conn.close();
    }
}
```

### c3p0连接池(能自动回收空闲连接的功能)

第一步：导入 jar 包 `c3p0-0.9.1.2.jar`

第二步：c3p0 默认到 src/main/resources/c3p0.properties 读取配置文件

```
c3p0.driverClass=com.mysql.jdbc.Driver
c3p0.jdbcUrl=jdbc:mysql://localhost:3306/test
c3p0.user=root
c3p0.password=root
```

或 c3p0 默认到 src/main/resources/c3p0-config.xml 读取配置文件

```xml
<c3p0-config>
	<!-- 默认配置，如果没有指定则使用这个配置 -->
	<default-config>
		<!-- 基本配置 -->
		<property name="driverClass">com.mysql.jdbc.Driver</property>
		<property name="jdbcUrl">jdbc:mysql://localhost:3306/test</property>
		<property name="user">root</property>
		<property name="password">root</property>
	
		<!--扩展配置-->
		<property name="checkoutTimeout">30000</property>
		<property name="idleConnectionTestPeriod">30</property>
		<property name="initialPoolSize">10</property>
		<property name="maxIdleTime">30</property>
		<property name="maxPoolSize">100</property>
		<property name="minPoolSize">10</property>
		<property name="maxStatements">200</property>
	</default-config> 
	
	<!-- 命名的配置 -->
	<named-config name="name">
		<property name="driverClass">com.mysql.jdbc.Driver</property>
		<property name="jdbcUrl">jdbc:mysql://127.0.0.1:3306/xxxx</property>
		<property name="user">root</property>
		<property name="password">1234</property>
		
		<!-- 如果池中数据连接不够时一次增长多少个 -->
		<property name="acquireIncrement">5</property>
		<property name="initialPoolSize">20</property>
		<property name="minPoolSize">10</property>
		<property name="maxPoolSize">40</property>
		<property name="maxStatements">20</property>
		<property name="maxStatementsPerConnection">5</property>
	</named-config>
</c3p0-config> 
```

第三步：c3p0-api

```java
import org.junit.Test;
import com.mchange.v2.c3p0.ComboPooledDataSource;
import java.sql.Connection;
import java.sql.PreparedStatement;

public class TestJdbc {
    @Test
    public void c3p0() throws Exception {
        //默认到 src/main/resources/c3p0.properties 读取配置文件
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        //使用命名的配置，若找不到就使用默认的配置
        //ComboPooledDataSource dataSource = new ComboPooledDataSource("name");
        /*dataSource.setDriverClass("com.mysql.jdbc.Driver");
        dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test");
        dataSource.setUser("root");
        dataSource.setPassword("root");*/

        //1、获得连接:
        Connection conn = dataSource.getConnection();
        //2、编写SQL语句.
        String sql = "insert into users values (null, ?, null)";
        //3、预编译SQL:
        PreparedStatement st = conn.prepareStatement(sql);
        //4、设置参数:
        st.setString(1, "Tom2");
        //5、执行SQL
        st.execute();
        //6、关闭连接
        st.close();
        conn.close();
    }
}
```

## dbutils

DBUtils 封装了对 JDBC 的操作，简化了 JDBC 操作。

Dbutils 三个核心功能介绍：

* [`QueryRunner`](#queryrunner) 提供对 sql 语句操作的 API 

* [`ResultSetHandler`](#resultsethandler) 接口，用于定义 select 操作后，怎样封装结果集

* [`DbUtils`](#dbutils) 工具类，定义了关闭资源与事务处理的方法

### QueryRunner

* `QueryRunner(DataSource ds)` 提供数据源（连接池），DBUtils底层自动维护连接connection

* `update(String sql, Object... params)` 执行更新数据

* `query(String sql, ResultSetHandler<T> rsh, Object... params)` 执行查询

### ResultSetHandler

结果集 | 说明
---- | ----
ArrayHandler | 将结果集中的第一条记录封装到一个Object[]数组中，数组中的每一个元素就是这条记录中的每一个字段的值
ArrayListHandler | 将结果集中的每一条记录都封装到一个Object[]数组中，将这些数组在封装到List集合中。
**BeanHandler** | 将结果集中第一条记录封装到一个指定的javaBean中。
**BeanListHandler** | 将结果集中每一条记录封装到指定的javaBean中，将这些javaBean在封装到List集合中
ColumnListHandler | 将结果集中指定的列的字段值，封装到一个List集合中
KeyedHandler | 将结果集中每一条记录封装到Map<String,Object>,在将这个map集合做为另一个Map的value,另一个Map集合的key是指定的字段的值。
MapHandler | 将结果集中第一条记录封装到了Map<String,Object>集合中，key就是字段名称，value就是字段值
**MapListHandler** | 将结果集中每一条记录封装到了Map<String,Object>集合中，key就是字段名称，value就是字段值，在将这些Map封装到List集合中。
**ScalarHandler** | 它是用于单数据。例如select count(*) from 表操作。

### DbUtils

* `closeQuietly(Connection conn)` 关闭连接，如果有异常 try 后不抛

* `commitAndCloseQuietly(Connection conn)` 提交并关闭连接

* `rollbackAndCloseQuietly(Connection conn)` 回滚并关闭连接

