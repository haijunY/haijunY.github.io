---
title: 2、Hexo博客的阅读统计和评论功能
date: 2018-11-12 15:07:00
categories: 建站总结
tags: 
---

hexo的nexT框架支持阅读统计和评论的功能，我们需要用到leancloud账号，将统计数据和评论内容存储到leancloud上，具体步骤如下
### leancloud
送上传送门[leancloud](https://leancloud.cn/)
1.首先需要创建一个应用，我们可以命名为mblog，选择开发版
<img src="../images/Station-HexoReadingCountAndComment-1.png">
2.然后创建class实例
创建Counter，设置数据条目的默认为无限制

<img src="../images/Station-HexoReadingCountAndComment-2.png">

Counter实例存储阅读统计信息，Comment实例存储评论功能，Comment实例在配置好nexT后会自动生成
3.在安全中心添加你的博客域名
<img src="../images/Station-HexoReadingCountAndComment-3.png">

4.记住AppId和AppKey
<img src="../images/Station-HexoReadingCountAndComment-4.png">

### 配置next的_config.yml
```
# Valine.
# You can get your appid and appkey from https://leancloud.cn
# more info please open https://valine.js.org
valine:
  enable: true
  appid:  填自己的appid
  appkey:  填自己的appkey
  notify: false # mail notifier , https://github.com/xCss/Valine/wiki
  verify: false # Verification code
  placeholder: 欢迎留言 # comment box placeholder
  avatar: source/images/headico.jpg # gravatar style
  guest_info: nick,mail,link # custom comment header
  pageSize: 10 # pagination size
```

```
# Show number of visitors to each article.
# You can visit https://leancloud.cn get AppID and AppKey.
leancloud_visitors:
  enable: true
  appid:  填自己的appid
  appkey:  填自己的appkey
```

### 重新发布博客
效果如下
阅读统计
<img src="../images/Station-HexoReadingCountAndComment-5.png">
评论功能
<img src="../images/Station-HexoReadingCountAndComment-6.png">
