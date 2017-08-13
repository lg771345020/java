## Jdbc 基本操作

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import org.junit.Test;

public class TestJdbc {

    @Test
    public void f2() throws Exception{
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


### 实例，建立 mysql 数据库的连接

创建 jdbc.properties 文件

```
driverClass=com.mysql.jdbc.Driver
url=jdbc\:mysql\://localhost\:3306/test
user=root
password=root
```

创建 jdbc.java 文件

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ResourceBundle;

import org.junit.Test;


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

	@Test
	public void f() {
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

**补充：properties配置文件读取：**

常见配置文件格式有 `properties`和`xml`两种

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

## 增强方法

* 继承/实现

* 装饰者模式(静态代理)

    * 装饰者和被装饰者实现同一个接口或者继承同一个类

    * 装饰者中要有被装饰者的引用

    * 对需要的增强的方法进行加强

    * 对不需要的方法调用原来的方法

* 动态代理

