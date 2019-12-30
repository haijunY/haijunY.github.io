---
title: 背景图片
date: 2018-11-12 15:07:00
categories: 建站总结
tags: 
---

打开文件
hexo\themes\next\source\css\_custom\custom.styl
添加如下代码
```css
body {
    background:url(https://source.unsplash.com/random/1600x900);
    background-repeat: no-repeat;
    background-attachment:fixed;
    background-position:50% 50%;
}

.main-inner { 
    margin-top: 60px;
    padding: 60px 60px 60px 60px;
    background: #fff;
    opacity: 0.8;
    min-height: 500px;
}
```
