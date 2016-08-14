# 设置慢查询

# 1. 用途

偶尔服务器502,我的程序到底哪里性能低了?如何跟踪,设置慢查询是个不错的选择

# 2. 设置

在php-fpm.conf中设置

```php
request_slowlog_timeout = 5s
slowlog = /usr/local/php/log/php-fpm.log.slow
```

重启php-fpm即可

# 3. 效果

```shell
6785 [12-Aug-2016 11:57:08]  [pool www] pid 6722
6786 script_filename = /data/nginx/hosts/pma/default/db_structure.php
6787 [0x00007f4277e22d88] mysqli_query() /data/nginx/hosts/pma/phpMyAdmin-4.5.2-all-languages/libraries/dbi/DBIMysqli.class.php:261
6788 [0x00007f4277e22b20] realQuery() /data/nginx/hosts/pma/phpMyAdmin-4.5.2-all-languages/libraries/DatabaseInterface.class.php:246
6789 [0x00007f4277e225c0] tryQuery() /data/nginx/hosts/pma/phpMyAdmin-4.5.2-all-languages/libraries/DatabaseInterface.class.php:1946
6790 [0x00007f4277e22158] fetchValue() /data/nginx/hosts/pma/phpMyAdmin-4.5.2-all-languages/libraries/Table.class.php:542
6791 [0x00007f4277e213d0] countRecords() /data/nginx/hosts/pma/phpMyAdmin-4.5.2-all-languages/libraries/controllers/DatabaseStructureController.class.php:1049
6792 [0x00007f4277e20cf0] getValuesForInnodbTable() /data/nginx/hosts/pma/phpMyAdmin-4.5.2-all-languages/libraries/controllers/DatabaseStructureController.class.php:935
6793 [0x00007f4277e20038] getStuffForEngineTypeTable() /data/nginx/hosts/pma/phpMyAdmin-4.5.2-all-languages/libraries/controllers/DatabaseStructureController.class.php:262
6794 [0x00007f4277e1ace0] indexAction() /data/nginx/hosts/pma/phpMyAdmin-4.5.2-all-languages/db_structure.php:54
```