# 阿里云服务器裸机初始化及安装Docker + Nginx + PHP + MySQL + Luajit

<!-- index-menu -->

## 本篇文章目的

* 初始化阿里云服务器环境
* 优化Ubuntu Server系统环境
* 安装Docker服务
* 安装上Nginx + PHP + MySQL + Luajit，并进行相应优化
* 配置以上服务的开机自启动


<!--more-->


## 初始系统环境

### 更新升级系统内核

当变成生产环境或开发环境之后，很难再有机会进行系统内核更新并重启系统，所以在一开始进行更新升级，是非常有必要的。

从阿里云的控制平台，选择系统镜像，Ubuntu Server 14.04 LTS系统版本，并配置root帐号的密码，进行系统盘的初始化。

首次，只能以root帐户登入，更新源并进行首次升级

    apt-get update

更新源的时候，有可能会碰到出错信息，没关系，一般是因为一些网络获取不到的原因，再多次运行下命令就好了。
更新完成后，进行升级

    apt-get dist-upgrade -y

### 设置hostname

通过设置易记的hostname，方便以后进行服务器管理，当前仍是以root帐户操作

    hostname YOUR_HOSTNAME

需要长久生效的话，需要编辑 /etc/hostname 文件，将里面的内容清空，放上你想要的名称。

    echo "YOUR_HOSTNAME" > /etc/hostname

**设置好hostname之后，需要编辑`/etc/hosts`文件，将该服务器的内网IP配置指向该hostname，并将原本的指向原来hostname的那一行删掉**

### 优化虚拟内存启用比率

**阿里云所安装的ubuntu server镜像已经默认做了此项优化了，所以此项优化命令不需执行。**

    sysctl vm.swappiness=0

设置成0，表示只有真正的内存使用快要满的时候才会启用虚拟内存，如果不想这么极端的话，也可以设置成5，表示真正的内存使用率达到95%左右的时候才会启用虚拟内存。该命令设置之后会即时生效，但会产生一个问题，重启之后会变回原来的设置，所以为了长久的生效，可运行以下命令

    echo "vm.swappiness=0" >> /etc/sysctl.conf

### 开启HugePage特性
HugePage特性，在PHP7的opcache中会使用到，可以预先分配大页内存，然后被其使用。

    sysctl vm.nr_hugepages=512

为了长久的生效，可运行以下命令

    echo "vm.nr_hugepages=512" >> /etc/sysctl.conf

### 优化文件句柄数量

**阿里云的ubuntu server默认已做好此项优化了，所以此项优化命令不需执行。**

编辑`/etc/security/limits.conf`文件，在后面加上以下内容，如果已经有了，就不必添加了。

    ## 全局设置
    ## nofile 表示文件句柄数的设置
    ## soft即是软限制，hard是硬限制。用户可以超过soft设置的值，但一定不能超过hard 的值。一般soft比hard小
    * soft nofile 65536
    * hard nofile 65536

### 挂载云服务器数据盘

通常都会购买一定容量的数据盘，挂载方式就直接参考阿里云上的帮助说明吧，可根据需要进行调整，如格式可用 ext4 格式等。参考链接

[阿里云服务器挂载数据盘帮助链接](https://help.aliyun.com/document_detail/ecs/operation-guide/disk-operation/attach.html)

这边做了几个调整：
* 在根据下建立了 /data 目录
* 将数据盘挂载到 /data 目录下
* 如果有多个数据盘的话，可以用 /data1, /data2 这样的命名方式并对应起来，因为本样例中只有一个数据盘，所以只建 /data 目录
* /data 目录，用于后续相关程序及非root及work帐户的HOME目录存放使用

## 建立work帐号，用于工作环境使用

建立work帐号，用于工作环境使用，即将需要运行的服务，都交由work帐号下进行操作执行，这通常是一个行业惯例，当然，也可以按照你自己的喜好来建立别的帐号。

    adduser work

将`/data`目录设成为work所有，方便后续使用

    chown -R work:work /data

将`work`帐户加入到`sudo`权限中，编辑`/etc/sudoers`文件，编辑方法请看下面注释说明，注释说明可不用添加进去

    # User privilege specification
    root    ALL=(ALL:ALL) ALL
    ## 以上两行是原本就有的内容，在下面加入对于work的配置的一行即可
    work    ALL=(ALL:ALL) ALL

**注意：编辑此文件时，千万注意，以免语法不对，导致系统出错。**

### 重启一次服务器
重启，是为了以上所有操作，最主要是新内核可以生效

    init 6

重启完成之后，后续使用work帐号登录。
**注意：后续所有操作如无特别说明，均使用`work`帐号进行操作。**

### 安装基本工具

**再提醒一次，请使用`work`帐号登录。**
安装一些基本的工具，方便后续服务器的使用，具体每一个工具的用途请自行google。

    sudo apt-get install -y tree git git-doc git-man realpath tmux exuberant-ctags build-essential automake autoconf cmake vim unzip zip curl apache2-utils python-dev mercurial python-pip lrzsz

### 安装Docker
**先安装docker，是因为有可能需要重启。**
本安装方法是依据官网上，针对Ubuntu的说明进行操作的。
   
    sudo apt-get update

先多运行以上命令几次，确保不报错，更新下升级源。然后运行以下命令，添加docker的官方源到系统中，并进行安装。

    sudo apt-get install apt-transport-https
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
    sudo bash -c "echo deb https://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list"
    sudo apt-get update
    sudo apt-get install -y lxc-docker

因为可能被墙的原因，导致更新源操作时经常出错，需要多执行几次。安装成功之后，建议重启一次。启动docker的命令如下：

    sudo service docker start

### 配置vim

    http://pan.baidu.com/s/1bpI40KR

从上面的百度网盘地址下载回已经配置好的配置文件，建议生成一个`/data/resource`的目录，并将其通过`rz -bye`的命令上传到服务器中。假设，已经将文件上传放好到`/data/resource/`目录下，运行以下命令解压

    tar zxf vimcfg.tar.gz -C ~/

### 配置tmux

    http://pan.baidu.com/s/1hrQR3q0

从上面的百度网盘地址中下载回tmux已经配置好的配置文件，并上传到服务器上，然后运行以下命令解压

    tar zxf tmuxcfg.tar.gz -C ~/

[点击查看tmux参考教程](http://blog.kissdata.com/2014/07/29/tmux.html)

## 环境安装前置说明

* 建立`/data/env`目录，用于存放所有`work`帐号需要安装编译的软件工具
* 针对每一个软件工具，会在`/data/env`目录下，建立相应的目录，如mysql，即会有`/data/env/mysql`目录，然后在编译安装时，根据版本号，在该目录下再指定编译到该版本号的目录中，如需要编译mysql的5.7.11版本，即会在编译时指定其`prefix`参数指向`/data/env/mysql/mysql-5.7.11`目录。
* 在每一个软件工具的目录下，又会根据当前系统正在使用的版本建立指向其的`default`软链接，如在`/data/env/mysql`目录下已经编译安装有两个版本，一个是`mysql-5.6.7`，另一个是`mysql-5.7.11`，若当前系统所使用版本为5.7.11版本，即会用以下命令建立一个default软链接

        ln -s /data/env/mysql/mysql-5.7.11 default

* 在环境变量及SHELL脚本中，当需要使用到某一个软件工具的所在目录时，务必使用其default软链接，这样以后在升级该软件工具时，只需修改default软链接指向新升级好的版本目录即可。如需要在脚本文件中使用到mysql的所在目录时，应该使用以下链接路径

        /data/env/mysql/default


## MySQL

### 预先安装所需环境

    sudo apt-get install libwrap0-dev openssl libssl-dev  libncurses5-dev  libcrypto++-dev bison libpcre3-dev

### 下载编译

写本文档时，最新的稳定版本为5.7.11，所以进入到`/data/resource`目录，然后用以下命令下载该版本的带boost库的MySQL源代码包

    wget http://cdn.mysql.com//Downloads/MySQL-5.7/mysql-boost-5.7.11.tar.gz

在`/data/env`目录下，建立mysql的主目录

    mkdir -p /data/env/mysql

解压下载回来的mysql源码

    cd /data/resource
    tar zxf mysql-boost-5.7.11.tar.gz
    cd mysql-5.7.11

然后用以下选项进行编译，这些选项参数是针对生产环境进行设置的，可根据自己的需要进行调整

    cmake \
    -DCMAKE_INSTALL_PREFIX=/data/env/mysql/mysql-5.7.11 \
    -DWITH_INNOBASE_STORAGE_ENGINE=1  \
    -DWITH_ARCHIVE_STORAGE_ENGINE=1   \
    -DWITH_BLACKHOLE_STORAGE_ENGINE=1 \
    -DWITH_PERFSCHEMA_STORAGE_ENGINE=1 \
    -DWITH_BOOST=./boost/ \
    -DDEFAULT_CHARSET=utf8mb4 \
    -DDEFAULT_COLLATION=utf8mb4_bin \
    -DENABLED_LOCAL_INFILE=1 \
    -DENABLED_PROFILING=1 \
    -DWITH_EMBEDDED_SERVER=1 \
    -DWITH_EXTRA_CHARSETS=all \
    -DWITH_LIBWRAP=1 \
    -DWITH_SSL=yes \
    -DWITH_DEBUG=0

当编译过程中出现错误时，解决完相应问题后，需要先删掉`CMakeCache.txt`文件，再重新运行编译命令，直至成功。接着，进行安装

    make
    make install

建立`default`软链接

    cd /data/env/mysql
    ln -s /data/env/mysql/mysql-5.7.11 default

将mysql可执行命令放入到work帐号的PATH中，编辑`work`帐号的`~/.profile`文件，在后面添加如下内容：

    export PATH=/data/env/mysql/default/bin:$PATH

然后重加载环境文件

    source ~/.profile

### 生成配置文件
建立统一存放各配置及文件的目录，`conf`目录用来统一存储所有的my.cnf配置文件，且一般根据端口号加上相应前后缀，如针对于3306端口的配置文件，即为`conf/my-3306.cnf`文件

    cd /data/env/mysql/
    mkdir conf

编辑生成`/data/env/mysql/conf/my-3306.cnf`文件，该文件中的参数，是根据4核8G内存的服务器配置进行相应配置的，有需要请根据自己服务器配置进行相应调整。

	# For advice on how to change settings please see
	# http://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html
	# http://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html

	[client]
	port = 3306
	socket = /tmp/mysql.sock
	default-character-set = utf8mb4


	[mysqld]
	port = 3306
	socket = /tmp/mysql.sock

	# These are commonly set, remove the # and set as required.
	basedir = /data/env/mysql/default
	datadir = /data/mysql/3306
	server_id = 1

	init-connect = 'SET NAMES utf8mb4'
	character-set-server = utf8mb4



	max_connections = 1000
	max_connect_errors = 6000
	open_files_limit = 65535
	max_heap_table_size = 64M
	tmp_table_size = 64M

	# 随机读缓冲区大小。当按任意顺序读取行时(例如，按照排序顺序)，将分配一个随机读缓存区。进行排序查询时，MySQL会首先扫描一遍该缓冲，以避免磁盘搜索，提高查询速度，如果需要排序大量数据，可适当调高该值。但MySQL会为每个客户连接发放该缓冲空间，所以应尽量适当设置该值，以避免内存开销过大。
	read_rnd_buffer_size = 4M 
	read_buffer_size = 1M

	# 每个线程单独分配缓冲区
	join_buffer_size = 8M

	# order by或group by的缓冲区大小
	sort_buffer_size = 8M
	myisam_sort_buffer_size = 8M

	# key_buffer_size只对MyISAM表起作用。即使你不使用MyISAM表，但是内部的临时磁盘表是MyISAM表，也要使用该值。可以使用检查状态值created_tmp_disk_tables得知详情。
	key_buffer_size = 64M

	## 关闭query缓存，网上有文章对此分析过，但仍需根据情况进行验证与调整
	query_cache_type = 0
	query_cache_size = 0
	query_cache_limit = 0

	# 交互用户不活动连接被自动断开的时间
	interactive_timeout = 300
	wait_timeout = 300

	# 开启慢查询功能
	slow-query-log = 1
	# 默认为10秒，这里改为3秒，超过该值即为慢查询
	long_query_time = 3
	slow_query_log_file = /data/mysql/3306/slow-query.log



	## 更改默认的引擎为 InnoDB
	default_storage_engine = InnoDB
	# Remove leading # and set to the amount of RAM for the most important data
	# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
	innodb_buffer_pool_size = 256M
	innodb_data_home_dir = /data/mysql/3306
	innodb_data_file_path = ibdata1:64M:autoextend

	## 写同步日志
	# 0：每秒写入并刷写到磁盘，这种情况下性能好，但崩溃时会丢失最后1s的日志
	# 1：默认值，每次事务提交就写日志并到磁盘上，最安全的配置，但性能也最差
	# 2：每次事务提交会写日志，但每隔1s才会刷写到磁盘上，进程崩溃时由于日志已经写入到系统缓存，所以并不会丢失数据。但操作系统崩溃时，通常会丢失最后1s的日志
	innodb_flush_log_at_trx_commit = 2




	# Remove leading # to turn on a very important data integrity option: logging
	# changes to the binary log between backups.
	log_bin



	sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES 


### 初始化数据库
用以下命令进行初始化，且为空密码，后续再修改密码。

    /data/env/mysql/default/bin/mysqld --defaults-file=/data/env/mysql/conf/my-3306.cnf --initialize-insecure

### 启动数据库并修改root密码

启动

    /data/env/mysql/default/bin/mysqld_safe --defaults-file=/data/env/mysql/conf/my-3306.cnf &

修改root密码

    mysqladmin -uroot password

在命令随后的提示中修改密码即可

### 关闭数据库的方式

    mysqladmin -uroot -p shutdown

### 设置mysql随机启动
在`/etc/init.d/rc.local`文件后面添加如下内容：

    ### 用work帐号开机自启动 mysql
    su work -c "/data/env/mysql/default/bin/mysqld_safe --defaults-file=/data/env/mysql/conf/my-3306.cnf &"


## PHP
### 预先安装所需环境

    sudo apt-get install libxml2-dev libsslcommon2-dev libcurl4-openssl-dev libbz2-dev libjpeg-dev libpng12-dev libxpm-dev libfreetype6-dev libmcrypt-dev libxslt1-dev 

### 下载编译

写本文档时，最新的稳定版本为7.0.4，所以进入到`/data/resource`目录，然后用以下命令下载源代码包

    wget http://cn2.php.net/distributions/php-7.0.4.tar.gz

在`/data/env`目录下，建立mysql的主目录

    mkdir -p /data/env/php

解压下载回来的源码

    cd /data/resource
    tar zxf php-7.0.4.tar.gz
    cd php-7.0.4

然后用以下选项进行编译，这些选项参数是针对生产环境进行设置的，可根据自己的需要进行调整

	./configure \
	--prefix=/data/env/php/php-7.0.4 \
	--with-mysqli=/data/env/mysql/default/bin/mysql_config \
	--with-pdo-mysql=/data/env/mysql/default \
	--enable-fpm \
	--with-curl \
	--with-pear \
	--with-gd \
	--with-jpeg-dir \
	--with-png-dir \
	--with-zlib \
	--with-xpm-dir \
	--with-freetype-dir \
	--with-mcrypt \
	--with-mhash \
	--with-openssl \
	--with-xmlrpc \
	--with-xsl \
	--with-bz2 \
	--with-gettext \
	--disable-debug \
	--enable-exif \
	--enable-wddx \
	--enable-zip \
	--enable-bcmath \
	--enable-calendar \
	--enable-ftp \
	--enable-mbstring \
	--enable-soap \
	--enable-sockets \
	--enable-shmop \
	--enable-dba \
	--enable-sysvsem \
	--enable-sysvshm \
	--enable-opcache \
	--enable-sysvmsg

当编译过程中出现错误时，解决完相应问题后，再重新运行编译命令，直至成功。接着，进行安装

    make
    make install

建立`default`软链接

    cd /data/env/php
    ln -s /data/env/php/php-7.0.4 default

### 配置 php.ini
在源码安装包里，已经预定义了两份配置文件，一个是生产环境，一个是开发环境，因这里是搭建生产环境，所以用以下命令
    
    cd /data/resource/php-7.0.4
    cp php.ini-production /data/env/php/php-7.0.4/lib/php.ini

下面对`/data/env/php/php-7.0.4/lib/php.ini`针对进行了一些优化配置：
* 首先是时区设置，找到其中被注释的一行（约第912行）

        ;date.timezone =

    将注释去掉，然后改成如下

        date.timezone = PRC

* 打开opcache特性，提升性能，找到以下两行（约第1730行附近），原来都是注释的

        ;opcache.enable=0

        ;opcache.enable_cli=0

    更改成

        opcache.enable=1

        opcache.enable_cli=1  

    然后在约第836行，即内容

        ;;;;;;;;;;;;;;;;;;;;;;
        ; Dynamic Extensions ;
        ;;;;;;;;;;;;;;;;;;;;;;  

    下面，增加一行

        zend_extension=opcache.so

* 增加脚本的执行最大时间，找到相应参数，约第368行，调整成如下

        max_execution_time = 300

* 增长脚本对于输入内容的解析时间，约第378行，调整如下

        max_input_time = 120

* 提高最大内存限制，约第389行，调整如下

        memory_limit = 512M

* 提高通过POST方式传入数据量限制，约第656行，调整如下

        post_max_size = 32M

* 提高上传文件大小限制，约第798行，调整如下

        upload_max_filesize = 32M

* 提高连接超时设置，约第826行，调整如下

        default_socket_timeout = 600

### 配置 php-fpm
先用默认的生成一份

    cd /data/env/php/php-7.0.4/etc/
    cp php-fpm.conf.default php-fpm.conf

PHP7将Pool的配置文件放置到了所安装好的php的`etc/php-fpm.d`目录下以`conf`结尾的文件中了，做如下操作

    cd /data/env/php/php-7.0.4/etc/php-fpm.d
    cp www.conf.default www.conf

### 为php-fpm运行建立nobody组

默认php-fpm.conf所指定的user与group均为nobody，ubuntu 14.04 server默认建有nobody用户，但未有nobody组，所以用以下命令建立nobody组，并将nobody用户归到其组下

    sudo groupadd nobody
    sudo usermod -g nobody nobody


### 权限控制与提示

默认使用的nobody用户与组，对于权限来说就已经够用了，下面根据几种情况给出在配合nginx使用时的安全配置的例子

**数据缓存目录**

数据缓存目录的特点，就是需要777权限，但无须提供给外部用户访问与使用，所以可如下配置

    location ~ "^/cache" {
        return 403;
    }
    location ~ "\.php$" {
        fastcgi_pass 127.0.0.0:9000;
        ....................
    }

**上传目录**

此目录的特点是需要开放访问权限，但所有文件不能由php 引擎解析（包括后缀名改为 gif 的木马文件），可如下配置

    location ~ "^/attachments" {
    }
    location ~ "\.php$" {
        fastcgi_pass 127.0.0.0:9000;
        ....................
    }

注意，上面对attachments 目录的 location 定义中是没有任何语句的。 nginx 对正则表达式的 location 匹配优先级最高，任何一个用正则表达式定义的 location, 只要匹配一次，将不会再匹配其它正则表达式定义的 location 。

现在，请在attachments 目录下建立一个 php 脚本文件，再通过浏览器访问安，我们发现浏览器提示下载，这说明 nginx 把 attachments 目录下的文件当成静态文件处理，并没有交给 php fastcgi 处理。这样即使可写目录被植入木马，但因为其无法被执行，网站也就更安全了。

**静态文件生成目录**

这些目录一般都是php 生成的静态页的保存目录，显然与上传目录有类似之处，按上传目录的权限设置即可。

可以预见的是，如果我们设置了较严格的权限，即使网站php程序存在漏洞，木马脚本也只能被写入到权限为 777 的目录中去，如果配合上述严格的目录权限控制，木马也无法被触发运行，整个系统的安全性显然会有显著的提高。

### 启动php-fpm

    sudo /data/env/php/default/sbin/php-fpm

### 关闭php-fpm
关闭的话，使用以下命令查找出进程号

    sudo ps -x | grep php-fpm

输出结果中应该会有多行，其中有一行有 master process 的字样的 php-fpm 的进程号即为我们要查找的进程号，假设查找到的进程号为 1214 ,则运行以下命令关闭 php-fpm 进行

    sudo kill -QUIT 1214

### 将php-fpm设置成随机启动
在`/etc/init.d/rc.local`文件后面添加如下内容：

    ### 启动php-fpm               
    /data/env/php/default/sbin/php-fpm

## LuaJit

写本文档时，最新的稳定版本为2.0.4，从`http://luajit.org/`下载回相应的`LuaJIT-2.0.4.tar.gz`文件，并通过`rz -bye`命令上传到`/data/resource`目录。
在`/data/env`目录下，建立luajit的主目录

    mkdir -p /data/env/luajit

解压下载回来的源码

    cd /data/resource
    tar zxf LuaJIT-2.0.4.tar.gz 
    cd LuaJIT-2.0.4/

luajit可直接进行安装，进行指定安装

    make PREFIX=/data/env/luajit/luajit-2.0.4
    make install PREFIX=/data/env/luajit/luajit-2.0.4

建立`default`软链接

    cd /data/env/luajit
    ln -s /data/env/luajit/luajit-2.0.4 default

将可执行命令放入到work帐号的PATH中，编辑work帐号的~/.profile文件，在后面添加如下内容：

    export PATH=/data/env/luajit/default/bin:$PATH

然后重加载环境文件

    source ~/.profile

## Nginx

### 下载、编译、安装

写本文档时，最新的稳定版本为1.9.12，下载到`/data/resource`目录。

    wget http://nginx.org/download/nginx-1.9.12.tar.gz

因为需要配合luajit进行使用，所以也需要下载相应在编译nginx时需要用到的一些代码包，如下

    wget https://github.com/simpl/ngx_devel_kit/archive/v0.2.19.tar.gz
    mv v0.2.19.tar.gz ngx_devel_kit-0.2.19.tar.gz
    tar zxf ngx_devel_kit-0.2.19.tar.gz

    wget https://github.com/openresty/lua-nginx-module/archive/v0.10.2.tar.gz
    mv v0.10.2.tar.gz lua-nginx-module-0.10.2.tar.gz
    tar zxf lua-nginx-module-0.10.2.tar.gz

在`/data/env`目录下，建立主目录

    mkdir -p /data/env/nginx

解压下载回来的源码

    cd /data/resource
    tar zxf nginx-1.9.12.tar.gz 
    cd nginx-1.9.12/

编译之前，需要告诉系统luajit的环境，如下

    export LUAJIT_LIB=/data/env/luajit/default/lib
    export LUAJIT_INC=/data/env/luajit/default/include/luajit-2.0

然后，进行编译

	./configure \
	--prefix=/data/env/nginx/nginx-1.9.12 \
	--with-http_ssl_module \
	--with-http_stub_status_module \
	--with-http_gzip_static_module \
	--with-http_realip_module \
	--add-module=/data/resource/ngx_devel_kit-0.2.19 \
	--add-module=/data/resource/lua-nginx-module-0.10.2

编译成功后，进行安装

    make
    make install

建立`default`链接

    cd /data/env/nginx
    ln -s /data/env/nginx/nginx-1.9.12 default

### 编译安装lua常用的库
在nginx主目录下建立`lua_so`目录，用来存放库。

    cd /data/env/nginx
    mkdir lua_so

**redis库**

    cd /data/env/nginx/lua_so
    wget https://raw.githubusercontent.com/openresty/lua-resty-redis/master/lib/resty/redis.lua

** struct pack **

    cd /data/resource
    wget http://www.inf.puc-rio.br/~roberto/struct/struct-0.2.tar.gz
    mkdir struct-0.2
    tar zxvf struct-0.2.tar.gz -C struct-0.2
    cd struct-0.2/

修改makefile中的LUADIR变量如下：

    LUADIR = /data/env/luajit/default/include/luajit-2.0/

编译，并移到文件夹：

    make
    cp struct.so /data/env/nginx/lua_so/
    
**cjson**

下载cjson，并解压

    cd /data/resource
    wget http://www.kyne.com.au/~mark/software/download/lua-cjson-2.1.0.tar.gz
    tar zxf lua-cjson-2.1.0.tar.gz
    cd lua-cjson-2.1.0

修改`MakeFile`文件中关于LUA_INCLUDE_DIR的设置，如下：

    LUA_INCLUDE_DIR = /data/env/luajit/default/include/luajit-2.0/

编译，并移动到上面所说的lua_so文件夹中：

    make
    cp cjson.so /data/env/nginx/lua_so/

**lua-zlib**

下载lua-zlib
    
    cd /data/resource
    git clone https://github.com/brimworks/lua-zlib.git
    cd lua-zlib

建立一个liblua.so的软链接，不然编译不成功：

    sudo ln -s /data/env/luajit/default/lib/libluajit-5.1.so /usr/lib/liblua.so

修改`Makefile`文件，使以下几个变量值如下：

    LUAPATH  ?= /data/env/luajit/default
    LUACPATH ?= /data/env/luajit/default/lib
    INCDIR   ?= -I/data/env/luajit/default/include/luajit-2.0
    LIBDIR   ?= -L/usr/lib -L/data/env/luajit/default/lib

编译，并拷贝至lua_so文件夹：

    make linux
    cp zlib.so /data/env/nginx/lua_so/

**mysql, bitop**
下载mysql.lua，mysql不需要编译，直接使用mysql.lua文件即可，不过会用到bit.so，所以要编译bit.so

    cd /data/resource
    wget https://raw.githubusercontent.com/openresty/lua-resty-mysql/master/lib/resty/mysql.lua
    cp mysql.lua /data/env/nginx/lua_so/

下载bitop

    wget http://bitop.luajit.org/download/LuaBitOp-1.0.2.tar.gz
    tar zxf LuaBitOp-1.0.2.tar.gz
    cd LuaBitOp-1.0.2

修改LuaBitOp中的`Makefile`文件前面中的INCLUDES变量如下：

    INCLUDES= -I/data/env/luajit/default/include/luajit-2.0

编译，并拷贝到lua_so文件夹：

    make
    cp bit.so /data/env/nginx/lua_so/

### 重载下库

安装完成之后，最好重载下库

    sudo /sbin/ldconfig

### 配置`nginx.conf`
在配置之前，先建立相关目录

    cd /data/env/nginx
    mkdir -p site-enabled-common site-enabled-frontend site-enabled-background

    mkdir -p /data/nginx
    cd /data/nginx
    mkdir -p log lua_files hosts
    touch lua_files/init.lua

**说明：**
* site-enabled-common：存放共用站点配置文件
* site-enabled-frontend：存放前端站点配置文件
* site-enabled-background：存放后台站点配置文件
* /data/nginx/lua_files/init.lua：这是lua的初始文件，所有站点共用一个

然后将`/data/env/nginx/default/conf/nginx.conf`文件内容替换成如下：

	user  nobody    nobody;

	## worker_processes的数值设置为cpu的核数
	worker_processes  4;

	### 绑定每个nginx进程所使用的CPU，使nginx可以利用cpu的多核，根据上面的不同数值进行设置，下面是数值为4时的设置
	worker_cpu_affinity 0001 0010 0100 1000;

	events {
	    use epoll;
	    worker_connections  65535;
	}


	http {
	    include       mime.types;
	    default_type  application/octet-stream;

	    ### 加载lua的库
	    # 查找 *.lua
	    lua_package_path '/data/env/nginx/lua_so/?.lua;;';
	    # 查找 *.so
	    lua_package_cpath '/data/evn/nginx/lua_so/?.so;;';

	    ## 启用gzip
	    gzip on;
	    gzip_disable "msie6";
	    gzip_vary on;
	    gzip_proxied any;
	    gzip_comp_level 6;
	    gzip_min_length 1100;
	    gzip_buffers 16 8k;
	    gzip_http_version 1.1;
	    gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;

	    # 缓存被频繁访问的文件相关的信息，默认是off
	    # 参数注释如下：
	    # max:设置缓存中的最大文件描述符数量，如果缓存被占满，采用LRU算法将描述符关闭。
	    # inactive:设置存活时间，默认是10s
	    # min_uses:设置在inactive时间段内，日志文件最少使用多少次后，该日志文件描述符记入缓存中，默认是1次
	    # valid:设置检查频率，默认60s
	    # off：禁用缓存
	    open_log_file_cache max=10 inactive=20s valid=60s min_uses=2;
	    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
	        '$status $body_bytes_sent "$http_referer" '
	        '"$http_user_agent" "$http_x_forwarded_for"';

	    ## 若考虑性能时，可增加缓冲
	    #access_log  /data/nginx/log/access.log main buffer=16k;
	    access_log  /data/nginx/log/access.log main;
	    error_log   /data/nginx/log/error.log; 

	    ## 调整客户端超时时间
	    client_max_body_size 50M;
	    client_body_buffer_size 1m;
	    client_body_timeout 15;
	    client_header_timeout 15;
	    keepalive_timeout 15;
	    send_timeout 15;
	    sendfile on;
	    tcp_nopush on;
	    tcp_nodelay on;

	    ## 调整输出缓冲区大小
	    fastcgi_buffers 256 16k;
	    fastcgi_buffer_size 128k;
	    fastcgi_connect_timeout 3s;
	    fastcgi_send_timeout 120s;
	    fastcgi_read_timeout 120s;
	    reset_timedout_connection on;
	    server_names_hash_bucket_size 100;

	    ## lua的初始化文件
	    init_by_lua_file /data/nginx/lua_files/init.lua;

	    include /data/env/nginx/site-enabled-frontend/*;
	    include /data/env/nginx/site-enabled-background/*;
	    include /data/env/nginx/site-enabled-common/*;
	}


然后再生成一个`/data/env/nginx/site-enabled-common/localhost`文件，待会可用于测试是否安装配置成功，其内容如下:

    server {
        listen       80;
        server_name  localhost;
        root /data/nginx/hosts/localhost;

        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

         location ~ \.php$ {
            include fastcgi.conf;  
            fastcgi_pass  127.0.0.1:9000;
            fastcgi_index index.php;
            fastcgi_param  SCRIPT_FILENAME  /$document_root$fastcgi_script_name;
        }
    }

然后建立`/data/nginx/hosts/localhost`目录，并在目录下生成一`index.php`的文件，内容如下：

    <?php
    phpinfo();

**注意：测试完成之后，务必删掉此文件，以免泄露服务器的相关配置信息。**


### 将nginx路径告诉sudo用户

编辑`/etc/sudoers`文件，针对其中的一行以`Defaults    secure_path`开头的一行，在其后面追加上`/data/env/nginx/default/sbin`路径，最终这一行内容示例如下：

    Defaults    secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/data/env/nginx/default/sbin"

这样添加之后，在运行nginx命令时，就不用全路径了，直接这样即可

    sudo nginx -t
    sudo nginx -s reload

### 将nginx设置成随机启动

在/etc/init.d/rc.local文件后面添加如下内容：

    ### 启动nginx               
    /data/env/nginx/default/sbin/nginx

## 后记

这么多操作，都是手动的，太耗时了，其实在线上还是会用`ansible`进行自动化装机的。






    