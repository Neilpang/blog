---
tags:
  - php
  - composer
---


用下面的命令安装:

```
curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer

```

值得注意的是, php 的 `composer` 是一个开发时的工具, 当用它解决完依赖以后, 应该把依赖都保存.

安装依赖的方法:
```
// vendor：厂商名 package：包名
composer require vendor/package

```

