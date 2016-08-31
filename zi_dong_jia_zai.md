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
3. vendor/autoload.php引入真正的加载器

    ```php
    require_once __DIR__ . '/composer' . '/autoload_real.php';
    return ComposerAutoloaderInit9830e50881e975b6d46e598d3a511e66::getLoader();
    ```