title: perl脚本发送邮件
date: 2015-11-28 16:53:35
tags:
 - perl
categories: [perl]
---

perl脚本调用mailer模块去发送邮件，首先需要安装之指定的模块。
安装方法：
`perl -MCPAN -e shell`
`cpan[1]>install Mail::Sender`
或者
`perl -MCPAN -e "install Mail::Mailer"`
可能需要在邮箱的客户端启用smtp，pop等设置

<!--more -->
# perl脚本
用perl的mailer模块写的发送邮件
```python
#!/usr/bin/perl

use strict;
use Mail::Mailer;

my $from_address = '316666062@163.com';
my $to_address = '316666062@qq.com>';
my $subject = "send mail perl";
my $mail_body = "hello world perl!";

my $mailer = Mail::Mailer->new("sendmail");
$mailer->open({ From    => $from_address,
                To      => $to_address,
                Subject => $subject,
                })or die ("Can't open: $!\n");
print $mailer $mail_body;
$mailer->close();

```

# python发送邮件

```python
#!/usr/bin/env python

import smtplib
import string

HOST = "smtp.163.com"
SUBJECT = "Test email from Python"
TO = "316666062@qq.com"
FROM = "316666062@163.com"
text = "Python rules them all!"
BODY = string.join((
        "From: %s" % FROM,
        "To: %s" % TO,
        "Subject: %s" % SUBJECT ,
        "",
        text
        ), "\r\n")
server = smtplib.SMTP()
server.connect(HOST,"25")
server.starttls()
server.login("316666062@163.com","******")
server.sendmail(FROM, [TO], BODY)
server.quit()
```
