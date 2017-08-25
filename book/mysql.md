## mysql 的 curd

C: create 创建
U: update 修改
R: read 读
D: delete 删除

### Mysql中INT(1)与INT(10)的区别

    INT[(M)] [UNSIGNED] [ZEROFILL]
    
普通大小的整数。带符号的范围是 -2147483648 ~ 2147483647。无符号的范围是 0 ~ 4294967295。

INT(1) 和 INT(10)本身没有区别，但是加上(M)值后，会有显示宽度的设置。

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

```mysql
DROP TABLE IF EXISTS test1;
create table test1(id int(3) zerofill);

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
