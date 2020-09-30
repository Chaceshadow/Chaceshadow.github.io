---
title: IIS PUT漏洞复现（MOVE方法 207 Multi-Status错误解决）
categories:
  - 漏洞复现
tags:
  - 漏洞
  - IIS
  - 漏洞复现
  - PUT
  - MOVE
author: Chace
date: 

---

## 漏洞介绍

WebDAV（Web-based Distributed Authoring and Versioning） 是一种HTTP1.1的扩展协议。它扩展了HTTP 1.1，在GET、POST、HEAD等几个HTTP标准方法以外添加了一些新的方法，使应用程序可对Web Server直接读写，并支持写文件锁定(Locking)及解锁(Unlock)，还可以支持文件的版本控制。这样就可以像操作本地文件夹一样操作服务器上的文件夹。当然该扩展也存在缺陷，可以被恶意攻击者利用，直接上传恶意文件。

<!--more-->

## 漏洞复现

靶机环境：windows server2003 +IIS6 (192.168.1.99)

首先在靶机开启的IIS服务器，使用IISPutScanner工具扫描，发现put方法未被允许。

![](1.png)

我们将web服务扩展中的webDAV打开

![](2.png)

完成之后用工具先验证一下服务器当前是否允许PUT方法

![](3.png)



可以看到PUT方法已经被允许了，然后我们需要复现input写入shell，所以我们在目标网站的属性中勾选“写入”权限。

![](4.png)

添加写入权限之后，那么就用IIS写权限利用工具来进一步利用，先通过options方法探查一下服务器支持哪些http方法

![](5.png)

可以看到是支持put方法的，下面我们选择PUT方法上传一个asp的shell（asp.asp），保存为test.asp

![](6.png)

这里注意我们直接上传asp文件会提示失败，我们可以通过先上传txt文件的方式绕过

![](7.png)

这里可以用MOVE方法将刚刚上传的txt文件修改为asp文件，从而将文本文件变成可执行的脚本文件。MOVE协议不会更改文件内容。

![](8.png)

发现无法成功修改，我们可以利用IIS目录解析漏洞在文件后面加上;.txt或者;.jpg来绕过上传。（其实PUT方法也可以直接使用这种方式直接绕过上传，这里MOVE方法不知道为啥提示403错误）

![](9.png)

我们看到shell文件成功写入，使用菜刀链接shell（http://192.168.1.99/shell.asp;.txt） 成功。

![](10.png)

MOVE方法没有成功各种不爽，然后查资料发现网站属性里面的主目录下除了要勾选“写入”选框之外还要勾选“脚本资源访问”

![](11.png)

然后使用MOVE方法成功修改txt为shell文件aa.asp

![](12.png)

## 修复建议

通过整个漏洞复现过程我们也可以知道这个IIS PUT漏洞完全是因为管理员的一些不当配置导致的，所以想要修复只需要做到以下两点即可

1. 关闭WebDAV

2. 关闭写入权限