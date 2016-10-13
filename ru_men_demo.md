# 入门DEMO

# 目的

通过在一个浏览器打log,输出到别的浏览器中

# 开始

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
```

到这里,基本就按照完成了,目前体验良好

本身提供一个cli,相关命令见<https://deepstream.io/docs/server/command-line-interface/>

