---
title: 通用软件测评之软件产品的信息安全性
categories:
  - 软件测评
tags:
  - 信息安全性
  - 通用软件测评
author: Chace
toc_number: true
date: 2020-09-28 10:55:50
---

## 软件测评标准概述

计算机软件是计算机应用的核心，其质量的好坏关系到计算机应用系统的成败，软件测评是提高软件质量的一个重要手段，目前我国软件开发、软件测评主要依照标准是《GBT 25000.51-2016系统与软件工程 系统与软件质量要求和评价(SQuaRE) 第51部分：就绪可用软件产品(RUSP)的质量要求和测试细则 》以及《GBT_25000.10-2016系统与软件工程 系统与软件质量要求和评价(SQuaRE) 第10部分：系统与软件质量模型》，这两个标准是对现行标准GB/T25000.51-2010以及GB/T16260.1-2006的修订。

<!--more-->

新标准最大的改变是将软件产品的六个质量特性扩充为八个特性（功能性、性能效率、兼容性、易用性、可靠性、信息安全性、维护性、可移植性），新标准增加了信息安全性和兼容性这两大特性，本文主要针对软件的信息安全性质量特性要求进行解读。

## 信息安全性定义

软件产品质量的信息安全性是指产品或者系统保护信息和数据的程度，以使用户、其他产品或系统具有与其授权类型和授权级别一致的数据访问度。

在《GBT 25000.51-2016系统与软件工程 系统与软件质量要求和评价(SQuaRE) 第51部分：就绪可用软件产品(RUSP)的质量要求和测试细则》信息安全性要求为：

软件应按照用户文档集中定义的信息安全性特征来运行。
软件应能防止对程序和数据的未授权访问(不管是无意的还是故意的)。
软件应能识别出对结构数据库或文件完整性产生损害的事件，且能阻止该事件，并通报给授权用户。
软件应能按照信息安全要求，对访问权限进行管理。
软件应能对保密数据进行保护，只允许授权用户访问。

## 信息安全性解析

信息安全性一般划分为以下6个子特性：保密性、完整性、抗抵赖性、可核查性、真实性、信息安全性的依从性。我们依据《GBT_25000.10-2016系统与软件工程 系统与软件质量要求和评价(SQuaRE) 第10部分：系统与软件质量模型》中的要求对其各个子特性逐一进行分析

### 保密性

**要求：**产品或系统确保数据只有在被授权时才能被访问的程度。

**测试内容：**

1. 验证软件产品是否具有对系统正常访问的控制能力依据安全策略和用户角色没置访问控制矩阵，用户权限应遵循“最小权限原则”

2. 测试系统是否进行用户身份鉴别，并在每次用户登录系统时进行鉴别。

3. 测试软件鉴别信息是否为不可见，且具有相应的抗攻击能力，并在存储或传输时用加密方法/具有相同安全强度的其他方法进行安全保护

4. 验证数据在传输过程中不被窃听，对整个通信过程中的整个报文或会话过程进行加密，如采用3DES(三重数据加密算法)、AES(高级加密标准)和IDEA(国际数据加密算法)等加密处理。

5. 明确区分系统中不同用户权限，系统不会因用户的权限的改变造成混乱。

6. 合适的身份认证方式，用户登密码是否是可见、可复制，密码的存储和传输安全，密码策略保证密码安全

7. 测试系统是否对不成功的鉴别尝试的值(包括尝试次数和时间的阈值)进行预先定义，井明确规定达到该值时是否采取了具有规范性和安全性的措施来实现鉴别失败的处理。

8. 软件应能防止对程序和数据的未授权访何(不管是无意的还是故意的).

**测试用例参考：**

> *明确区分系统中不同的用户权限*
>
> *系统不会因用户的权限的改变造成混乱，把原来高权限用户改为低权限用户，登录后系统是否异常*
>
> *系统中存在用户登录鉴别模块，并且每次登录需要鉴别*
>
> *是否存在登录验证码，登录失败次数限制等抗攻击能力*
>
> *用户登录密码是否可见、可复制，密码是否加密存储和传输*
>
> *网站是否为https协议传输*
>
> *是否可以通过绝对路径登录系统（访问用户登录后的链接直接进入系统）*



### 完整性

**要求：**系统、产品或组件防止未授权访问、篡改计算机程序或数据的程度。

**测试内容：**

1. 验证对软件产品是杏具有对未授权用户非法访间的控制能力
2. 采用专业测试工具开展“渗透测试”、“漏洞扫描”等手段，在模拟非法入侵攻击事件的条件下，验证软件产品是否有控制和处理能力
3. 验证软件产品对非授权人创建、删除或修改信息是否有控制处理能力
4. 软件应能识别出对结构数据库或文件完整性产生损害的事件，且能阻止该事件，并通报给授权人
5. 系统数据的完整性、可管理性可备份和可恢复能力，数据传输、数据使用、数据存储的完整性

**测试用例参考：**

> *未授权访问，低权限用户访问高权限用户数据*
>
> *未授权修改，无修改权限用户修改系统数据（包括创建、删除、修改）*
>
> *漏洞扫描或渗透测试中，系统是否存在告警机制、是否存在自动限制账号、IP访问等控制处理功能*
>
> *数据库、文件篡改、破坏是否具备防止和恢复功能*
>
> *漏洞扫描测试未发现存在已知漏洞*



### 抗抵赖性

**要求：**活动或事件发生后可以被证实且不可被否认的程度。

**测试内容：**

1. 测试软件是否具有在请求的情况下为数据原发者提供数据原发证据的功能:
2. 测试软件是否具有在请求的情况下为数据接收者提供数据接收证据的功能:
3. 测试软件是否使用数字证书等方式保证用户的身份认证，在收到请求的情况下为数据原发者或接受者提供数据原发和接收的证据。
4. 测试软件是否具备完整且无法算改的审计记录，确保用户操作可经过审计及追踪。系统操作日志/审计、异常日志、告警日志，审计日志的完整性、保密性。
5. 验证审计日志的管理，日志不能被任何人修改和删除，能够形成完整的证据链。

**测试用例参考：**

> *用户操作是否存在审计日志*
>
> *用户操作审计日志是否可以删除*
>
> *软件是否使用数字证书加密*
>
> *软件的系统操作日志、异常日志、告警日志，审计日志完整性，并只有授权用户能访问*



### 可核查性

**要求：**实体的活动可以被唯一地追溯到该实体的程度。

**测试内容：**

1. 测试系统是否将用户进程与所有者用户相关联，使用户进程行为可以追溯到进程所有者用户
2. 测试系统是否将系统进程动态地与当前服务要求者用户相关联，使系统进程的行为可以追溯到
3. 测试系统或软件的审计模块，检查模块是否具有完善的安全审计功能考察启用安全审计功能后，覆盖用户的多少和安全事件的程度，覆盖到每个用户活动，日志记录内容至少应包括事件日期、事件、发起者信息、类型、描述和结果等，审计跟踪设置是否定义了审计跟踪极限的阀值，当存储空间被耗尽时，能否采取必要的保护措施
4. 账户管理，包括账户唯一性、登录机制、密码管理策略
5. 会话管理，设计登录成功使用新的会话:设计会话数据的存储安全；设计会话数据的传输安全；设计会话的安全终止；设计合理的会话存活时间:设计避免跨站请求伪造

**测试用例参考：**

> *审计模块是否具有安全审计功能，功能是否合理完善有效*
>
> *身份标识具有唯一性，无法注册相同用户名*
>
> *多因素登录保证用户登录的一致性，且其中一种至少应使用密码技术实现*
>
> *是否存在避免跨站请求伪造漏洞，关键数据操作是否验证请求来源*
>
> *用户退出登录后是否删除所有鉴权信息，退出登录系统后使用浏览器的后退功能是否可以不通过输入口令进入系统*
>
> *是否存在登录连接超时自动退出功能*



### 真实性

**要求：**对象或资源的身份标识能够被证实符合其声明的程度。

**测试内容：**

1. 验证软件是否具有当前使用系统的用户列表和配置表。
2. 验证软件在系统的访问历史数据库中记录的访问登录记录是否完整
3. 检查软件是否具有用户使用系统的历史日志及日志管理功能。
4. 在模拟攻击事件的入侵的情况下，验证软件的日志内容是否有相关记录。
5. 检查软件中的"用户访问系统和数据”的记录内是否包括防止病毒的"病毒检测记录”。

**测试用例参考：**

> *软件是否存在用户管理模块，是否具有用户列表和配置表功能*
> *查看数据库日志访问登录日志是否完整，真实记录*
> *查看审计日志是否有用户登录使用日志*
> *查看审计日志是否有攻击防护日志*
> *软件中的"用户访问系统和数据”的记录内是否包括防止病毒的"病毒检测记录”*



### 信息安全性的依从性

**要求：**产品或系统遵循与信息安全性相关的标准、约定或法规以及类似规定的程度。

**测试内容：**

1. 检查软件产品的信息安全性是否遵循了所实施法规、标准和约定

**测试用例参考：**

> *软件是否遵循了所实施法规、标准和约定，行业标准、验收标准、等级保护标准等*



## 附件：信息安全危害分级标准

目前定义有五类危害等级，危害等级定义依据为：

| 漏洞等级 | 漏洞危害说明                                                 |
| :------: | :----------------------------------------------------------- |
|   紧急   | 可以直接被利用的漏洞，且利用难度较低。被攻击之后可能对网站或服务器的正常运行造成严重影响，或对用户财产及个人信息造成重大损失。 |
|   高危   | 被利用之后，造成的影响较大，但直接利用难度较高的漏洞。或本身无法直接攻击，但能为进一步攻击造成极大便利的漏洞。 |
|   中危   | 利用难度极高，或满足严格条件才能实现攻击的漏洞。或漏洞本身无法被直接攻击，但能为进一步攻击起较大帮助作用的漏洞。 |
|   低危   | 无法直接实现攻击，但提供的信息可能让攻击者更容易找到其他安全漏洞。 |
|   信息   | 本身对网站安全没有直接影响，提供的信息可能为攻击者提供少量帮助，或可用于其他手段的攻击，如社工等。 |