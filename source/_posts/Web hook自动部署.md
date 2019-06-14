---
title: "Web hook自动部署"
catalog: true
date: 2019-06-13 22:10:00
subtitle: "This is about Web hook."
header-img: "Demo.png"
tags:
- Web hook
- Hexo
catagories:
- Hexo
---

之前写博客的时候都直接在Linux云服务器上写，平时倒是没有什么问题，但是因为我使用了国外的服务器，导致延时时高时低，有的时候连一个ssh都连不上。无奈之下开始探寻新的方式。后来发现将云服务器和GitHub关联起来，实现本地修改，远端更新。



方式其实是有很多种，我选择的这一种也算是比较简单的



### 具体步骤

1. 在GitHub创建仓库，具体步骤不详细说。
2. 在远程Linux服务器相应文件夹建立和GitHub的关联，并且git push
3. 在本地电脑git clone相应仓库，更改文件后git push
4. **重点：在GitHub上选择仓库->设置->Webhook，选择相应链接**
5. **在远程服务器安装webhook软件，并且后台启用服务**
6. **编辑触发事件的脚本，此脚本会在github发送webhook后执行，此时可以在脚本中更新nginx中的静态资源**