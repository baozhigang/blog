---
title: Composer自定义包设置及使用
date: 2022-06-04
categories:
- Composer
tags:
- Composer
---

🏣 代码复用是工作中非常重要的一项内容，把各个项目复用性比较高的一些类库提取出来，放到公共的仓库中，做成composer自定义package。不同的项目使用时直接引入使用，简直不要太爽，具体实现步骤如下：

<!--more-->

1. 创建自定义composer包目录，如：`composer-private`，`cd composer-private`
2. 创建文件 `composer.json`，必须添加命名空间映射，如下：

    ```json
    {
        "name": "private-package/test",
        "description": "A private package test",
        "version": "1.0.0",
        "autoload": {
            "psr-4": {
                "Privatepackage\\": "src/"
            }
        },
        "require": {
            "php": ">=7.4"
        }
    }
    ```

3. 创建src目录，里面添加公共类库，类库必须声明命名空间

4. 提交到git仓库到远程，github或者gitlab，如
   [示例代码](https://github.com/baozhigang/composer-private)

5. 具体项目使用自己的composer包时，composer.json 必须配置
    `"minimum-stability": "dev",`
    默认 stable 不支持自定义package

6. `composer.json` 添加仓库并引入自定义包

    ```json
    "repositories": [
        { "type": "vcs", "url": "https://github.com/baozhigang/composer-private" }
    ],
    ```

7. 引入自定义的composer包
    `composer require private-package/test`

8. 就可以跟其他官方包一样正常使用了
