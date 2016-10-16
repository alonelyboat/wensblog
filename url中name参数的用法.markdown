# url函数中name参数的使用
- ps. 初学django，URLconf中，url函数中的name参数在练习中，难以理解其意义，通过百度
整理了一些东西
- 作用和用法：name的作用是保存 请求url（如收到请求www.sina.com/news/df/?a=4, 请求url为news/df/），因为请求url在用正则表达式匹配并指定视图后，会丢失，但是如果将其保存在name变量中后，可以在views和模板文件中使用，在模板中使用，可以优化超链接，减少链接硬编码。下面以一个新闻网站为例介绍，主页是 127.0.0.1：8080/news/, 这个url链接的视图news所渲染的模板页面是 news.html, 主要介绍在模板文件中的应用

```
# 新闻网站 首页是 news.html
# url请求: 127.0.0.1:8080/news/
# urls.py
 url(r'^news/$',news,name='nvar')
# views.py: 
def news(req):
    ...
    retrun render(req,'news.html',context)  

# news.html 中存在大量的链接 
<a href='/news/sadam'>sadam</a> ,
<a href='/news/obama'>obanma</a> , 
...
<a href='/news/kazafei/'>kazafei</a> 
# href使用了硬编码，如果在一段时间后，要求修改链接 将 /news/***/ 改为 /news/international/***/，将 
# 那么，需要一条一条修改，工作量大，容易出错
# 如果引用url的name参数，将url保存在name参数指定的变量中（在此是 nvar），
# 那么，href参数可以引用 nvar, 使得上述需求非常容易实现：
# news.html 链接修改如下，即name参数在html模板中的用法：
<a href='{% url 'nvar' %}sadam'>sadam</a> ,
<a href='{% url 'nvar' %}obama'>obanma</a> , 
...
<a href='{% url 'nvar' %}kazafei/'>kazafei</a> 
#  用render to template时，{% url 'nvar' %}随request 参数一起注入到模板

# 在views中使用变量 nvar，需要用reverse函数将其解析, 用法如下
from django.core.urlresolvers import reverse
url = reverse("nvar") # url保存了nvar指定的url


# 带参数的情况，这种情况感觉用的不多
# urls.py
url(r'^news/(\d{1,2})/$',news,name='nvar')
# 那么，前面介绍的两种用法需要修改, 增加参数，参数必须与模式匹配，在此参数应该为一个 1个数字或者两个数字

# html， 当然，此处没有什么实用价值，主要是说明带参数的用法
<a href='{% url 'nvar' 3 %}sadam'>sadam</a> ,
<a href='{% url 'nvar' 12%}obama'>obanma</a> , 
...
<a href='{% url 'nvar' %}kazafei/'>kazafei</a> 

# views 中的用法 
from django.core.urlresolvers import reverse
url = reverse("nvar",args=('21',)) 


# url中，使用子url，即include
# 那么，name变量需要设在子url
# urls.py
url(r'^news/',include('international.urls')
# international.urls.py
url(r'^international/$', news, name='nvar')
# views.py中：
from django.core.urlresolvers import reverse
url = reverse("nvar") # url='/news/international/'
```


