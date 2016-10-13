# 入门DEMO

# 目的

在index.html页面input输入任何东西,在website.html页面会有一个对应的input自动更着修改

# 准备Serve

文档第一眼看起来写得不错

选好操作系统进入文档<https://deepstream.io/install/>

我这里运用Ubuntu

```shell
work@iZ94fnej0x9Z:~$ source /etc/lsb-release && echo "deb http://dl.bintray.com/deepstreamio/deb ${DISTRIB_CODENAME} main" | sudo tee -a /etc/apt/sources.list
[sudo] password for work:
deb http://dl.bintray.com/deepstreamio/deb trusty main
work@iZ94fnej0x9Z:~$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 379CE192D401AB61
Executing: gpg --ignore-time-conflict --no-options --no-default-keyring --homedir /tmp/tmp.OkH6HAidlo --no-auto-check-trustdb --trust-model always --keyring /etc/apt/trusted.gpg --primary-keyring /etc/apt/trusted.gpg --keyring /etc/apt/trusted.gpg.d/nginx-stable.gpg --keyring /etc/apt/trusted.gpg.d/ondrej-php.gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 379CE192D401AB61
gpg: requesting key D401AB61 from hkp server keyserver.ubuntu.com
gpg: key D401AB61: public key "Bintray (by JFrog) <bintray@bintray.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1)
work@iZ94fnej0x9Z:~$ sudo apt-get update
...
work@iZ94fnej0x9Z:~$ sudo apt-get install -y deepstream.io
# 启动deepstram
work@iZ94fnej0x9Z:~$ deepstream start
```

![deepstream按照成功](QQ20161013-0.png)

到这里,基本就按照完成了,目前体验良好

本身提供一个cli,相关命令见<https://deepstream.io/docs/server/command-line-interface/>

你可以更改一下你的配置

```shell
$ cd ~
$ cp /etc/deepstream/* .
$ ls
config.yml  permissions.yml  users.yml
$ deepstream start -c config.yml
```

deepstram源码地址<https://github.com/deepstreamIO/deepstream.io/releases>

这个README.mk也是非常炫酷....

# client
```html
#index.html
<!DOCTYPE html>
<html>
  <head>
    <script src="https://cdn.rawgit.com/deepstreamIO/deepstream.io-client-js/master/dist/deepstream.min.js"></script>
  </head>
  <body>
    <input type="text" />
    <script type="text/javascript">
     var client = deepstream('localhost:6020').login()
     var record = client.record.getRecord('some-name')
     var input = document.querySelector('input')

      input.onkeyup = (function() {
          record.set('firstname', input.value)
        })
    </script>
  </body>
</html>

#website.html
<!DOCTYPE html>
<html>
  <head>
    <script src="https://cdn.rawgit.com/deepstreamIO/deepstream.io-client-js/master/dist/deepstream.min.js"></script>
  </head>
  <body>
    <input type="text" />
    <script type="text/javascript">
    var client = deepstream('120.24.37.206:6020').login()
    var record = client.record.getRecord('some-name')
    var input = document.querySelector('input')

     record.subscribe('firstname', function(value) {
        input.value = value
     })
    </script>
  </body>
</html>
```
