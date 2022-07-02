---
layout: post   	
catalog: true 	
tags:
    - Linux
---



## 安装前卸载原有PHP

* ```
  //删除php的相关包及配置
  apt-get autoremove php7*
  ```

* ```
  //删除关联
  find /etc -name "*php*" |xargs rm -rf
  ```

* ```
  //清除dept列表
  apt purge `dpkg -l | grep php| awk '{print $2}' |tr "\n" " "`
  ```

## Ubuntu20安装php

* 安装php和php的apache模块

```bash
sudo apt install -y php libapache2-mod-php
```

* 重启apache

```bash
sudo service apache2 restart
```

* 测试php是否安装成功

web目录`/var/www/html`

在web目录下新建`php.info`

```php
<?php
phpinfo();
?>
```

在浏览器输入 `http://服务器的ip/info.php` 出现内容即代表安装成功

* 安装好的php所在路径`/etc/php`

* php的配置文件`/etc/php/7.4/cli`

## 安装phpize

* 安装和php版本对应的(修改版本号即可)

```bash
apt-get install php7.4-dev
```

* 安装后phpize的路径`/usr/bin/phpize`



## 安装VLD

* 下载对应版本的VLD`https://pecl.php.net/package/vld`

php7.4 对应 0.16.0

* 解压，进入 0.16.0文件夹，执行命令`/usr/bin/phpize`，出现以下内容

```
Configuring for:
PHP Api Version:         20190902
Zend Module Api No:      20190902
Zend Extension Api No:   320190902
```

* 查找php-config，使用命令`find / -name php-config`

找到`php-config7.4`的路径(即php对应版本的config)

* 配置编译的php-config路径

在 0.16.0 目录下执行

```
./configure --with-php-config=/usr/bin/php-config7.4 --enable-vld
```

* 编译安装

在 0.16.0 目录下执行

```
make && make install
```

* 修改`php.ini`

找到`vld.so`的路径，使用`find / -name vld.so`

修改`php.ini`，Ctrl + f 搜索`Dynamic Extensions`，在正确位置添加`extension=vld.so的路径`，保存，退出

## 测试

使用命令`php -dvld.active=1 文件路径` 

输出内容中，有`line     #* E I O op                           fetch          ext  return  operands`这么一行，说明成功



## 参考资料

[https://blog.csdn.net/keyiis_sh/article/details/113776788?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-2.control&amp;depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-2.control]: 
[https://www.cnblogs.com/fsong/p/11337059.html]: 
[https://www.php.cn/php-ask-459228.html]: 
[https://www.cnblogs.com/soupig/p/8037900.html]: 

