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
**下面是整个流程**

#  登录

```python
https://mobile.yiban.cn/api/v2/passport/login?v=9.9.9&ct=99&account=&passwd=
```

**居然是明文的，还是get请求，请求成功之后，获取access_token**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200508085327598.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
#  在APP主页面获取疫情防控入口链接
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200515075117147.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200515075210303.png)
#  请求疫情防控的链接
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200515075435950.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
#  获取跳转Location
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020051507550033.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
#  认证
还是易班的Host，其他都没变化。
act=iapp603148（我们学校在易班的编号）
v=xxx(这是我们登录成功后的access_token)

Location为我们学校的认证url
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200508081912774.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
#  我们接着访问到学校认证url，然后获取到cookie
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200508083119124.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
#  打卡
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200511153720670.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
#  id = xmid为固定值

#  无法确定signature的设置，反编译APP获取源码查看代码
#  APP脱壳

#  使用[drizzleDumper](https://github.com/DrizzleRisk/drizzleDumper)在手机上执行linux命令进行app脱壳。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200513060634975.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
#  复制到电脑后，使用[jadx](https://github.com/skylot/jadx)打开源码（用于搜索，jadx打开代码可能不全），用dex2jar把dex文件转为jar,再用[jarExplorer](http://dst.in.ua/jarexp/index.html?l)打开（用于查看，搜索较为缓慢）
#  并且jarExplorer有三种decompilers方式
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020051423490551.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
#  比较两个工具，很明显，左边有很多的代码无法获取
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200513061247632.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
#  找到设置signature的地方
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200514235116687.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
#  查看工具DesUtils类
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200513062230440.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
#  确认加密为Base64(DES())，双重加密
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200515092425560.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
#  App版本待续未完，android7.0抓包，app某些动作无网，无法获得连续的信息，故搁置。

#  web版本（学校提供web版本打卡）
#  加入定时器，到点自动打卡，发送email到邮箱。
```python
# -*- coding: utf-8 -*-
import requests
import smtplib
from email.mime.text import MIMEText
import datetime

from apscheduler.schedulers.blocking import BlockingScheduler
# 电脑打卡
class PCPostYiBan:
    def __init__(self, username: int, mail_address):
        self.mail_address = mail_address
        self.username = username
        self.cookies = {}
        self.headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.129 Safari/537.36'}
        self.id = ''
        self.session = requests.session()
        self.message = ''

    def pc_login(self):
        try:
            url = 'http://yq.gdupt.edu.cn/login/Login.htm'
            data = {'username': self.username, 'password': 'abc123!'}
            self.session.post(url=url, data=data, headers=self.headers)
            content = self.session.post(url='http://yq.gdupt.edu.cn/syt/zzapply/queryxmqks.htm?type=yqsjsb', data={'pageIndex': 0, 'pageSize': 20})
            if content.status_code == 200:
                self.id = eval(str(content.text).replace('null', '0').replace('true', '1').replace('false', '0'))['data'][0]['id']
                # print(self.id)
                self.daily_post()
            else:
                self.message = '登录失败'
                print('登录失败')
        except Exception as e:
            self.message = '出现错误，请报告给开发者'+str(e)
            self.sendMail(self.message, self.mail_address)

    def daily_post(self):
        try:
            r = self.session.post(url='http://yq.gdupt.edu.cn/syt/zzapply/operation.htm', data={'data': '''{"xmqkb":{"id":"ff8080817056f727017057083b010001"},"pdnf":"2020","type":"yqsjsb","c5":"36-37.2°C","c6":"健康","c7":"健康","c8":"否","c9":"","c2":"","c3":"","c10":"","c11":"","c12":"","c1":"否","c4":""}''',
    'msgUrl': '''syt/zzglappro/index.htm?type=yqsjsb&xmid=ff8080817056f727017057083b010001'''})
            print(r.status_code)
            if r.text == 'success':
                self.message = '打卡成功'
                self.sendMail(self.message, self.mail_address)
                print('打卡成功')
                # if scheduler.get_job(job_id='test2'):
                #     scheduler.remove_job(job_id='test2')
            else:
                print('打卡不成功，请查看是否已打卡。')
                #  打卡不成功
                # scheduler.add_job(myyiban.daily_post, 'interval', minute=45, id='test2')
        except Exception as e:
            self.message = '出现错误，请报告给开发者'+str(e)
            self.sendMail(self.message, self.mail_address)

    def check_post(self):
        data = {'xmid': self.id, 'pdnf': 2020}
        r = self.session.post(url='http://yq.gdupt.edu.cn/syt/zzapply/checkrestrict.htm', data=data)
        print(r.text)

    # def getInfo(self):
    #     r = requests.get(url='http://yq.gdupt.edu.cn/syt/student/getById.htm', cookies=self.cookies, headers=self.headers)
    #     print(r.status_code)
    #     print(r.text)
    def sendMail(self, message:str, mail_address:str):
        from_addr = '****@163.com'
        password = 'RRSFPYLJOFPMXANX'

        # 收信方邮箱
        to_addr = mail_address

        # 发信服务器
        smtp_server = 'smtp.163.com'

        mail_msg = """人生苦短，我用python.打卡时间：{}""".format(str(datetime.datetime.now())[:19])
        # 邮箱正文内容，第一个参数为内容，第二个参数为格式(plain 为纯文本)，第三个参数为编码
        msg = MIMEText(mail_msg, 'html', 'utf-8')

        # 邮件头信息
        msg['From'] = '***@163.com'
        msg['To'] = '***qq.com'
        msg['Subject'] = '{},{}'.format(str(datetime.datetime.now())[5:10], message)
        # msg['Subject'] = Header('计算机17-1 37 谢卓亨 实验结果验收', 'utf-8')

        # 开启发信服务，这里使用的是加密传输
        server = smtplib.SMTP_SSL()
        server.connect(smtp_server, 465)
        # 登录发信邮箱
        server.login(from_addr, password)
        # 发送邮件
        server.sendmail(from_addr, to_addr, msg.as_string())

def post_all():
    information = {
    #   学号： ‘邮箱’
        *****: '***@qq.com',
    }
    for number, mail_address in information.items():
        personal = PCPostYiBan(number, mail_address)
        personal.pc_login()
        # personal.check_post()


if __name__ == '__main__':
    scheduler = BlockingScheduler()
    scheduler.add_job(post_all, 'cron', hour=12, minute=0, id='one')
    # print('Press Ctrl+{0} to exit'.format('Break' if os.name == 'nt' else 'C'))

    scheduler.start()
    # scheduler.
```



