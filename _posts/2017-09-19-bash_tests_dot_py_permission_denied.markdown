---
layout: post
title:  "bash xxx.py permission denied"
date:   2017-09-19 15:09:10 +0800
categories: automake
---

Makefile.in 中check_TESTS 
bash tests/xxx.py   Permission Denied
原因是因为 xxx.py没有可执行权限，不是因为tests没有使用python xxx.py进行测试。
