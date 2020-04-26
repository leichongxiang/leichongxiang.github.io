title: google hosts
date: 2015-11-30 13:53:35
tags:
 - google 
categories: [软件]

---
google一直被墙，对于我们解决技术问题很是不方便，网上有人会定期更新hosts文件，每次手动更新太麻烦，于是就用python的requests模块写了一个脚本，需要更新的时候回执行脚本自动替换C:\Windows\System32\drivers\etc\hosts文件。

<!--more -->

# 更改hosts目录的安全设置
需要更改C:\Windows\System32\drivers\etc目录的权限，否则会出现无法访问hosts文件的问题。具体办法：
etc目录右键--》安全--》权限--》给完全控制的权限

# python脚本
首先需要在windows上支持python,另外需要安装python添加模块的工具pip或者easy_install，pip install requests
```python
# -*- coding: utf-8 -*-
# ----------------------------------
# Desc:   Python scripts
# Author: leicx
# Date:   15-11-30
# Version:1.0
# ----------------------------------
 
import urllib,os
import requests
 	
def main():
	url = 'https://raw.githubusercontent.com/racaljk/hosts/master/hosts'
	local_file = r'C:\Windows\System32\drivers\etc\hosts'
	#local_file = r'd:\a.txt'
	r = requests.get(url)
	output = file(local_file, 'w')
	with open(local_file, "w") as code:
	    code.write(r.content)

if __name__ == '__main__':
    main()
```
