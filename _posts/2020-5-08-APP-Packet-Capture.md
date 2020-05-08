---
layout: post
title:  "一次APP抓包失败之旅"
date:   2020-05-08 10:16
categories: 爬虫
tags: 抓包、爬虫
---

* content
{:toc}




#  目标APP：易班
#  目的：实现自动打卡
#  失败原因：无法破解易班APP对我们学校登录的headers里面的signature和authorization，logintoken（进行过多种解码，无法获得有效信息）。利用dex2jar反编译APP无法获得全部代码。
**下面是整个流程**

 1. 手机先安装Packet Capture进行抓包。抓到如下（蓝色为send，红色为return）**图片与图片之间不一定是同一次抓包**：
 
 这是发出的第一个请求，这里的Host还是在易班，在对比多次的请求后，发现
 logintoken和authorization相同，也是url的一部分，变化没有找到规律，有时一个上午相同，有时下一次请求会变，一天后会变化。
 v_time为15位时间戳
 signature前面一部分不变，后面一些每一次不相同
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200508080654777.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
**第二次请求**
还是易班的Host，其他都没变化。
返回一个跳转到我们学校的url，还给了几个access_token,第一个yibanM_user_token为我们的logintoken.第二个yiban_user_token未知。Location为我们学校的认证url
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200508081912774.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
**第三次**
即使跳到了我们学校，还是带着之前的一系列认证的参数。
return **这里最重要的东西Cookies出现了，有了Cookies，然后的打卡就不成为问题。**
Location 还是我们学校另外一个链接
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200508083119124.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
**直接复制上面的认证url，用代码去请求不会报错，但是拿到Cookies之后，想去Post打卡的url会报错。**
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020050808395975.png)
**直接复制相应的Cookies去POST打开，也会出现同样的错误。**
#  在参考他人的时候发现了易班其他的api

```python
url = 'https://mobile.yiban.cn/api/v2/passport/login?v=9.9.9&ct=99&account=' + urllib.request.quote(username) + '&passwd=' + urllib.request.quote(password)
```
**居然是明文的，还是get请求，请求成功之后，然后访问web_accesstoken_login_url**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200508085327598.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
得到与APP相似的yibanM_user_token和yiban_user_token(多次访问都没有变化)但是没有Location，无法跳到我们学校的认证的url，也就无法获得相应的Cookies。不过下面的CNZZDA。。。和_cnzz_CV1253488264分别有易班的一个url和我们学校的url（进行urlDecode得到），这个我们学校的url不是认证的，而是我们学校提供给我们全体学校学生手机坏掉的时候，可以进行web进行打卡，这里已经实现了代码实现web的打开，但我们还是更希望自己破解APP的流程。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200508085941462.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
#  反编译APP
[参考文章](https://blog.csdn.net/u010782846/article/details/79765866)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200508092741677.png)
**classes.dex有9m，jar只有16k，很明显App是加密的，而且里面的源码只有几个类**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200508092758142.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200508092947507.png)
