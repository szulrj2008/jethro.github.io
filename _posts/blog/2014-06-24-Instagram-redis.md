---
layout: post
title: 节约内存：Instagram的Redis实践
description: 优化无止境，只要肯琢磨。
category: blog
---

Instagram可以说是网拍App的始祖级应用，也是当前最火热的拍照App之一，Instagram的照片数量已经达到3亿，
而在Instagram里，我们需要知道每一张照片的作者是谁，下面就是Instagram团队如何使用Redis来解决这个问题
并进行内存优化的。

首先，这个通过图片ID反查用户UID的应用有以下几点需求：

##查询速度要足够快
数据要能全部放到内存里，最好是一台EC2的 high-memory 机型就能存储（17GB或者34GB的，68GB的太浪费了）
要合适Instagram现有的架构（Instagram对Redis有一定的使用经验，比如这个应用）
支持持久化，这样在服务器重启后不需要再预热
Instagram的开发者首先否定了数据库存储的方案，他们保持了KISS原则（Keep It Simple and Stupid），因为
这个应用根本用不到数据库的update功能，事务功能和关联查询等等牛X功能，所以不必为这些用不到的功能去选
择维护一个数据库。

于是他们选择了Redis，Redis是一个支持持久化的内存数据库，所有的数据都被存储在内存中（忘掉VM吧），而
最简单的实现就是使用Redis的String结构来做一个key-value存储就行了。像这样：

    SET     media:1155315 939     
    GET     media:1155315
    >939

[Jethro]:    http://blog.nigie1989.tk  "Jethro"
[Instagram]: http://instagram.com/