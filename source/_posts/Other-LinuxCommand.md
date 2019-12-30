---
title: Linux常用命令
categories: 其它
---
##查看日志
```
tail -100f xx.log 从倒数100行开始监视日志
grep '关键字' --col -A30 xx.log 查找关键字显示成红色及后续30行,-C30表示之前30行
less xx.log 查看日志
tail -100f xx.log | grep '关键字' -A30 监控关键字产生的日志,多打30行

```