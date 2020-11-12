---
title: 8种src常用越权测试小技巧
categories:
  - 渗透测试
tags:
  - 越权
  - SRC
author: Chace
toc_number: false
date: 2020-11-12 09:25:51
---

[8种src常用越权测试小技巧](https://mp.weixin.qq.com/s?__biz=MzIxOTk2Mjg1NA==&mid=2247483941&idx=1&sn=f9ccbd4f9e6ba4678fa4f1c29529b5cd)

## (1) 添加参数 

- user/info 
- user/info?id=123 

<!--more-->

## (2) hpp 参数污染 

- user/info?id=1 
- user/info?id=2&id=1 
- user/info?id=2,2&id=1,1 

## (3) 添加.json（如果它是用 ruby 构建的）

- user/id/1 
- user/id/1.json 

## (4) 测试过时的api的版本 

- /v3/user/123 
- /v2/user/123

## (5) 用数组包装ID 

- {"id":1}
- {"id":[2]} 

## (6) 用json对象包装ID 

- {"id":1} 
- {"id":{"id":1}}

## (7) json参数污染 

- {"id":2,"id":1} 

## (8) 大小写替换

-  /admin/info -> 401未授权 
-  /ADMIN/info -> 200 ok 

## 常用技巧： 

- 可以使用通配符(*)，而不是id 
- 如果有相同的web应用程序，可以测试下app的api端点 
- 如果端点的名称类似/api/users/info,可以修改为/api/admin/info 
- 用GET/POST/PUT替换请求方法

