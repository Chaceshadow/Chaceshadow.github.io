---
title: 软件测试项目实操指南与过程文档
categories:
  - 软件测评
tags:
  - 软件测评
  - 模板
  - 过程文档
author: Chace
toc_number: false
date: 2020-09-28 10:34:02
---

> 软件测试一般分为测试需求分析、测试计划编制、测试方案设计阶、测试用例实现、测试执行、评估与关闭六大阶段。
>

## 一、测试需求分析阶段

测试需求分析阶段主要工作是获得测试项目的测试需求(测试规格)。
输出产物:《可测试性需求说明书》和《测试规格》

<!--more-->

在实际过程中针对软件的安全性测评项目，甲方通常会提供安全功能测试需求，这就需要我们通过需求分析把甲方的安全测试需求对应到软件的信息安全性的六大子特性中。通常情况软件的安全功能应该是和我们的软件产品质量的信息安全性是对应的，但是在实际检测过程中，由于软件的差异性和各自的功能需求特性，软件产品可能无法包含所有的安全子特性的所有要求，就需要我们给甲方指出缺失项，并和甲方客户确认协商。

测试计划文档目录如下：

```
一、概述
 	1.1 需求分析目的
 	1.2 需求分析的依据
 	1.3 需求分析的方法
二、软件产品说明
 	2.1 项目背景
 	2.2 项目需求说明
 	2.3 项目整体设计说明
三、测试需求分析
 	3.1 原始需求
 	3.2 产品测试需求列表
		3.2.1 功能测试需求
		3.2.2 安全测试需求
		3.2.3 性能测试需求
		3.2.4 压力测试需求
 	3.3 测试类型确定
 	3.4 测试环境要求
		3.4.1 硬件要求
		3.4.2 软件要求
四、测试规格评估
 	4.1 测试类型评估
 	4.2 测试用例密度
 	4.3 需求覆盖率
```

参考文档链接：[软件测试需求分析报告](https://wenku.baidu.com/view/d28383ac4b7302768e9951e79b89680202d86b44.html)

## 二、测试计划编制阶段

以测试需求为基础,分析产品的总体测试策略。
输出产物:《产品总体测试计划》

当对需求有完整和全面的理解后，接下来我们需要制定详细的测试计划，为即将开始的测试工作做好充足的准备。

测试计划描述了要进行的测试活动的范围、方法、资源和进度的文档。它主要包括测试项、被测特性、测试任务、谁执行任务和风险控制等。

测试计划文档目录如下：

```
一、概述
 	1.1 项目说明
 	1.2 测试范围
二、测试目标
三、测试资源
 	3.1 软件资源
 	3.2 硬件资源
 	3.3 测试工具
 	3.4 人力资源
四、测试种类和测试标准
 	4.1 功能测试
 	4.2 性能测试
 	4.3 安全测试
 	4.4 易用性测试
 	…………
五、测试要点
六、测试时间和进度
七、风险及对策
```

## 三、测试方案设计阶段

本阶段主要是以测试规格为基础获得特性测试方案,对于有自动化测试的项目,
进行自动化测试的分析,获得测试策略
输出产物:《产品或者版本总体测试方案》

测试方案是指描述需要测试的特性、测试的方法、测试环境的规划、测试工具的设计和选择、测试用例的设计方法、测试代码的设计方案。

测试方案文档目录如下：

```
一、引言
    1.1 编写目的
    1.2 项目背景
    1.3 项目相关方
        1.3.1 委托方与联系方式
        1.3.2 承研单位与联系方式
        1.3.3 测试机构与联系方式
    1.4 测试目标及范围
    1.5 引用文件
二、项目软件介绍
    2.1 功能需求介绍
    2.2 非功能需求介绍
        2.2.1 性能效率需求
        2.2.2 兼容性需求
        2.2.3 易用性需求
        2.2.4 可靠性需求
        2.2.5 信息安全性需求
        2.2.6 维护性需求
        2.2.7可移植性需求测试需求
三、测试需求分析
    3.1 测试总体要求
    3.2 功能性测试
        3.2.1 测试分析
        3.2.2 测试内容
    3.3 性能效率测试
        3.3.1 测试分析
        3.3.2 测试内容
    3.4 兼容性测
        3.4.1 测试分析
        3.4.2 测试内容
    3.5 易用性测试
        3.5.1 测试分析
        3.5.2 测试内容
    3.6 可靠性测试
        3.6.1 测试分析
        3.6.2 测试内容
    3.7 信息安全性测试
        3.7.1 测试分析
        3.7.2 测试内容
    3.8 维护性测试
        3.8.1 测试分析
        3.8.2 测试内容
    3.9 可移植性测试
        3.9.1 测试分析
        3.9.2 测试内容
    3.10 用户文档集测试
        3.10.1 测试分析
        3.10.2 测试内容
    3.11 静态分析
        3.11.1 测试分析
        3.11.2 测试内容
    3.12 安全渗透测试
        3.12.1 测试分析
        3.12.2 测试内容
    3.13 测试需求追踪
    3.14 测试项标识说明
    3.15 测试方法
        3.15.1 功能测试
        3.15.2 性能效率测试
        3.15.3 兼容性测试
        3.15.4 易用性测试
        3.15.5 可靠性测试
        3.15.6 信息安全性测
        3.15.7 维护性测试
        3.15.8 可移植性测
        3.15.9 用户文档集测试
        3.15.10 静态分析
        3.15.11 内存检测
        3.15.12 软件质量度量
        3.15.13 安全渗透测试
    3.16 测试充分性要求
    3.17 测试终止要求
        3.17.1 正常终止
        3.17.2 异常中止
        3.17.3 中止及重新启动
    3.18 测试通过准则
四、测试环境
    4.1 测试环境要求
    4.2 静态测试环境
        4.2.1 测试环境配置
    4.3 功能测试环境
        4.3.1 测试环境配置
        4.3.2 环境差异影响分析
    4.4 效率测试环境
        4.4.1 测试环境配置
        4.4.2 环境差异影响分析
    4.5 测试工具配置
        4.5.1 工具使用计划
        4.5.2 测试工具介绍
五、项目团队架构及职责
    5.1 团队组织架构
    5.2 团队职责分工
六、测试计划进度
    6.1 测试流程
    6.2 进度计划
七、过程质量管理
    7.1 配置管理
    7.2 质量保证
        7.2.1 质量目标和要求
        7.2.2 质量保证任务
    7.3跟踪与控制
八、测试交付成果
九、测试验收规范
十、风险分析应对
```



## 四、测试用例实现阶段

本阶段主要是完成各个特性的测试用例的编写和自动化脚本的编写。
输出产物:《产品自动化测试用例》和《手工执行测试用例》

用于描述测试用例的具体细节工作，测试用例一般根据测试计划及测试策略来编写

测试用例设计文档模板如下：

![](1.png)

测试用例执行文档模板如下：

![](2.png)

## 五、测试执行阶段

本阶段是根据测试策略开展测试执行和回归测试
输出产品:《产品或版本测试报告》和《缺陷分析报告》

《测试报告文档模板》如下：

```
一、测试基本信息
二、被测系统概述
三、测试资源
    3.1 组织
    3.2 测试环境及工具
        3.2.1 测试环境一
        3.2.2 测试环境二
四、测试规程
    4.1 充分性评价
    4.2 测试过程
五、测试结果
    5.1 软件问题情况
    5.2 测试执行结果
        5.2.1 功能性测试
        5.2.2 可靠性测试
        5.2.3 易用性测试
        5.2.4 效率测试
        5.2.5 维护性测试
        5.2.6 可移植性测试
        5.2.7 用户文档测试
        5.2.8 使用质量测试
        5.2.9 产品说明测试
附录A 软件问题清单
附录B 效率测试结果
```

《缺陷分析报告模板》如下：

![](3.png)

缺陷填写说明见附件1

## 六、评估与关闭阶段

只对前面的各个阶段的执行情况,完成对测试项目的关闭,同时提供完整的度量数据和项目总结报告
输出产物:《遗留问题风险分析报告》、《度量分析报告》和《测试关闭报告》





## 附件1：软件测试缺陷的定义级别、优先级及状态

### 1、缺陷的定义及主要类型

我们对软件缺陷分析一下,所谓"软件缺陷（bug）"，即为计算机软件或程序中存在的某种破坏正常运行能力的问题、错误，或者隐藏的功能缺陷。一般来说，软件缺陷的属性包括缺陷标识、缺陷类型、缺陷严重程度、缺陷优先级、缺陷来源、缺陷原因等。

进行软件缺陷分析后,软件缺陷的主要可以分为以下几种类型：

（1）设计不合理;

（2）功能、特性没有实现或部分实现; 

（3）运行出错，包括运行中断、系统崩溃、界面混乱等;

（4）与需求不一致，在执行TestCase时则为实际结果和预期结果不一致;

（5）用户不能接受的其他问题，如存取时间过长、界面不美观等;

（6）软件实现了需求未提到的功能。

（7）软件存在安全漏洞

### 2、缺陷的级别、优先级及状态

#### 2.1 缺陷级别

软件缺陷有四种级别，分别为：致命的(Fatal)，严重的(Critical)，一般的(Major)，微小的(Minor)。

A类—致命的软件缺陷(Fatal):造成系统或应用程序崩溃、死机、系统挂起，或造成数据丢失，主要功能完全丧失，导致本模块以及相关模块异常等问题。如代码错误，死循环，数据库发生死锁、与数据库连接错误或数据通讯错误，未考虑异常操作，功能错误等

B类—严重错误的软件缺陷（critical）：系统的主要功能部分丧失、数据不能保存，系统的次要功能完全丧失。问题局限在本模块，导致模块功能失效或异常退出。如致命的错误声明，程序接口错误，数据库的表、业务规则、缺省值未加完整性等约束条件

C类—一般错误的软件缺陷（major）：次要功能没有完全实现但不影响使用。如提示信息不太准确，或用户界面差，操作时间长，模块功能部分失效等，打印内容、格式错误，删除操作未给出提示，数据库表中有过多的空字段等

D类—较小错误的软件缺陷（Minor）:使操作者不方便或遇到麻烦，但它不影响功能过的操作和执行，如错别字、界面不规范（字体大小不统一，文字排列不整齐，可输入区域和只读区域没有明显的区分标志），辅助说明描述不清楚

E类- 建议问题的软件缺陷（Enhancemental）：由问题提出人对测试对象的改进意见或测试人员提出的建议、质疑。

#### 2.2 缺陷优先级

常用的软件缺陷的优先级表示方法可分为：立即解决P1、高优先级P2、正常排队P3、低优先级P4。立即解决是指缺陷导致系统几乎不能使用或者测试不能继续，需立即修复；高优先级是指缺陷严重影响测试，需要优先考虑；正常排队是指缺陷需要正常排队等待修复；而低优先级是指缺陷可以在开发人员有时间的时候再被纠正。

#### 2.3 缺陷状态

（1）激活状态(Active或Open)。

（2）已修正状态(Fixed或Resolved)。

（3）关闭或非激活状态(Close或Inactive)。