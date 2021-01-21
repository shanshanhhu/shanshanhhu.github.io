---
title: Web Security Introduction
date: 2021-01-20 14:30:22 +0800
categories: [security, websecurity]
tags: [websecurity]
---

# security vulnerability
- **SQL Injection**
- **XSS(Cross-site scripting)**
    XSS attacks are a type of injection, in which malicious scripts are injected into otherwise benign and trusted websites.
- **CSRF(Cross-Site Request Forgery)**
    an attack that forces an end user to execute unwanted actions on a web application in which they're currently authenticated.

# First Chapter
- **信任域：**\
A trust domain is a domain that the local system trusts to authenticate users.
**信任边界：**\
the boundary that divides two different trust domains.

- **Security is essentially a matter of trust.**\
The foundation of all security scheme design is built on the trust relationship. We have to believe in something. There has to be
some basic assumptions in order to establish security schemes.
- **Three elements:CIA Confidentiality, Integrity, Availability**\
  - 机密性（Confidentiality）: requires the protected data cannot be leak.
  - 完整性（Integrity）: requires that the data is intact and not be tampered with.
  - 可用性（Availability）: 要求保护资源是“随需而得”。
- The process of security evaluation.\
资产等级划分 -> 威胁分析 -> 风险分析 -> 确认解决方案


# Protocols
- **ACL**
An **access control list (ACL)** is a table that tells a computer operating
system which access rights each user has to a particular system object,
such as a file directory or individual file. Each object has a security
attribute that identifies its access control list.

- **RPC**
**[Remote Procedure Call (RPC)](https://searchapparchitecture.techtarget.com/definition/Remote-Procedure-Call-RPC)** is a protocol that one program can use to request
a service from a program located in another computer on a network without
having to understand the network's details. RPC is used to call other processes on the remote systems like a local system.
[understand RPC](https://www.zhihu.com/question/25536695)




