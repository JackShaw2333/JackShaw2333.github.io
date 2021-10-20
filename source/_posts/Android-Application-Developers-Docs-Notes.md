---
title: Android Application Developers Docs Notes
date: 2020-03-13 21:30:53
categories: [Andriod]
tags:
- Android
---

## Introduction

阅读[开发者文档](https://developer.android.com/guide)后的要点总结。

## [基础知识](https://developer.android.com/guide/components/fundamentals)

### [应用组件](https://developer.android.com/guide/components/fundamentals#Components)

应用组件是 Android 应用的基本构建块。每个组件都是一个入口点，系统或用户可通过该入口点进入您的应用。有些组件会依赖于其他组件。

4种类型

1. Activity
2. 服务
3. 广播接收器
4. 内容提供程序

#### Activity

拥有单个界面

#### 服务

在后台保持运行

#### 广播接收器

在常规用户流之外项别的app传递信息，如设定闹钟的功能

#### 内容提供程序

内容提供程序管理一组共享的应用数据，您可以将这些数据存储在文件系统、SQLite 数据库、网络中或者您的应用可访问的任何其他持久化存储位置。其他应用可通过内容提供程序查询或修改数据。
