## mysql 的 curd

C: create 创建
U: update 修改
R: read 读
D: delete 删除

### mysql bigint-int-smallint-tinyint

* `bigint` 从 -2^63 (-9223372036854775808) 到 2^63-1 (9223372036854775807) 的整型数据（所有数字）。存储大小为 8 个字节。

* `int` 从 -2^31 (-2,147,483,648) 到 2^31 – 1 (2,147,483,647) 的整型数据（所有数字）。存储大小为 4 个字节。int 的 SQL-92 同义字为 integer。 

* `smallint` 从 -2^15 (-32,768) 到 2^15 – 1 (32,767) 的整型数据。存储大小为 2 个字节。 

* `tinyint` 从 0 到 255 的整型数据。存储大小为 1 字节。 

### mysql 中INT与INT(10)的区别

    INT[(M)] [UNSIGNED] [ZEROFILL]
    
普通大小的整数。带符号的范围是 -2147483648 ~ 2147483647。无符号的范围是 0 ~ 4294967295。

在 int(M) 中，M 的值跟 int(M) 所占多少存储空间并无任何关系。和数字位数也无关系 int(3)、int(4)、int(8) 在磁盘上都是占用 4 btyes 的存储空间。

示例如代码如下：

第一步：`test` 表不设置 INT[(M)]

```mysql
DROP TABLE IF EXISTS `test`;
CREATE TABLE `test` (
  `id` int(3) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

insert into test values(12);
insert into test values(123);
insert into test values(-123);

select * from test;
```
`test` 表查询结果正常显示：

```
+-------+
| id    |
+-------+
|    12 |
|   123 |
| -1234 |
+-------+
```

第二步：`test1` 表设置 INT[(M)]

```mysql
DROP TABLE IF EXISTS test1;
create table test1(
  id int(3) zerofill DEFAULT NULL
)  ENGINE=InnoDB DEFAULT CHARSET=utf8;

insert into test1 value(12);
insert into test1 value(1234);
insert into test1 value(-1234);

select * from test1;
```

`test1` 表查询结果为：

```
+------+
| id   |
+------+
|  012 |
| 1234 |
|  000 |
+------+
```

**注意：**

* 012 前多个 0，int(M) 的值多了个0，这就是显示宽度的限制

* zerofill 的时候系统会给自动添加上 unsigned 属性，就是非负数。而设置的显示宽度为3位，所以就会输出 000 

### MySQL--删除表数据drop、truncate和delete的用法

* `drop table 表名` 删除整个表

        drop table  dbo.Sys_Test
        
* `truncate table 表名` 清空表中的数据，删除内容、释放空间但不删除定义(保留表的数据结构)。与drop不同的是,只是清空表数据而已。

        truncate  table dbo.Sys_Test    
                      
* `delete from 表名 where 列名 = 值` 删除表中的行。delete语句执行删除的过程是每次从表中删除一行，并且同时将该行的删除操作作为事务记录在日志中保存
以便进行进行回滚操作。

        delete from dbo.Sys_Test where test='test'
