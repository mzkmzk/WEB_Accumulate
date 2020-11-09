# 常见命令

# 远程连接数据库

```shell
# mydb = 数据库名
mysql --host=localhost --user=myname --password=password mydb
```

参考链接: https://dev.mysql.com/doc/refman/8.0/en/connecting.html

#  新建数据库

`CREATE DATABASE 数据库名;` 

# 查看所有数据库

```bash
show databases;
```

# 选择数据库

```bash
use 数据库名
```

# 查看当前选择的数据库

```bash
select database();
```

# 查看当前数据库的所有表

```bash
show tables;
```

# 查看数据库表的结构

```bash
DESCRIBE 表名
```

# 查看数据库表的创建语句

```bash
SHOW CREATE TABLE 数据库表名
```

#  每一个单一数据库创建单一的读写权限用户

    mysql -u ... -h ... -p
    //进去后,可以查看授权的配置
    ? grant

    //例1：增加一个test1用户，密码为123456，可以在任何主机上登录，并对所有数据库有查询，增加，修改和删除的功能。需要在mysql的root用户下进行
    mysql>grant select,insert,update,delete on *.* to test1@”%” identified by “123456″；
    mysql>flush privileges;

    use mysql;
    select distinct(User) from user;
    //显示该数据库授权登陆的用户名(其实还有HOST的限制)

#  查看数据库里的时间和现在的时间差

```sql
select * from `表名` wher  TIMESTAMPDIFF(DAY,now(),存储的时间属性) <= 天数
```
可以得出属性中的时间小于天数的所有行.