---
layout: post
title: git常见问题
tags: git
categories:
- [计算机科学, git]
---

# git常见问题

## permission denied (publickey)

换了新电脑之后git pull的时候提示permission denied (publickey)

![](http://s3k1abi6b.hd-bkt.clouddn.com//20231104001648.png)

这才想起来没配ssh，重新生成一下, 生成id_rsa和id_rsa.pub文件说明生成成功。

```shel
ssy-keygen -t rsa -C "youemail"
```

![](http://s3k1abi6b.hd-bkt.clouddn.com//20231104001757.png)

接下来打开id_ras.pub文件，将里面的内容全部粘贴到github新建的ssh里面即可

![](http://s3k1abi6b.hd-bkt.clouddn.com//20231104164727.png)

