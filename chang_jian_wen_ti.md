# 常见问题

# 1. 无法删除数据库

问题: drop database_name的时候,出现`Drop database return “Error dropping database errno: 66” in MySQL`

解决: 

1. cd /usr/local/mysql/data/
2. sudo rm -rf database_name

参考: 

<http://stackoverflow.com/questions/16527054/drop-database-return-error-dropping-database-errno-66-in-mysql>
