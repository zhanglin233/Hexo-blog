---
title: 基于hexo框架部署个人博客
tags: 博客
categories:
- [计算机科学, 博客]
---

# 基于hexo框架部署个人博客

## 起因

## 准备工作

### git

### node

### hexo

## 遇到的坑

1. 首先遇到的第一个问题是

   ```bash
   The tag `linksfile` on line 23 in `source/friends/index.md` is not a recognized Liquid tag. For more information, see https://docs.github.com/github/working-with-github-pages/troubleshooting-jekyll-build-errors-for-github-pages-sites#unknown-tag-error.
   ```

   大概原因是Jekyll 是一个静态站点生成器，内置 GitHub Pages 支持和简化的构建过程，官方建议将 Jekyll 用于 GitHub Pages。因此如果使用的不是jekyll框架，就可能会存在标签无法识别的情况。为了解决这样的问题，我们只需要在配置文件中禁用jekyll即可

   在博客项目根目录下添加空的.nojekyll文件

   ![](https://gitee.com/nobody_heard_of_it/pic-md1/raw/master/image/20211126221143.png)

   并在_config.yml文件中加入以下配置即可：

   ```yaml
   include:
     - .nojekyll
   
   ```

2. 第二个问题就是依据hexo官方文档

3. spawn failed

4. deploy :https换ssh

5. RROR Deployer not found: git

