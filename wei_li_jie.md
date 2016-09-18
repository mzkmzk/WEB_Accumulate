# 未理解

# 1.No supported encrypter found. The cipher and / or key length are invalid.

因为我自己研发的插件k_laravel_creator,里有默认自带route,一报No supported encrypter found. The cipher and / or key length are invalid.的错

然后再App/Http/route里面设置相同的代码,则可以执行通过

然后上网搜了一下,重新生成密钥就可以了

```shell
php artisan key:generate
Application key [1HPet63EUJF2wGeOFrKpYvevlp5ukZoP] set successfully.
```

比较奇怪是为甚在App\Http\route里面设置路径就没有这样的问题

但是在verndor里面设置的route就会有问题

