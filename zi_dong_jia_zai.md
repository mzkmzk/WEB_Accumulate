# 自动加载

# 整体过程

1. 在public/index.php中引入自动加载文件autoload.php

  ```php
  require __DIR__.'/../bootstrap/autoload.php';
  ```
2. 在bootstrap/autoload.php引入vendor/autoload.php

   ```php
   require __DIR__.'/../vendor/autoload.php';
   ```
   