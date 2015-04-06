---
layout: post
title: 如何解决无法使用Python发送邮件的问题
date: 2015-04-06 22:01
comments: true
categories: mail python gmail
---

这两天在玩Flask Web开发,其中讲到使用Flask Mail来发送邮件, 例子里面用的是Gmail的SMTP (Simple Mail Transter Protocal) 服务器, 我想国内的话还是用QQ邮箱方便一些吧,于是打开QQ邮箱的设置面板,找到如下的配置信息:

	接收邮件服务器：imap.qq.com，使用SSL，端口号993
	发送邮件服务器：smtp.qq.com，使用SSL，端口号465或587
	账户名：您的QQ邮箱账户名（如果您是VIP帐号或Foxmail帐号，账户名需要填写完整的邮件地址）
	密码：您的QQ邮箱密码
	电子邮件地址：您的QQ邮箱的完整邮件地址
	
兴高采烈地把这些配置写入程序, 执行一下代码, 没想到竟然报错了:

```
ssl.SSLError: [Errno 1] _ssl.c:507: error:140770FC:SSL routines:SSL23_GET_SERVER_HELLO:unknown protocol
```

一开始我以为需要在本机生成什么SSL证书之类的,不过上网搜了一圈发现,原来与程序本身无法,而是由于国内的这些邮件服务商(如网易, 腾讯)使用的SSL协议与python `smtplib`不兼容(真是坑爹呀). 无奈,只好搭上梯子去连Gmail了.

但接着还是报错了 :(

不过这次报的错信息量多多了:

```
smtplib.SMTPAuthenticationError: (534, '5.7.14 <https://accounts.google.com/ContinueSignIn?sarp=1&scc=1&plt=AKgnsbu_a\n5.7.14 jp4-nDDfH6fUTS8dQUeQfvPLhoMiYxCCT4CThSQuhcOJbzMUYI_QQZ44jUTB6FbPnYp8gv\n5.7.14 DEXJItt2gSz6WypV6cr7cZv9rpEprruo_JHXIBw6ZK3wjDKeSKKUKYMtpKoJUmARwONwcJ\n5.7.14 D3v5xypcAJtcazDB_WIUNCP8b3ZQl94GOTIKRvr7ASIgNuyD-rud8doBOTRnKpnLHbuc9B\n5.7.14 lZEGyzw> Please log in via your web browser and then try again.\n5.7.14 Learn more at\n5.7.14 https://support.google.com/mail/bin/answer.py?answer=78754 hv7sm4775459pdb.86 - gsmtp')
```

谷歌不愧是谷歌, 居然还在response里面给出了解决这个问题的网页,虽然是报错的,但是心里还是踏实多了,至少有响应了.

点开错误提示中的那个链接一看,里面列出了多个可能导致无法连接的原因, 一一分析了一下, 觉得这条嫌疑最大:

	您的邮件应用可能不支持最新的安全标准。了解如何让安全性较低的应用访问 Gmail 帐户。

转到"允许不够安全的应用"网页, 开启了让不够安全的应用访问Gmail的开关, 再次运行程序, 谢谢谷歌,邮件终于可以发出去了.

