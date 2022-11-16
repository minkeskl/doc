# python邮件收发
[TOC]
## 协议简介
简单介绍一下，具体信息可参考图解TCTCP/IP这本书，不需要深入了解实现
### pop3
POP3是Post Office Protocol 3的简称，即邮局协议的第3个版本,它规定怎样将个人计算机连接到Internet的邮件服务器和下载电子邮件的电子协议。它是因特网电子邮件的第一个离线协议标准,POP3允许用户从服务器上把邮件存储到本地主机（即自己的计算机）上,同时删除保存在邮件服务器上的邮件，而POP3服务器则是遵循POP3协议的接收邮件服务器，用来接收电子邮件的。
### IMAP
IMAP全称是Internet Mail Access Protocol，即交互式邮件存取协议，它是跟POP3类似邮件访问标准协议之一。不同的是，开启了IMAP后，您在电子邮件客户端收取的邮件仍然保留在服务器上，同时在客户端上的操作都会反馈到服务器上，如：删除邮件，标记已读等，服务器上的邮件也会做相应的动作。所以无论从浏览器登录邮箱或者客户端软件登录邮箱，看到的邮件以及状态都是一致的。
### SMTP
SMTP（Simple Mail Transfer Protocol）即简单邮件传输协议,它是一组用于由源地址到目的地址传送邮件的规则，由它来控制信件的中转方式。
python的smtplib提供了一种很方便的途径发送电子邮件。它对smtp协议进行了简单的封装。
## 功能构想
我们在这里实现简单的收发，更多功能可自由实现
### 邮件的收取
使用poplib库,email库来实现我们的收取
#### 代码示例
```python
#-*- encoding: gb2312 -*-
import os, sys, string
import poplib

# pop3服务器地址
host = "pop3.163.com"
# 用户名
username = "xxxxxx@163.com"
# 密码
password = "xxxxxxx"
# 创建一个pop3对象，这个时候实际上已经连接上服务器了
pp = poplib.POP3(host)
# 设置调试模式，可以看到与服务器的交互信息
pp.set_debuglevel(1)
# 向服务器发送用户名
pp.user(username)
# 向服务器发送密码
pp.pass_(password)
# 获取服务器上信件信息，返回是一个列表，第一项是一共有多上封邮件，第二项是共有多少字节
ret = pp.stat()
print ret
# 需要取出所有信件的头部，信件id是从1开始的。
for i in range(1, ret[0]+1):
    # 取出信件头部。注意：top指定的行数是以信件头为基数的，也就是说当取0行，
    # 其实是返回头部信息，取1行其实是返回头部信息之外再多1行。
    mlist = pp.top(i, 0)
    print 'line: ', len(mlist[1])
# 列出服务器上邮件信息，这个会对每一封邮件都输出id和大小。不象stat输出的是总的统计信息
ret = pp.list()
print ret
# 取第一封邮件完整信息，在返回值里，是按行存储在down[1]的列表里的。down[0]是返回的状态信息
down = pp.retr(1)
print 'lines:', len(down)
# 输出邮件
for line in down[1]:
    print line
# 退出
pp.quit()
在有些地方，有安全邮件这一说，其实是对pop3做了ssl加密。这样的，poplib一样可以处理，只不过不是用POP3这个类，而是用POP3_SSL, 他们的方法都一样。因此支持ssl在上面代码中，替换创建pop3对象的一行为：

pp = poplib.POP3_SSL(host)
```
### 邮件的发送
使用smtp实现发送邮件
#### 代码示例
```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
import smtplib
from email.mime.text import MIMEText
from email.header import Header
 
sender = 'from@runoob.com'
receivers = ['429240967@qq.com']  # 接收邮件，可设置为你的QQ邮箱或者其他邮箱
 
# 三个参数：第一个为文本内容，第二个 plain 设置文本格式，第三个 utf-8 设置编码
message = MIMEText('Python 邮件发送测试...', 'plain', 'utf-8')
message['From'] = Header("菜鸟教程", 'utf-8')   # 发送者
message['To'] =  Header("测试", 'utf-8')        # 接收者
 
subject = 'Python SMTP 邮件测试'
message['Subject'] = Header(subject, 'utf-8')
 
 
try:
    smtpObj = smtplib.SMTP('localhost')
    smtpObj.sendmail(sender, receivers, message.as_string())
    print "邮件发送成功"
except smtplib.SMTPException:
    print "Error: 无法发送邮件"
```
附上[菜鸟教程的文章](https://www.runoob.com/python/python-email.html)
## 总结
添加这种收发模块到自己的服务器上运行的服务上,可以增加交互性,实现诸如自动回复,错误报警等功能,对我还是比较实用的，关于自动回复
> 个人水平不足,相关信息都是学习自他人博客,错误在所难免,欢迎指出
> 邮箱:**minkeskl@qq.com**



