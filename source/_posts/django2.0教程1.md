---
title: django2.0教程1
date: 2020-09-21 07:53:05
categories: 开发
tags: Django
---
# Django
Django 最初被设计用于具有快速开发需求的新闻类站点，目的是要实现简单快捷的网站开发。以下内容简要介绍了如何使用 Django 实现一个数据库驱动的 Web 应用。

# Django主要组成
## views
## settings
##

# Django常用的管理语句

# 快速入门
## 设计模型
Django 无需数据库就可以使用，它提供了 对象关系映射器 通过此技术，你可以使用 Python 代码来描述数据库结构。

你可以使用强大的 数据-模型语句 来描述你的数据模型，这解决了数年以来在数据库模式中的难题。以下是一个简明的例子：

```
mysite/news/models.py
from django.db import models

class Reporter(models.Model):
    full_name = models.CharField(max_length=70)

    def __str__(self):
        return self.full_name

class Article(models.Model):
    pub_date = models.DateField()
    headline = models.CharField(max_length=200)
    content = models.TextField()
    reporter = models.ForeignKey(Reporter, on_delete=models.CASCADE)

    def __str__(self):
        return self.headline
```
## 应用数据模型

然后，运行 Django 命令行工具来创建数据库表：

这个 migrate 命令查找你所有可用的模型并将在数据库中创建那些不存在的表，还可提供了可选的 丰富 schema 控制
```
$ python manage.py migrate
```

## 便捷的API

```
# Import the models we created from our "news" app
>>> from news.models import Article, Reporter

# No reporters are in the system yet.
>>> Reporter.objects.all()
<QuerySet []>

# Create a new Reporter.
>>> r = Reporter(full_name='John Smith')

# Save the object into the database. You have to call save() explicitly.
>>> r.save()

# Now it has an ID.
>>> r.id
1

# Now the new reporter is in the database.
>>> Reporter.objects.all()
<QuerySet [<Reporter: John Smith>]>

# Fields are represented as attributes on the Python object.
>>> r.full_name
'John Smith'

# Django provides a rich database lookup API.
>>> Reporter.objects.get(id=1)
<Reporter: John Smith>
>>> Reporter.objects.get(full_name__startswith='John')
<Reporter: John Smith>
>>> Reporter.objects.get(full_name__contains='mith')
<Reporter: John Smith>
>>> Reporter.objects.get(id=2)
Traceback (most recent call last):
    ...
DoesNotExist: Reporter matching query does not exist.

# Create an article.
>>> from datetime import date
>>> a = Article(pub_date=date.today(), headline='Django is cool',
...     content='Yeah.', reporter=r)
>>> a.save()

# Now the article is in the database.
>>> Article.objects.all()
<QuerySet [<Article: Django is cool>]>

# Article objects get API access to related Reporter objects.
>>> r = a.reporter
>>> r.full_name
'John Smith'

# And vice versa: Reporter objects get API access to Article objects.
>>> r.article_set.all()
<QuerySet [<Article: Django is cool>]>

# The API follows relationships as far as you need, performing efficient
# JOINs for you behind the scenes.
# This finds all articles by a reporter whose name starts with "John".
>>> Article.objects.filter(reporter__full_name__startswith='John')
<QuerySet [<Article: Django is cool>]>

# Change an object by altering its attributes and calling save().
>>> r.full_name = 'Billy Goat'
>>> r.save()

# Delete an object with delete().
>>> r.delete()
```

## 动态管理接口

```
mysite/news/models.py
from django.db import models

class Article(models.Model):
    pub_date = models.DateField()
    headline = models.CharField(max_length=200)
    content = models.TextField()
    reporter = models.ForeignKey(Reporter, on_delete=models.CASCADE)



mysite/news/admin.py

from django.contrib import admin

from . import models

admin.site.register(models.Article)
```

## 规划URLs
简洁优雅的 URL 规划对于一个高质量 Web 应用来说至关重要。Django 推崇优美的 URL 设计，所以不要把诸如 .php 和 .asp 之类的冗余的后缀放到 URL 里。

为了设计你自己的 URLconf ，你需要创建一个叫做 URLconf 的 Python 模块。这是网站的目录，它包含了一张 URL 和 Python 回调函数之间的映射表。URLconf 也有利于将 Python 代码与 URL 进行解耦（译注：使各个模块分离，独立）。

```
mysite/news/urls.py
from django.urls import path

from . import views

urlpatterns = [
    path('articles/<int:year>/', views.year_archive),
    path('articles/<int:year>/<int:month>/', views.month_archive),
    path('articles/<int:year>/<int:month>/<int:pk>/', views.article_detail),
]
```

上述代码将 URL 路径映射到了 Python 回调函数（“视图”）。路径字符串使用参数标签从URL中“捕获”相应值。当用户请求页面时，Django 依次遍历路径，直至初次匹配到了请求的 URL。(如果无匹配项，Django 会调用 404 视图。) 这个过程非常快，因为路径在加载时就编译成了正则表达式。

一旦有 URL 路径匹配成功，Django 会调用相应的视图函数。每个视图函数会接受一个请求对象——包含请求元信息——以及在匹配式中获取的参数值。

例如，当用户请求了这样的 URL "/articles/2005/05/39323/"，Django 会调用 news.views.article_detail(request, year=2005, month=5, pk=39323)。

## 编写视图
视图函数的执行结果只可能有两种：返回一个包含请求页面元素的 HttpResponse 对象，或者是抛出 Http404 这类异常。至于执行过程中的其它的动作则由你决定。

通常来说，一个视图的工作就是：从参数获取数据，装载一个模板，然后将根据获取的数据对模板进行渲染。下面是一个 year_archive 的视图样例：

```
mysite/news/views.py
from django.shortcuts import render

from .models import Article

def year_archive(request, year):
    a_list = Article.objects.filter(pub_date__year=year)
    context = {'year': year, 'article_list': a_list}
    return render(request, 'news/year_archive.html', context)
```

这个例子使用了 Django 模板系统 ，它有着很多强大的功能，而且使用起来足够简单，即使不是程序员也可轻松使用


<font color='red'>
每个视图函数至少要有一个参数，通常被叫作request。 这是一个触发这个视图、包含当前Web请求信息的对象，是类django.http.HttpRequest的一个实例。在这个示例中，我们虽然不用request做任何事情，然而它仍必须是这个视图的第一个参数。
</font>
## 设计模板

```
上面的代码加载了 news/year_archive.html 模板。

Django 允许设置搜索模板路径，这样可以最小化模板之间的冗余。在 Django 设置中，你可以通过 DIRS 参数指定一个路径列表用于检索模板。如果第一个路径中不包含任何模板，就继续检查第二个，以此类推。

让我们假设 news/year_archive.html 模板已经找到。它看起来可能是下面这个样子：

mysite/news/templates/news/year_archive.html
{% extends "base.html" %}

{% block title %}Articles for {{ year }}{% endblock %}

{% block content %}
<h1>Articles for {{ year }}</h1>

{% for article in article_list %}
    <p>{{ article.headline }}</p>
    <p>By {{ article.reporter.full_name }}</p>
    <p>Published {{ article.pub_date|date:"F j, Y" }}</p>
{% endfor %}
{% endblock %}
```

我们看到变量都被双大括号括起来了。
```
 {{ article.headline }}
 ```
  的意思是“输出 article 的 headline 属性值”。这个“点”还有更多的用途，比如查找字典键值、查找索引和函数调用。

我们注意到
```
 {{ article.pub_date|date:"F j, Y" }} 
 ```
 使用了 Unix 风格的“管道符”（“|”字符）。这是一个模板过滤器，用于过滤变量值。在这里过滤器将一个 Python datetime 对象转化为指定的格式（就像 PHP 中的日期函数那样）。

你可以将多个过滤器连在一起使用。你还可以使用你 自定义的模板过滤器 。你甚至可以自己编写 自定义的模板标签 ，相关的 Python 代码会在使用标签时在后台运行。

Django 使用了 ''模板继承'' 的概念。这就是 
```
{% extends "base.html" %}
```
 的作用。它的含义是''先加载名为 base 的模板，并且用下面的标记块对模板中定义的标记块进行填充''。简而言之，模板继承可以使模板间的冗余内容最小化：每个模板只需包含与其它文档有区别的内容。

下面是 base.html 可能的样子，它使用了 静态文件

```
mysite/templates/base.html
{% load static %}
<html>
<head>
    <title>{% block title %}{% endblock %}</title>
</head>
<body>
    <img src="{% static "images/sitelogo.png" %}" alt="Logo" />
    {% block content %}{% endblock %}
</body>
</html>
```
