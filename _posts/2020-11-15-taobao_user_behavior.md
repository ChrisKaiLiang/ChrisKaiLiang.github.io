---
layout: post
title: "淘宝用户行为分析"
subtitle: "基于阿里云天池-淘宝用户行为数据集"
date: 2020-11-15 09:30:00 -0100
background: '/img/posts/07.jpg'
permalink: '/taobao-user-behavior'
---

# 淘宝用户行为分析

TODO: upload analysis  
**项目代码-GitHub**


## 1 - 项目简介  

### 1.1 项目背景

UserBehavior是阿里巴巴提供的一个淘宝用户行为数据集，包含了2017年11月25日至2017年12月3日之间，有行为的约一百万随机用户的所有行为（行为包括点击、购买、加购、喜欢）。数据集的组织形式和MovieLens-20M类似，即数据集的每一行表示一条用户行为，由用户ID、商品ID、商品类目ID、行为类型和时间戳组成，并以逗号分隔。

数据集地址：[User Behavior Data from Taobao](https://tianchi.aliyun.com/dataset/dataDetail?dataId=649) 

关于数据集中每一列的详细描述如下：

| 列名 | 中文列名 | 说明 |
| --- | ------- | --- |
| user_id       |用户ID    |整数类型，序列化后的用户ID|
| item_id       |商品ID    |整数类型，序列化后的商品ID|
| category_id   |商品类目ID|整数类型，序列化后的商品所属类目ID|
| behavior_type |行为类型  |字符串，枚举类型，包括('pv', 'buy', 'cart', 'fav')|
| timestamp     |时间戳    | 行为发生的时间戳|

用户行为类型共有四种，它们分别是：

|行为类型|说明|
|------|----|
|pv  | 商品详情页pv，等价于点击|
|buy | 商品购买|
|cart| 将商品加入购物车|
|fav | 收藏商品|

<br/>
### 1.2 分析目标

- 流量指标
- 用户消费频次
- 用户行为在时间维度的分布
- 用户行为转化漏斗
- 用户留存率
- 商品销量
- RFM






<span>Cover Photo by <a href="https://unsplash.com/@glenncarstenspeters?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Glenn Carstens-Peters</a> on <a href="https://unsplash.com/s/photos/computer?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Unsplash</a></span>