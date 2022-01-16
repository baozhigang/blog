---
title: Homebrew的安装卸载问题
date: 2022-1-16
categories:
- Homebrew
tags:
- Homebrew
- PHP
---

🪜 记录一些自己在软件管理方面的过失，希望吸取教训，不再出现类似的失误。我把这个过程分成四个阶段，详细记录分析。


##### 阶段一

Mac系统升级后，取消了自带PHP，我们只能自己安装。安装最好的办法是使用Homebrew安装，方便升级和卸载（当然，这是事后才明白的，🐶）。

<!--more-->

当我由于某个原因想卸载PHP的时候，记不清是怎么安装的了，匆忙之下，找了个帖子删除本地PHP。结果多删除了文件，导致brew安装PHP的时候安装失败：

    brew install php@7.4
    Error: No such file or directory - /usr/local/var/homebrew/linked/php@7.4

小结：
- 软件的管理一定要交给管理工具去处理，如mac的Homebrew。
   安装 brew install php@7.4
   卸载 brew uninstall php@7.4
   就解决了。
- 删除文件要慎重，不清楚的话先改名，新安装成功后再删除旧的文件。

##### 阶段二

我意识到电脑上的Homebrew文件缺失了，查下资料只能卸载重新安装。吸取教训，我到官网找到教程，

> https://github.com/homebrew/install#uninstall-homebrew

执行命令：
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/uninstall.sh)"
curl: (7) Failed to connect to raw.githubusercontent.com port 443: Connection refused
Failed to fetch Homebrew .gitignore!
```
请求失败，需要梯子
```
/bin/bash -c "$(curl -x socks5h://x.x.x.x:x -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/uninstall.sh)"
curl: (7) Failed to connect to raw.githubusercontent.com port 443: Connection refused
Failed to fetch Homebrew .gitignore!
```
我要崩溃了。。。
卸载脚本里面发起请求不成功，为什么代理还不行？我只能调整方向，找开源镜像。

##### 阶段三

找到清华大学的开源镜像，添加环境变量到本地：
```
export HOMEBREW_BREW_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git"
export HOMEBREW_CORE_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git"
export HOMEBREW_BOTTLE_DOMAIN="https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles"
```

下载清华的安装脚本，卸载Homebrew
```
git clone --depth=1 https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/install.git brew-install
/bin/bash brew-install/uninstall.sh
```
报错：
```
curl: (7) Failed to connect to raw.githubusercontent.com port 443: Connection refused
Failed to fetch Homebrew .gitignore!
```
说明本地脚本发起请求被拦截了，uninstall.sh中找到代码，加梯子：
```
-gitignore="$(curl -fsSL https://raw.githubusercontent.com/Homebrew/brew/HEAD/.gitignore)"
+gitignore="$(curl -x socks5h://x.x.x.x:x -fsSL https://raw.githubusercontent.com/Homebrew/brew/HEAD/.gitignore)"
```
成功了。。。
我靠，很激动，导致我没记录下那行代码。
然后根据提示删除脚本没能删除的文件。

##### 步骤四

安装Homebrew。有两种方式，一种是直接镜像安装
```
/bin/bash brew-install/install.sh
```
另一种是官方安装脚本，我选择官方，当然要加梯子：

```
/bin/bash -c "$(curl -x socks5h://x.x.x.x:x -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

==> Checking for `sudo` access (which may request your password)...
Password:
==> This script will install:
/usr/local/bin/brew
/usr/local/share/doc/homebrew
/usr/local/share/man/man1/brew.1
/usr/local/share/zsh/site-functions/_brew
/usr/local/etc/bash_completion.d/brew
/usr/local/Homebrew
==> The following new directories will be created:
/usr/local/Cellar
/usr/local/Caskroom
==> HOMEBREW_BREW_GIT_REMOTE is set to a non-default URL:
https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git will be used as the Homebrew/brew Git remote.
==> HOMEBREW_CORE_GIT_REMOTE is set to a non-default URL:
https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git will be used as the Homebrew/homebrew-core Git remote.
```
成功了，哈哈。
远程脚本检测到本地设置了环境变量，直接启用了清华的软件源。

然后安装PHP
```
brew install php@7.4
HOMEBREW_BREW_GIT_REMOTE set: using https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git for Homebrew/brew Git remote.
HOMEBREW_CORE_GIT_REMOTE set: using https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git for Homebrew/core Git remote.
Running `brew update --preinstall`...
Warning: php@7.4 has been deprecated because it is a versioned formula!
==> Downloading https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/apr-1.7.0_2.monterey.bottle.tar.gz
######################################################################## 100.0%
..................

```
完美。

##### 写个总结吧

1. 软件管理要交给管理工具去做啊，省心省力。所以要好好对她: Homebrew。一定研究透。
2. 网络请求怎么用梯子🪜，是自己欠缺的地方。好好研究网络请求，和代理。
3. 要用官方文档，不要用别人随意写的帖子，切记切记！
