---
title: snychronized
date: 2018-07-28 15:07:00
categories: 高并发
tags: [锁]
---
同步关键字snychronized用于锁定对应的代码，类似于一把锁一把钥匙，当代码并发执行的时候，最先执行的线程先拿到钥匙，打开锁开始执行代码，执行完成以后归还钥匙，然后等待下一个线程获取执行，这种方式保证了代码的安全性，使得代码执行的顺序是线性的，而不会造成数据错误
比如对金额的操作
```java
    /**
     * 添加synchronized关键字，以防止金额错误
     */
    @Override
    public synchronized void addPrice(long id, BigDecimal price){
        User user = userMapper.selectByPrimaryKey(id);
        BigDecimal socialPriceAmount = user.getSocialPriceAmount();
        User userN = new User();
        userN.setId(id);
        userN.setSocialPriceAmount(socialPriceAmount.add(price));
        userMapper.updateByPrimaryKeySelective(userN);
    }
```
假设有并发addPrice()的情况，通过snychronized可以保证最终结果的正确性，如果不加，则有可能在查询金额到执行更新操作这段时间内，会发生金额被当前方法的其他线程更新的情况，这样当本次线程最终提交的时候数据就会将其覆盖，则最终得到的是错误数据