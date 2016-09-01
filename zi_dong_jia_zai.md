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

# 加载加载器过程

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

这里主要理解一下`spl_autoload_register`方法

`spl_autoload_register`简单来说就是当准备触发__autoload()方法的时候,会被`spl_autoload_register`给拦截住,直接执行`spl_autoload_register`方法

```php
bool spl_autoload_register ([ callable $autoload_function [, bool $throw = true [, bool $prepend = false ]]] )
```

最后一个参数为true时,spl_autoload_register会添加到队列之首,而不是尾部

例如这里并没有引用`\Composer\Autoload\ClassLoader`

而在new \Composer\Autoload\ClassLoader之前先spl_autoload_register给自己的loadClassLoader方法

这里列举下各种加载文件的内容,返回的都是一个数组

```php
$vendorDir = dirname(dirname(__FILE__));
$baseDir = dirname($vendorDir);

///autoload_namespaces.php
return [
  '0e6d7bf4a5811bfa5cf40c5ccd6fae6a' => $vendorDir . '/symfony/polyfill-mbstring/bootstrap.php',
  ...
];

//autoload_psr4.php
return [
  'XdgBaseDir\\' => array($vendorDir . '/dnoegel/php-xdg-base-dir/src'),
  ...
];

//autoload_classmap.php
return [
  'CreateActivity' => $baseDir . '/database/migrations/2016_03_29_676568_Create_Activity.php',
  ...
];
//autoload_files.php
return [
  '0e6d7bf4a5811bfa5cf40c5ccd6fae6a' => $vendorDir . '/symfony/polyfill-mbstring/bootstrap.php',
  ...
];
```

这里配置的路径用于ClassLoader的查找,

把所有预加载的php文件都放在spl_autoload_register函数中

核心的代码有

在autoload_real.php中加载完

1. autoload_namespaces.php
2. autoload_psr4.php
3. autoload_classmap.php

后就会执行ClassLoader的register方法: `$loader->register(true);`


```php
    //
    public function register($prepend = false)
    {
        spl_autoload_register(array($this, 'loadClass'), true, $prepend);
    }
    
     public function loadClass($class)
    {
        if ($file = $this->findFile($class)) {
            includeFile($file);

            return true;
        }
    }
    
    function includeFile($file)
    {
        include $file;
    }

```

就是把需要用psr0 psr4 classmap的时候,就会执行这个loadClass的方法加载

而最后的`autoload_files.php`文件为一般的全局文件

会放在`$GLOBALS`中

```php
function composerRequire9830e50881e975b6d46e598d3a511e66($fileIdentifier, $file)
{
    if (empty($GLOBALS['__composer_autoload_files'][$fileIdentifier])) {
        require $file;

        $GLOBALS['__composer_autoload_files'][$fileIdentifier] = true;
    }
}

```
