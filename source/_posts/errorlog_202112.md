---
title: Error Log Of 2021-12
date: 2022-1-11
categories:
- error
tags:
- error
---

ðº ç®åæ´ç2021å¹´12æä»½éå°çæ¥éæ¥å¿åè§£å³æ¹æ³å¦ä¸ï¼

<!--more-->


##### 2021.12.30

å½ä»¤ï¼
åå§å MySQL è¡¨ä¸­åºç¡æ°æ®ï¼

    docker-compose exec fpm php yii init/data

æ¥éï¼
```
Exception 'yii\db\Exception' with message 'SQLSTATE[HY000]:
General error: 1366 Incorrect string value: '\xE4\xBB\xA5\xE5\xA4\xAA...' for column 'name_cn' at row 1
The SQL being executed was: INSERT INTO `currency` (`symbol`, `name_cn`, `name_en`) VALUES ('eth', 'ä»¥å¤ªå', 'Ethereum')'
```

å¤çæ¹å¼ï¼
ä¿®æ¹æ¡æ¶æ°æ®åºå­ç¬¦é utf8->utf8mb4

##### 2021.12.31
widowsä¸å½ä»¤ï¼
```
winpty docker run --rm -it -v "C:\phpstudy_pro\WWW\chainadmin":/work
--network chainadmin_project python:3.9-slim-bullseye sh -c 'cd /work
&& pip install -r ./process_data/requirements.txt
&& python ./process_data/generate_mining_stats.py --mysql-host mysql --user root --passwd root --parse-date all'
```

æ¥éï¼
```
ERROR: Could not install packages due to an OSError: HTTPSConnectionPool(host='files.pythonhosted.org', port=443):
Max retries exceeded with url: /packages/a7/f6/84070ab117e6b080a87aac0ac9e4d269a66c6f6076ad81509bd0aac828d8/autopep8-1.5.7-py2.py3-none-any.whl
(Caused by ConnectTimeoutError(<pip._vendor.urllib3.connection.HTTPSConnection object at 0x7fd2819e9160>,
'Connection to files.pythonhosted.org timed out. (connect timeout=15)'))
```

å¤çæ¹å¼ï¼
```
winpty docker run --rm -it -v "C:\phpstudy_pro\WWW\chainadmin":/work
--network chainadmin_project python:3.9-slim-bullseye sh -c
'cd /work && pip install -r ./process_data/requirements.txt --index-url=https://mirrors.aliyun.com/pypi/simple/
&& python ./process_data/generate_mining_stats.py --mysql-host mysql --user root --passwd root --parse-date all'
```

å°ç»ï¼
åæ¢äºå½åæº

å½ä»¤ï¼

    php yii migrate

æ¥éï¼
```
Exception 'yii\db\Exception' with message 'SQLSTATE[HY000] [2002]
php_network_getaddresses: getaddrinfo failed: nodename nor servname provided, or not known'
in /Users/baozhigang/work/chainadmin/vendor/yiisoft/yii2/db/Connection.php:649
```

å¤çæ¹å¼ï¼
è¿å¥fpmç»ç«¯æ§è¡
