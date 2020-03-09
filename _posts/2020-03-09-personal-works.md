---
layout: post
title:  "django+scrapy作品"
date:   2019-04-21 03:09:23
categories: python
tags: 作品
---

* content
{:toc}

在学习`django`和`scrapy`之后,楼主尝试着去结合两个框架,后来发现并没有那么容易,因为`dajngo`是同步的,而`scrapy`是异步的.是不能够相结合的.
[b站视频演示链接](https://www.bilibili.com/video/av94841063)
整个项目在我的[dataAnalyze](https://github.com/XZHhengge/dataAnalyze)上面，项目结构有点乱


再寻找资料的过程中,找到一种代替的方法.就是
把`scrapy`爬虫框架部署在[scrapyd](https://scrapyd.readthedocs.io/en/stable/)上面,`django`框架在`view`通过`requests`包动态传值,调用`scrapy`爬虫框架爬取数据然后存入数据库.然后`view`再从数据库中取出数据.(这里会有一个挺大的问题,就是`view`取数据会比`scrapy`爬取要快,就会出现前端加载完成了,但是爬虫还没爬完.这里可以让前端进行等待的方法)
大概流程就是这样,下面出代码进一步说明.
spider的一个爬虫

```
class TaobaoSpider(scrapy.Spider):
    name = 'taobao'
    allowed_domains = ['taobao.com', 'rate.tmall.com']

    # 这里初始化这个类,可以动态传值并开始爬虫,这里传的是keyword
    def __init__(self, keyword=None, *args, **kwargs):   
        super(TaobaoSpider, self).__init__(*args, **kwargs)   
        self.start_urls = ['https://s.taobao.com/search?q={}'.format(keyword)]
        self.keyword = keyword

    def parse(self, response):
        prices = re.findall('"view_price":"(.*?)",', response.text)[1]  # 正则提示商品价格
        nid = re.findall('"nid":"(.*?)"', response.text)[1]  # 正则匹配id
        ProductName.objects.filter(name=self.keyword).update(taobaoProductId=nid)
        # Product['taobaoProductId'] = nid
        # Product['name'] = self.keyword
        # yield Product
        taobaoproduct = TaobaoProduct()
        taobaoproduct['productid'] = nid
        taobaoproduct['productname'] = self.keyword
        taobaoproduct['productprice'] = prices
        taobaoproduct['producturl'] = 'https://detail.tmall.com/item.htm?spm=a230r.1.14.6.70de7ffcZej9q8&id={}&cm_id=140105335569ed55e27b&abbucket=20&sku_properties=10004:1617715035;5919063:6536025'.format(nid)
        yield taobaoproduct
        data = {}
        data['id'] = nid
        taobao_sumtagurl = 'https://rate.tmall.com/listTagClouds.htm?itemId={0}&isAll=true&isInner=true&t=&groupId=&_ksTS='.format(nid)

        yield Request(url=taobao_sumtagurl, callback=self.parse_sumcomment, meta=data, dont_filter=True)
```
官方文档:
[安装scrapyd](https://scrapyd.readthedocs.io/en/stable/install.html#),ubuntu大概就是`apt-get install scrapyd`,Windows 下`pip install scrapyd`
安装之后,在有**scrapy.cfg**同级目录下命令`scrapyd`,然后服务就开启了.
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190420201602885.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwOTY1MTc3,size_16,color_FFFFFF,t_70)
然后目录会多出`twistd.pid`和`setup.py`,`build`目录.
然后部署`scrapy`爬虫,要部署还有下载[scrapyd-client](https://github.com/scrapy/scrapyd-client)
安装完`scrapy-client`之后,配置一下`scrapy.cfg`
`project= **`为项目名字,.`[deploy:***]`**可以自己定义
就可以通过`scrapyd-deploy  <target> -p <project> --version <version>`

参数解释
target :depoly 后面的名称
project :建议使用项目名字
version 版本,自己定义,不写为当前时间
ex
scrapyd-deploy 100 -p taobao --version 1.0
`scrapy`部署这里不多说了,网上自己搜索.要是部署没有出错的话,我们`scrapyd`的`api`查看项目状态.
浏览器输入`http://localhost:6800/listprojects.json`查看爬虫项目信息
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190420202637363.png)
**这里面有一个很重要的信息,就是这个projects的名字.我一开始部署完之后,并不知project的名字是是什么,导致后来找了很久**

我们动态传值的时候,要以字典的形式传这个projects的值
通过`requests`传值如下
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190420203256149.png)
还有另外一个`scrapy` `items`保存到数据库中的,也挺好用的就是[scrapy-djangoitem](https://github.com/scrapy-plugins/scrapy-djangoitem),有了这个包,`scrapy`框架的`items.py`和`pipelines.py`都很简单地写
ex


```
from scrapy_djangoitem import DjangoItem

from taobao.models import *


class ProductName(DjangoItem):
    django_model = ProductName


class JDProductsItem(DjangoItem):
    django_model = JDProductsItem


class JDCommentSummaryItem(DjangoItem):
    django_model = JDCommentSummaryItem


class JDHotCommentTagItem(DjangoItem):
    django_model = JDHotCommentTagItem


class JDCommentItem(DjangoItem):
    django_model = JDCommentItem
```
在pipelines.py中直接用item.save()就可以代替一大串的sql语句
```
        if isinstance(item, ProductName):
            try:
                item.save()
                # cur = self.conn.cursor()
                # sql = '''insert into product values (%s,%s,%s,%s,%s,%s,%s)'''
                # self.conn.ping(reconnect=True)
                # cur.execute(sql,(item['productid'],item['category'],item['description'],item['name'],item['imgurl'],item['reallyPrice'],item['url']))
                # cur.close()
                # self.conn.commit()
                # self.conn.close()
            except Exception as e:
                print(e)
        elif isinstance(item, JDProductsItem):
            try:
                item.save()
                # cur = self.conn.cursor()
                # sql = '''insert into productlink values (%s,%s)'''
                # self.conn.ping(reconnect=True)
                # cur.execute(sql,(item['id'], item['imgurl']))
                # cur.close()
                # self.conn.commit()
                # self.conn.close()
            except Exception as e:
                print(e)
```

此外国外有人做一个[django-dynamic-scraper](https://github.com/holgerd77/django-dynamic-scraper)的项目,有兴趣的同学,可以进行尝试一下.
