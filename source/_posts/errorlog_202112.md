---
title: Error Log Of 2021-12
date: 2022-1-11
categories:
- error
tags:
- error
---

🔺 简单整理2021年12月份遇到的报错日志及解决方法如下：

<!--more-->


##### 2021.12.30

命令：
初始化 MySQL 表中基础数据：

    docker-compose exec fpm php yii init/data

报错：
```
Exception 'yii\db\Exception' with message 'SQLSTATE[HY000]:
General error: 1366 Incorrect string value: '\xE4\xBB\xA5\xE5\xA4\xAA...' for column 'name_cn' at row 1
The SQL being executed was: INSERT INTO `currency` (`symbol`, `name_cn`, `name_en`) VALUES ('eth', '以太坊', 'Ethereum')'
```

处理方式：
修改框架数据库字符集 utf8->utf8mb4

##### 2021.12.31
widows下命令：
```
winpty docker run --rm -it -v "C:\phpstudy_pro\WWW\chainadmin":/work
--network chainadmin_project python:3.9-slim-bullseye sh -c 'cd /work
&& pip install -r ./process_data/requirements.txt
&& python ./process_data/generate_mining_stats.py --mysql-host mysql --user root --passwd root --parse-date all'
```

报错：
```
ERROR: Could not install packages due to an OSError: HTTPSConnectionPool(host='files.pythonhosted.org', port=443):
Max retries exceeded with url: /packages/a7/f6/84070ab117e6b080a87aac0ac9e4d269a66c6f6076ad81509bd0aac828d8/autopep8-1.5.7-py2.py3-none-any.whl
(Caused by ConnectTimeoutError(<pip._vendor.urllib3.connection.HTTPSConnection object at 0x7fd2819e9160>,
'Connection to files.pythonhosted.org timed out. (connect timeout=15)'))
```

处理方式：
```
winpty docker run --rm -it -v "C:\phpstudy_pro\WWW\chainadmin":/work
--network chainadmin_project python:3.9-slim-bullseye sh -c
'cd /work && pip install -r ./process_data/requirements.txt --index-url=https://mirrors.aliyun.com/pypi/simple/
&& python ./process_data/generate_mining_stats.py --mysql-host mysql --user root --passwd root --parse-date all'
```

小结：
切换了国内源

命令：

    php yii migrate

报错：
```
Exception 'yii\db\Exception' with message 'SQLSTATE[HY000] [2002]
php_network_getaddresses: getaddrinfo failed: nodename nor servname provided, or not known'
in /Users/baozhigang/work/chainadmin/vendor/yiisoft/yii2/db/Connection.php:649
```

处理方式：
进入fpm终端执行
