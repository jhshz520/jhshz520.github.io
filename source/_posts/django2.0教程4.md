---
title: django2.0教程4
date: 2020-12-10 09:38:36
categories: 开发
tags: Django
---
# manage工具
现在让我们进入交互式 Python 命令行，尝试一下 Django 为你创建的各种 API。通过以下命令打开 Python 命令行：
```
$ python manage.py shell
```

我们使用这个命令而不是简单的使用 "Python" 是因为 manage.py 会设置 DJANGO_SETTINGS_MODULE 环境变量，这个变量会让 Django 根据 mysite/settings.py 文件来设置 Python 包的导入路径。

当你成功进入命令行后，来试试 database API 吧:

```
>>> from polls.models import Choice, Question  # Import the model classes we just wrote.

# No questions are in the system yet.
>>> Question.objects.all()
<QuerySet []>

# Create a new Question.
# Support for time zones is enabled in the default settings file, so
# Django expects a datetime with tzinfo for pub_date. Use timezone.now()
# instead of datetime.datetime.now() and it will do the right thing.
>>> from django.utils import timezone
>>> q = Question(question_text="What's new?", pub_date=timezone.now())

# Save the object into the database. You have to call save() explicitly.
>>> q.save()

# Now it has an ID.
>>> q.id
1

# Access model field values via Python attributes.
>>> q.question_text
"What's new?"
>>> q.pub_date
datetime.datetime(2012, 2, 26, 13, 0, 0, 775217, tzinfo=<UTC>)

# Change values by changing the attributes, then calling save().
>>> q.question_text = "What's up?"
>>> q.save()

# objects.all() displays all the questions in the database.
>>> Question.objects.all()
<QuerySet [<Question: Question object (1)>]>

等等。<Question: Question object (1)>对于我们了解这个对象的细节没什么帮助。让我们通过编辑 Question 模型的代码（位于 polls/models.py 中）来修复这个问题。给 Question 和 Choice 增加 __str__() 方法
```


**polls/models.py**
```
from django.db import models

class Question(models.Model):
    # ...
    def __str__(self):
        return self.question_text

class Choice(models.Model):
    # ...
    def __str__(self):
        return self.choice_text
给模型增加 __str__() 方法是很重要的，这不仅仅能给你在命令行里使用带来方便，Django 自动生成的 admin 里也使用这个方法来表示对象。
```


注意：这些都是常规的 Python方法。让我们添加一个自定义的方法，这只是为了演示：
```
polls/models.py
import datetime

from django.db import models
from django.utils import timezone


class Question(models.Model):
    # ...
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
```
新加入的 import datetime 和 from django.utils import timezone 分别导入了 Python 的标准 datetime 模块和 Django 中和时区相关的 django.utils.timezone 工具模块。如果你不太熟悉 Python 中的时区处理，看看 时区支持文档 吧。

保存文件然后通过 python manage.py shell 命令再次打开 Python 交互式命令行

```
>>> from polls.models import Choice, Question

# Make sure our __str__() addition worked.
>>> Question.objects.all()
<QuerySet [<Question: What's up?>]>

# Django provides a rich database lookup API that's entirely driven by
# keyword arguments.
>>> Question.objects.filter(id=1)
<QuerySet [<Question: What's up?>]>
>>> Question.objects.filter(question_text__startswith='What')
<QuerySet [<Question: What's up?>]>

# Get the question that was published this year.
>>> from django.utils import timezone
>>> current_year = timezone.now().year
>>> Question.objects.get(pub_date__year=current_year)
<Question: What's up?>

# Request an ID that doesn't exist, this will raise an exception.
>>> Question.objects.get(id=2)
Traceback (most recent call last):
    ...
DoesNotExist: Question matching query does not exist.

# Lookup by a primary key is the most common case, so Django provides a
# shortcut for primary-key exact lookups.
# The following is identical to Question.objects.get(id=1).
>>> Question.objects.get(pk=1)
<Question: What's up?>

# Make sure our custom method worked.
>>> q = Question.objects.get(pk=1)
>>> q.was_published_recently()
True

# Give the Question a couple of Choices. The create call constructs a new
# Choice object, does the INSERT statement, adds the choice to the set
# of available choices and returns the new Choice object. Django creates
# a set to hold the "other side" of a ForeignKey relation
# (e.g. a question's choice) which can be accessed via the API.
>>> q = Question.objects.get(pk=1)

# Display any choices from the related object set -- none so far.
>>> q.choice_set.all()
<QuerySet []>

# Create three choices.
>>> q.choice_set.create(choice_text='Not much', votes=0)
<Choice: Not much>
>>> q.choice_set.create(choice_text='The sky', votes=0)
<Choice: The sky>
>>> c = q.choice_set.create(choice_text='Just hacking again', votes=0)

# Choice objects have API access to their related Question objects.
>>> c.question
<Question: What's up?>

# And vice versa: Question objects get access to Choice objects.
>>> q.choice_set.all()
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>
>>> q.choice_set.count()
3

# The API automatically follows relationships as far as you need.
# Use double underscores to separate relationships.
# This works as many levels deep as you want; there's no limit.
# Find all Choices for any question whose pub_date is in this year
# (reusing the 'current_year' variable we created above).
>>> Choice.objects.filter(question__pub_date__year=current_year)
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>

# Let's delete one of the choices. Use delete() for that.
>>> c = q.choice_set.filter(choice_text__startswith='Just hacking')
>>> c.delete()
```



## djaogo管理工具
为你的员工或客户生成一个用户添加，修改和删除内容的后台是一项缺乏创造性和乏味的工作。因此，Django 全自动地根据模型创建后台界面。

Django 产生于一个公众页面和内容发布者页面完全分离的新闻类站点的开发过程中。站点管理人员使用管理系统来添加新闻、事件和体育时讯等，这些添加的内容被显示在公众页面上。Django 通过为站点管理人员创建统一的内容编辑界面解决了这个问题。

管理界面不是为了网站的访问者，而是为管理者准备的

### 创建管理员账号

```
$ python manage.py createsuperuser
```

键入你想要使用的用户名，然后按下回车键：

然后提示你输入想要使用的邮件地址：

最后一步是输入密码。你会被要求输入两次密码，第二次的目的是为了确认第一次输入的确实是你想要的密码

## 启动开发服务器

Django 的管理界面默认就是启用的。让我们启动开发服务器，看看它到底是什么样的。

如果开发服务器未启动，用以下命令启动它：

```
$ python manage.py runserver
```

 "http://127.0.0.1:8000/admin/" 。你应该会看见管理员登录界面
 因为 翻译 功能默认是开着的，所以登录界面可能会使用你的语言，取决于你浏览器的设置和 Django 是否拥有你语言的翻译

 你将会看到几种可编辑的内容：组和用户。它们是由 django.contrib.auth 提供的，这是 Django 开发的认证框架

 ## 向管理页面加入投票应用
 只需要做一件事：我们得告诉管理页面，问题 Question 对象需要被管理。打开 polls/admin.py 文件，把它编辑成下面这样：

 ```
 polls/admin.py
from django.contrib import admin

from .models import Question

admin.site.register(Question)
 ```

 ## 管理功能

 现在我们向管理页面注册了问题 Question 类。Django 知道它应该被显示在索引页里：

 点击 "Questions" 。现在看到是问题 "Questions" 对象的列表 "change list" 。这个界面会显示所有数据库里的问题 Question 对象，你可以选择一个来修改。这里现在有我们在上一部分中创建的 “What's up?” 问题。

 这个表单是从问题 Question 模型中自动生成的
不同的字段类型（日期时间字段 DateTimeField 、字符字段 CharField）会生成对应的 HTML 输入控件。每个类型的字段都知道它们该如何在管理页面里显示自己。
每个日期时间字段 DateTimeField 都有 JavaScript 写的快捷按钮。日期有转到今天（Today）的快捷按钮和一个弹出式日历界面。时间有设为现在（Now）的快捷按钮和一个列出常用时间的方便的弹出式列表。
页面的底部提供了几个选项：

保存（Save） - 保存改变，然后返回对象列表。
保存并继续编辑（Save and continue editing） - 保存改变，然后重新载入当前对象的修改界面。
保存并新增（Save and add another） - 保存改变，然后添加一个新的空对象并载入修改界面。
删除（Delete） - 显示一个确认删除页面。
如果显示的 “发布日期(Date Published)” 和你在 教程 1 里创建它们的时间不一致，这意味着你可能没有正确的设置 TIME_ZONE 。改变设置，然后重新载入页面看看是否显示了正确的值。

通过点击 “今天(Today)” 和 “现在(Now)” 按钮改变 “发布日期(Date Published)”。然后点击 “保存并继续编辑(Save and add another)”按钮。然后点击右上角的 “历史(History)”按钮。你会看到一个列出了所有通过 Django 管理页面对当前对象进行的改变的页面，其中列出了时间戳和进行修改操作的用户名：