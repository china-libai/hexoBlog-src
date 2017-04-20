---
title: The begin of blog
---
浑浑噩噩这么多年，一直没能养成写博客的习惯。如今是时候开始积累自己。
这篇就算是一个开端，主要记录下hexo该如何使用。

## 搭建hexo静态博客
Hexo官方文档 :: [https://hexo.io/zh-cn/docs](https://hexo.io/zh-cn/docs)
### 安装

``` bash
$ wget -C https://nodejs.org/dist/v6.10.2/node-v6.10.2.pkg      # 下载Node.js安装包
$ open node-v6.10.2.pkg      # 傻瓜式安装
$ brew install git           # 安装git
$ npm install -g hexo-cli    # 安全hexo
$ hexo init <folder>         # 从hexo官网下载初始化文件
$ cd <folder>                
$ npm install                # 安装站点文件

$ hexo server                # 启动服务器。默认情况下，访问网址为： http://localhost:4000/

$ hexo new [layout] <title>  # 新建一个post
$ hexo generate              # 生成静态文件
```

### 使用

``` bash
$ hexo new [layout] <title>    # layout默认为 post，可以在 _config.yml 的 default_layout 参数指定。
                               # Hexo 有三种默认布局：post、page 和 draft。

$ hexo new draft <title0>       # 新建一份草稿
$ hexo publish [layout] <title0>   # 将draft移动到post
$ hexo generate                 # 生成静态文件
```

### 部署

``` bash
$ npm install hexo-deployer-git --save      # 安装hexo-deployer-git以实现git部署

$ vim _config.yml
--------------[file content]
    deploy:
      type: git
      repo: <repository url>    # 库（Repository）地址
      branch: [branch]          # 分支名称。如果您使用的是 GitHub 或 GitCafe 的话，程序会尝试自动检测。
      message: [message]        # 自定义提交信息 (默认为 Site updated: {{ now('YYYY-MM-DD HH:mm:ss') }})
--------------[file content]
$ hexo deploy     # 部署
```

## Github 配置
### 新建用于存放pages的repo
在GitHub上建一个repository（库），任意命名。
如果是没有个人域名，最好以 [github账号].github.io 命名，后续这个二级域名便指向这个库的GitHub pages。
如果有个人域名，可以任意命名，在setting中可以自行设置域名。然后将个人域名DNS解析指向GitHub.io的IP(192.30.252.153,192.30.252.154)。

### 新建SSH Keys
我使用SSH连接GitHub。 [官方文档](https://help.github.com/articles/connecting-to-github-with-ssh/)

``` bash
$ ls -al ~/.ssh     # 查看是否有 id_rsa 和 id_rsa.pub 文件
# 如果不存在或者想要生成新的密钥对，可使用下面命令
$ ssh-keygen -t rsa -b 4096 -C "user@oddboy.cn"
$ pbcopy < ~/.ssh/id_rsa.pub    # 很强势的一个命令，将文件内容直接复制到剪贴板。
```
在GitHub的Personal setting --> SSH and GPG keys点击“New SSH key”, 粘贴好公钥即可。

如此, hexo depoly就可以用了。

### 新建用于存放hexo博客源码的repo
hexo deploy上传的只是生成的静态页面，博客的原始数据仍然存在于本地。为了安全起见，我们需要另外建一个库，用于博客源码。

``` bash
$ git init    # git本地目录初始化
$ git remote add origin git@github.com:odboy/hexoBlog-src.git   # 配置远程git库
$ git add .   
$ git commit -m "comment信息"
$ git status      # 查看分支情况
$ git push -u origin master   # 推送到master分支
```