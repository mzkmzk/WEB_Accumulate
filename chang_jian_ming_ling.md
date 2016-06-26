# 常见命令

# 1. 新建数据库

`CREATE DATABASE 数据库名;` 

# 2. 每一个单一数据库创建单一的读写权限用户

    mysql -u ... -h ... -p
    //进去后,可以查看授权的配置
    ? grant

    //例1：增加一个test1用户，密码为123456，可以在任何主机上登录，并对所有数据库有查询，增加，修改和删除的功能。需要在mysql的root用户下进行
    mysql>grant select,insert,update,delete on *.* to test1@”%” identified by “123456″；
    mysql>flush privileges;

    use mysql;
    select distinct(User) from user;
    //显示该数据库授权登陆的用户名(其实还有HOST的限制)

# 3. 查看数据库里的时间和现在的时间差

```sql
select * from `表名` wher  TIMESTAMPDIFF(DAY,now(),存储的时间属性) <= 天数
```
可以得出属性中的时间小于天数的所有行.