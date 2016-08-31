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
    
    `ComposerAutoloaderInit9830e50881e975b6d46e598d3a511e66`是上面一行引入的autoload_real.php中正在的类名,用于调用静态方法`getLoader`
    
4. 通过上一部的getLoader,整个程序会得到一个加载器`Composer\Autoload\ClassLoader`,ClassLoader主要用来加载PSR0、PSR4和classmap

# 加载过程

核心的加载在ClassLoader,autoload_real.php如何通过getLoader获得加载器

先讲一下autoload_real.php是如何给ClassLoader内容的

```php
class ComposerAutoloaderInit9830e50881e975b6d46e598d3a511e66
{
    private static $loader;

    public static function loadClassLoader($class)
    {
        if ('Composer\Autoload\ClassLoader' === $class) {
            require __DIR__ . '/ClassLoader.php';
        }
    }

    public static function getLoader()
    {
        if (null !== self::$loader) {
            return self::$loader;
        }
        //奇怪.为啥要这么引用.
        spl_autoload_register(array('ComposerAutoloaderInit9830e50881e975b6d46e598d3a511e66', 'loadClassLoader'), true, true);
        self::$loader = $loader = new \Composer\Autoload\ClassLoader();
        spl_autoload_unregister(array('ComposerAutoloaderInit9830e50881e975b6d46e598d3a511e66', 'loadClassLoader'));

        $map = require __DIR__ . '/autoload_namespaces.php';
        foreach ($map as $namespace => $path) {
            $loader->set($namespace, $path);
        }

        $map = require __DIR__ . '/autoload_psr4.php';
        foreach ($map as $namespace => $path) {
            $loader->setPsr4($namespace, $path);
        }

        $classMap = require __DIR__ . '/autoload_classmap.php';
        if ($classMap) {
            $loader->addClassMap($classMap);
        }

        $loader->register(true);

        $includeFiles = require __DIR__ . '/autoload_files.php';
        foreach ($includeFiles as $fileIdentifier => $file) {
            composerRequire9830e50881e975b6d46e598d3a511e66($fileIdentifier, $file);
        }

        return $loader;
    }
}

function composerRequire9830e50881e975b6d46e598d3a511e66($fileIdentifier, $file)
{
    if (empty($GLOBALS['__composer_autoload_files'][$fileIdentifier])) {
        require $file;

        $GLOBALS['__composer_autoload_files'][$fileIdentifier] = true;
    }
}

```