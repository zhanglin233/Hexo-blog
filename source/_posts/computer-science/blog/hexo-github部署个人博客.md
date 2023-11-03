---
layout: post
title: 基于hexo框架部署个人博客
tags: 博客
sticky: true
categories:
- [计算机科学, 博客]
---

# 基于hexo框架部署个人博客

## 起因

之前我的博客是基于jekyll搭建的，但是jekyll模板搭建出来的博客的样式我并不是喜欢，所以在前天看到是室友打算用hexo搭建博客的时候，发现效果挺好的，所以转而把博客迁移到hexo上来。

## 准备工作

### git

因为最终是把生成的页面提交到github上的免费私有仓库，所以git环境是必不可少的,网上有关git安装教程很多，就不一一赘述，查询当前设备的git环境，在控制台输入git -v,得到git版本信息即说明git环境已装好。

```bash
git --version
```

![](http://s3k1abi6b.hd-bkt.clouddn.com/20211127143705.png)

### node

开发过程中需要安装较多依赖的插件，使用npm包管理工具来管理这些插件。具体安装步骤网上查询即可。

![](http://s3k1abi6b.hd-bkt.clouddn.com/20211127145223.png)

### hexo

如果电脑已经具备了git和node环境，安装hexo非常简单

```bash
npm install hexo -g
```

建议加上 -g 将hexo设为全局环境变量

查看hexo版本信息

![](http://s3k1abi6b.hd-bkt.clouddn.com/20211127145634.png)

能得到以上信息就说明安装成功。

## 开始本地部署

此次博客部署参考了https://shoka.lostyu.me/。

### 初始化文件夹

选择一个文件夹用来部署你的博客。我的文件夹所在地：

![](http://s3k1abi6b.hd-bkt.clouddn.com/20211127150222.png)

接下来在终端中进入你博客的文件路径，分别输入

```bash
hexo init

git init
```

进行初始化，使其能够运行hexo和git命令。（接下来所有操作都是在此路径下进行）

### 下载模板

```bash
# cd your-blog
git clone https://github.com/amehime/hexo-theme-shoka.git ./themes/shoka
```

### 安装依赖插件

#### 安装yarn

直接使用npm下载可能存在下载速率过慢的问题，可以使用yarn依赖插件。

```
npm install yarn
```

#### 安装项目所需依赖

```bash
# md 文件渲染器，压缩 css/js/html
yarn add hexo-renderer-multi-markdown-it -s 

# 给生成的 css 文件们添加浏览器前缀
yarn add hexo-autoprefixer -s

#站内搜索功能
yarn add hexo-algoliasearch -s

# 文章或站点字数及阅读时间统计	
yarn add hexo-symbols-count-time -s

# 生成 Feed 文件
yarn add hexo-feed -s

# 将生成页面提交到远程仓库所需的依赖
yarn add hexo-deployer-git -s
```

### 基本配置

基本配置为blog所在路径下的_config.yml文件,各个配置什么作用注释应该挺清楚的，就不一一解释，如还有不清楚的可以查看[原作者的博客](https://shoka.lostyu.me/computer-science/note/theme-shoka-doc/config/)。

```yaml
# Hexo Configuration
## Docs: http://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# Site
title: Hexo
subtitle: subtitle
description: 描述123
keywords: 关键词1,关键词2 # edit for Theme.shoka
author: John Doe
language: zh-CN # 这里只可以选 zh-CN、zh-HK、zh-TW、ja、en 这几个格式
timezone: 'Asia/Shanghai'

# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://yoursite.com
root: /
permalink: :title/ # edit for Theme.shoka
permalink_defaults:

# Directory
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:

# Writing
new_post_name: :title.md # File name of new posts
default_layout: post
titlecase: false # Transform title into titlecase
external_link:
  enable: true # Open external links in new tab
  field: site # Apply to the whole site
  exclude: ''
filename_case: 0
render_drafts: false
post_asset_folder: false
relative_link: false
future: true
highlight:
  enable: false # edit for Theme.shoka
  line_number: true
  auto_detect: true
  tab_replace: ''
prismjs:
  enable: false # edit for Theme.shoka

# Category & Tag
default_category: uncategorized
category_map: # edit for Theme.shoka
  计算机科学: computer-science
  Java: java
  二进制杂谈: note
  零基础学Java语言-浙江大学-翁恺: course-1
  Theme Shoka Documentation: theme-shoka-doc
tag_map:

# Date / Time format
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD
time_format: HH:mm:ss

# Pagination
## Set per_page to 0 to disable pagination
per_page: 10
pagination_dir: page

# Extensions
## Plugins: http://hexo.io/plugins/
## Themes: http://hexo.io/themes/
theme: shoka # edit for Theme.shoka

# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type:


# edit for Theme.shoka
autoprefixer:
  exclude:
    - '*.min.css'

markdown:
  render: # 渲染器设置
    html: false # 过滤 HTML 标签
    xhtmlOut: true # 使用 '/' 来闭合单标签 （比如 <br />）。
    breaks: true # 转换段落里的 '\n' 到 <br>。
    linkify: true # 将类似 URL 的文本自动转换为链接。
    typographer:
    quotes: '“”‘’'
  plugins: # markdown-it插件设置
    - plugin:
        name: markdown-it-toc-and-anchor
        enable: true
        options: # 文章目录以及锚点应用的class名称，shoka主题必须设置成这样
          tocClassName: 'toc'
          anchorClassName: 'anchor'
    - plugin:
        name: markdown-it-multimd-table
        enable: true
        options:
          multiline: true
          rowspan: true
          headerless: true
    - plugin:
        name: ./markdown-it-furigana
        enable: true
        options:
          fallbackParens: "()"
    - plugin:
        name: ./markdown-it-spoiler
        enable: true
        options:
          title: "你知道得太多了"

minify:
  html:
    enable: true
    stamp: false
    exclude:
      - '**/json.ejs'
      - '**/atom.ejs'
      - '**/rss.ejs'
  css:
    enable: true
    stamp: false
    exclude:
      - '**/*.min.css'
  js:
    enable: true
    stamp: false
    mangle:
      toplevel: true
    output:
    compress:
    exclude:
      - '**/*.min.js'

# algolia:
#   appId:
#   apiKey:
#   adminApiKey:
#   chunkSize: 5000
#   indexName:
#   fields:
#     - title #必须配置
#     - path #必须配置
#     - categories #推荐配置
#     - content:strip:truncate,0,4000
#     - gallery
#     - photos
#     - tags

feed:
    limit: 20
    order_by: "-date"
    tag_dir: false
    category_dir: false
    rss:
        enable: true
        template: "themes/shoka/layout/_alternate/rss.ejs"
        output: "rss.xml"
    atom:
        enable: true
        template: "themes/shoka/layout/_alternate/atom.ejs"
        output: "atom.xml"
    jsonFeed:
        enable: true
        template: "themes/shoka/layout/_alternate/json.ejs"
        output: "feed.json"

```



### 项目配置

项目配置文件为themea/shoka文件夹中的_config.yml文件

```yaml
alternate: Yume Shoka

# Assets
statics: / #//cdn.jsdelivr.net/gh/amehime/shoka@latest/

open_graph:
  #twitter_id:
  #google_plus:
  #fb_admins:
  #fb_app_id:

menu:
  home: / || home
  posts:
    default: / || feather
    archives: /archives/ || list-alt
    categories: /categories/ || th
    tags: /tags/ || tags
  friends: /friends/ || heart

# Social Links
# Usage: `Key: permalink || icon || color`
# Key is the link label showing to end users.
# Value before `||` delimiter is the target permalink,
# secend value is the name of Font icon.
social:
  github: https://github.com/amehime || github || "#191717"
  #google: https://plus.google.com/yourname || google
  twitter: https://twitter.com/amehime || twitter || "#00aff0"
  zhihu: https://www.zhihu.com/people/rurismzk || zhihu || "#1e88e5"
  music: https://music.163.com/#/user/home?id=12886823 || cloud-music || "#e60026"
  weibo: https://weibo.com/amehime || weibo || "#ea716e"
  about: https://about.me/amehime || address-card || "#3b5998"
  #email: mailto:yourname@mail.com || envelope || "#55acd5"
  #facebook: https://www.facebook.com/yourname || facebook
  #stackoverflow: https://stackoverflow.com/yourname || stack-overflow
  #youtube: https://youtube.com/yourname || youtube
  #instagram: https://instagram.com/yourname || instagram
  #skype: skype:yourname?call|chat || skype
  #douban: https://www.douban.com/people/yourname/ || douban

footer:
  # Specify the date when the site was setup. If not defined, current year will be used.
  since: 2010
  count: true

post:
  count: true


# ---------------------------------------------------------------
# Third Party Plugins & Services Settings
# ---------------------------------------------------------------

# Comments
# Valine
# For more information: https://valine.js.org, https://github.com/xCss/Valine
valine:
  appId: #这里不要忘了改
  appKey: #这里不要忘了改
  placeholder: ヽ(○´∀`)ﾉ♪ # Comment box placeholder
  pageSize: 10 # Pagination size
  lang: zh-CN
  tagMember:
    master:
      # - deea5a8d259d17182a53be1772e4c182
    friend:
      - deea5a8d259d17182a53be1772e4c182

# bgm
audio:
   - title: 列表1
      list:
        - https://music.163.com/#/playlist?id=2943811283
        - https://music.163.com/#/playlist?id=2297706586
   - title: 列表2
      list:
        - https://music.163.com/#/playlist?id=2031842656

# Dependencies: https://github.com/amehime/hexo-renderer-multi-markdown-it
pangu: true

# ---------------------------------------------------------------
# analytics & SEO Settings
# ---------------------------------------------------------------


# Disable Baidu transformation on mobile devices.
disable_baidu_transformation: true

# Automatically add external URL with Base64 encrypt & decrypt.
exturl: true

```

### 显示文章

需展示的文章应为markdown文件，放于yourBlog/source/_post目录下，如我的博客位于问价夹下D:\blog\myBlog2,则文章位于D:\blog\myBlog2\source\_posts问价夹下，其它细节配置参考[界面展示](https://shoka.lostyu.me/computer-science/note/theme-shoka-doc/display/)

### 本地启动项目

```bash
hexo s
```

启动项目即可

```bash
PS D:\blog\myBlog2> hexo s   
INFO  Validating config
INFO  Start processing
Prism's Diff Highlight plugin requires the Diff language definition (prism-diff.js).Make sure the language definition is loaded or use Prism's Autoloader plugin.
INFO  Hexo is running at http://localhost:4000 . Press Ctrl+C to stop.

```

出现以上信息即为启动成功，前往http://localhost:4000 即可查看效果。

## 将博客部署到github上

首先在根目录下配置一下信息

```yaml
# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
deploy:
  type: 'git'
  repo: # 你为你的博客所创建的仓库，建议仓库名为yourname.github.io,即仓库名为https://github/yourname/yourname.github.io
  branch: master # 项目所在的分支

```

依次运行以下的命令

```yaml
# 清楚本地缓存
hexo clean

# 重新生成并提交到远端仓库 
hexo g -d  # 相当于 hexo generate 及 hexo dep
```

如果没有发生错误你就可以在你的gihub仓库上看到提交的内容了

![](http://s3k1abi6b.hd-bkt.clouddn.com/20211127154035.png)

在浏览器中查看效果

## 遇到的坑

### 按照heox官方文档推荐的部署到github上失败

官方推荐[将 Hexo 部署到 GitHub Pages | Hexo](https://hexo.io/zh-cn/docs/github-pages)

本人惨痛的教训建议不要按照官方步骤来，官方的太麻烦了而且还有给的步骤还有问题，反正我是没弄出来。

官方的意思是使用 [Travis CI](https://github.com/marketplace/travis-ci)能够动态监听代码上传并将生成的页面文件提交到与项目代码文件不同的分支下。 

为了达到与官方类似的效果，我们只需要新建一个仓库用于存放整体项目文件（按照本文章的上述步骤，yourname.github.io仓库只会存放生成的页面文件，即运行hexo g命令后生成的publi文件夹之中的内容），并将该项目绑定到新建的仓库即可。

```bash
git remote add origin 你的仓库
```

以后要刷新博客内容只需运行

```bash
hexo cl
hexo g -d
```

将整个项目文件保存到github

```bash
git add . 
git commit -m ""
git pull 
git push
```



### ... is not a  a recognized Liquid tag

```bash
The tag `linksfile` on line 23 in `source/friends/index.md` is not a recognized Liquid tag. For more information, see https://docs.github.com/github/working-with-github-pages/troubleshooting-jekyll-build-errors-for-github-pages-sites#unknown-tag-error.
```

大概原因是Jekyll 是一个静态站点生成器，内置 GitHub Pages 支持和简化的构建过程，所以github官方建议将 Jekyll 用于 GitHub Pages。因此如果你使用的不是jekyll框架，就可能会存在标签无法识别的情况。为了解决这样的问题，我们只需要在配置文件中禁用jekyll即可。

在博客项目根目录下添加空的.nojekyll文件

![](http://s3k1abi6b.hd-bkt.clouddn.com/20211126221143.png)

并在_config.yml文件中加入以下配置即可：

```yaml
include:
  - .nojekyll

```

1. 第二个问题就是依据hexo官方文
### hexo d 时出现spawn failed

如果报的是类似认证远端仓库的问题的话，将

```yaml
# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
deploy:
  type: 'git'
  repo: # 你为你的博客所创建的仓库，建议仓库名为yourname.github.io,即仓库名为https://github/yourname/yourname.github.io
  branch: master # 项目所在的分支

```

中的repo换成ssh地址，不要使用https地址

![](http://s3k1abi6b.hd-bkt.clouddn.com/20211127155137.png)

```yaml
# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
deploy:
  type: 'git'
  repo: # 你为你的博客所创建的仓库，建议仓库名为yourname.github.io,即仓库名为git@github.com:github/yourname/yourname.github.io
  branch: master # 项目所在的分支
```

### hexo本地测试运行重启后页面空白,提示 : WARN No layout: index.html?

检查themes文件夹下的主题名字是否和项目根目录下_config.yml配置中的theme属性一样，如此次使用的主题为shoka，则项目根目录下_config.yml文件对应的部分为

```yaml
theme: shoka
```

### github上配置域名失效，每次提交之后都要重新在github仓库的配置里重新设置域名‘

在项目source文件夹下新增CNAME文件，在CNAME文件里写入你想要绑定的域名即可。
