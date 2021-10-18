# Hugo+GithubPages搭建博客教程


之前用 `typecho`  搭的博客需要使用服务器，续不起阿里云服务器了（留下了贫穷的泪水，于是发现了好用的 `Hugo` ~

# 1.Hugo初探

Hugo 是由 Go 语言实现的静态网站生成器。简单、易用、高效、易扩展、快速部署！

## 1.1安装Hugo

首先我们需要下载 `Hugo` 和 `git`，前往[Hugo中文文档](https://gohugo.org)，推荐使用二进制安装，点击[Hugo Releases](https://github.com/gohugoio/hugo/releases)进入github页面，选择对应版本下载

下载完成后解压到本地合适的文件夹，假设我们解压到 `D:\myblog\bin`，将此路径添加到系统环境变量中，打开终端输入 `hugo version` 检查hugo版本，查询成功则表示安装成功

```shell
hugo version
```

## 1.2生成站点

选择合适的文件夹建站，使用 `Hugo` 快速生成站点，进入博客根目录输入（.代表当前目录）

```shell
hugo new site demosite # 命令格式，hugo new site <项目名称>
# or
hugo new site .
```

进入根目录，站点目录结构如下：

```shell
├── archetypes
│   └── default.md
├── content
├── layouts
├── data
├── resources
├── static
├── themes	# 主题文件夹
└── config.coml  # 配置文件
```

## 1.3主题选择

前往[Hugo主题](https://themes.gohugo.io/)选择心仪的主题，找到对应 Github 仓库，下载zip或直接使用 `git clone`

```shell
cd themes
git clone https://github.com/xxx.git
```

> 若主题文件夹含有 `-master` 后缀，尽量将其删除，以免后期引起奇奇怪怪的BUG，主题的不同也可能需要在初期配置不同的文件，本文主要面向Lovelt主题

**重要！！！**将主题的 `config.coml` 文件**替换**根目录下的同名文件，此文件是整个站点主题的配置文件，可根据自己的需求调整

## 1.4创建文章

让我们创建第一篇文章！

```shell
hugo new posts/demo.md
```

## 1.5启动服务

启动 hugo 服务器，进入 `http://localhost:1313` 预览页面

```shell
hugo server
```

# 2.Lovelt主题配置记录

本部分Lovelt主题~~踩坑~~配置记录

## 2.1初始配置

在最开始配置前需要修改一下主题路径 `themesDir 配置`，将其注释掉

```toml
baseURL = "https://yourname.github.io"
# [en, zh-cn, fr, pl, ...] determines default content language
# [en, zh-cn, fr, pl, ...] 设置默认的语言
defaultContentLanguage = "zh-cn"
# theme
# 主题
theme = "LoveIt"
# themes directory
# 主题目录
themesDir = "../.." # 将其注释
# website title
# 网站标题
title = ""
```

## 2.2时间设置

最开始我创建博客在设置时间时，时间变成了 `0001-01-01` 。Hugo 在生成静态页面的时候，不会生成超过当前时间的文章；而 Hugo 默认采用的是 [格林尼治平时](https://zh.wikipedia.org/wiki/格林尼治標準時間) (GMT)，比北京时间 (UTC+8) 晚了 8 个小时。也就是说，当北京时间在 08:00 之前，而你又将文章发布日期设在当天时，Hugo 就默认不会生成这个页面，可以在文章开头使用 `timezone` 指定时区：

```toml
---
...
date: 2019-05-12
timezone: UTC+8
...
---
```

其他配置可参考 `config.toml` 进行个性化配置

# 3.GithubPages和个人域名

## 3.1GithubPages建站

`GitHubPages`  是一项静态站点托管服务，它直接从 GitHub 上的仓库获取 index.html、HTML、CSS 和 JavaScript 文件，也可以通过构建过程运行文件，然后发布网站。我们使用  `GitHubPages` 搭建个人网站

（1）创建一个新的 Github 项目，项目名称需要是 `<username.github.io>`格式，最好先**不要**选择创建 `README.md` 文件

（2）生成静态页面之前需要修改 `config.toml` 文件中的 `baseURL` 配置，将其修改为个人站点，即`https://yourname.github.io`

```toml
# config.toml
baseURL = "https://yourname.github.io" 
title = ""
theme = ""
```

（3）若要将博客托管在 Github 上，需要上传静态页面，在根目录下输入 `hugo` 可以生成静态页面

```shell
hugo
```

> 输入后我们发下根目录下多了一个 `public` 目录，所有静态页面都会生成到 `public` 目录

（4）上传项目，静态页面生成完成后，便可以将整个静态页面以及本项目其他文件推送到 Github 项目中

```shell
cd public
git init
git remote add origin https://github.com/yourname/xxx.github.io.git
git add .
git commit -m "first commit"
git push -u origin master
```

（5）浏览器访问 `http://yourname.github.io/`，你的博客已经超成功部署

## 3.2配置个人域名

在配置个人域名之前，我们需要一些前置知识（以阿里云为例）

- 域名，简单来说就是访问博客的链接，我们需要购买自己的域名。可以通过阿里云选择[域名](https://wanwang.aliyun.com/?spm=5176.12825654.eofdhaal5.9.3dbd2c4aYvnkSD)
- 在阿里云购买域名过程中需要实名认证，实名认证后需要进行备案，可以通过阿里云APP方便操作。
- DNS解析，简单来说就是解析域名和IP的对应关系。具体可参考[阿里云域名购买与DNS解析教程](https://blog.csdn.net/shaock2018/article/details/91358968)。在cmd中输入 `ping +域名  `，丢包率为0则解析成功~

（1）当你拥有域名后，首先是用 `ping ` 命令找到存放你的 `GitHubPages`  的主机的IP地址，在终端里面用命令 `ping yourname.github.io` 便可完成，回复地址即我们要找的IP地址

（2）在购买域名的提供商为域名添加解析，添加两条记录

![dnsd836d52d9ddab03f.png](https://s1.imagehub.cc/images/2021/10/17/dnsd836d52d9ddab03f.png)

- 记录类型：CNAME 将一个域名指向另一个域名，需要添加 CNAME 记录（`yourname.github.io`）。 主机记录：www 表示访问域名的时候以 www 开头为一级域名。如果是二级域名的话就在前面加上自己想要的参数，访问的时候也是以二级域名的形式访问
- 记录类型：A 将域名指向一个IPv4地址，如果需要将域名指向一个 IP 地址（刚才我们 `ping` 获得的IP地址），就需要添加 A 记录。 主机记录：@ 表示访问的时候直接访问，前面不加任何参数

 （3）在 Github 仓库根目录中创建 `CNAME ` 文件，内容是你的域名（不要加http头），同步远程库同步到本地库。

```shell
git pull --rebase origin master
```

（4）打开仓库 `Repository Settings` 的 `Pages` ，我们发现 `Custom domain` 变为我们的域名，若没有自行更改即可，选择 `Enforce HTTPS` 为你的网站**添加小绿锁**，配置后需要等一会才可以生效~

<img src="https://s1.imagehub.cc/images/2021/10/17/Custom_domain.png" alt="Custom_domain.png" style="zoom: 50%;" />

（5） 设置完成后就可以通过你的域名访问部署在GitHub上的Hugo的网站啦



































