---
title: Django模板
date: 2020-09-21 07:54:05
categories: 开发
tags: Django
---
# django模板相关的知识
## django自定义模板标签
代码写在app目录下的名为 tempaltetags的文件夹【必须叫这个名字】。这个文件夹必须是一个python的包，这个文件夹下一定要有 __init__.py的文件，最好直接创建 pythonpackage，将其命名为 tempaltetags，这样会自动包含 __init__.py文件。

自定义的模板标签或模板过滤器就放在这个包下的python模块中（python脚本文件）

app必须在 settings.py中被注册

settings.py文件中在【 INSTALLED_APPS】配置中加上app应用名 teacher

## url正则表达式
```
+ 有什么含义
* 表示匹配前一个字符零次或者无限次
+ 表示匹配前一个字符一次或者无限次#
```
## python format函数
{0}.format（"hello"）,在第一个位置替换hello

## django装饰器
装饰器可以在不改变函数代码和调用方式的情况下给函数添加心得功能。广泛应用与权限检验和缓存等场景，能够让代码变得更加干净更可读可维护。
### 权限认证 @login_required
```
login_required

from django.contrib.auth.decorators import logtin_required

@ login_required(login_url='accounts/login/')
def my_view(request):
    ...
```

作用执行视图函数前先检查用户是否通过登录身份认证，并将未登录的用户重定向到指定的登录url。其中login_url是可选参数，如果不设置，默认login_url是settings.py 里面
设置的LOGIN_URL,另一个可选参数是redirect_filed_name,默认值是'next'


```
@login_required(redirect_field_name='my_redirect_field')
def my_view(request):
    '''

```
该装饰器不会检查用户是否是is_active状态。想进一步限制登录验证成功的用户对某些视图函数的访问，需要更加强大的@user_passes_test装饰器
### 权限设置 @login_passes_test
作用是对登录用户对象的信息进行判断，只有通过测试的用户才能访问视图函数，不符合的用户会被跳转到指定的登录url

```
from django.contrib.auth.decorators import user_passes_test

def email_check(user):
    return user.email.endswith('@example.com')

  
@user_passes_test(email_check)
def my_view(request):
    ...
如果需要加可选参数，可以按如下方式使用。
@user_passes_test(email_check, login_url='/login/'):
def my_view(request):
    ...

```
一个匿名的用户没有email地址显然不能通过测试

能够起到两层校验的作用：1检查用户是否登录2检查用户是否符合某些条件，例如如果只允许is_active的登录用户访问某些视图
```
from django.contrib.auth.decorators import user_passes_test
@user_passes_test(lambda u: u.is_active)
def my_view(request):
    ...
```
### 权限验证 @permission_required
作用检查用户是否有特定的权限，第一个参数perm是权限名，必选，第二个参数是login_url为可选
permission_required(perm[,login_url=None,raise_exception=False])

检查用户是否有polls.can_vote的权限，没有定向到login_url

```
from django.contrib.auth.decorators import permission_required 

@permission_required('polls.can_vote',login_url='/login/')
def my_view(request):
    ...
```
## 缓存
缓存是装饰器重要场景，前提是对缓存进行了相关设置
### @cache_page
```
from django.views.decorators.cache import cache_page

@cache_page(60 * 15)
def my_view(request):
    ...
```
可以接受缓存的时间作为参数，比如设置为上述的15分钟
### @cache_control
用户面对两种缓存，私有缓存和公共缓存
缓存控制器可以区分私有和公共
```
from django.views.decorators.cache import cache_control

@cache_control(private=True)
def my_view(request):
# ...
```
该修饰器负责在后台发送相应的 HTTP 头部。还有一些其他方法可以控制缓存参数。 例如, HTTP 允许应用程序执行如下操作:

定义页面可以被缓存的最大时间。
指定某个缓存是否总是检查较新版本，仅当无更新时才传递所缓存内容。
```
from django.views.decorators.cache import cache_control

@cache_control(must_revalidate=True, max_age=3600)
def my_view(request):
# ...

```
缺省情况下，Django 的缓存系统使用所请求的路径(如blog/article/1)来创建其缓存键。这意味着不同用户请求同样路径都会得到同样的缓存版本，不考虑客户端user-agent, cookie和语言配置的不同, 除非你使用Vary头部通知缓存机制需要考虑请求头里的cookie和语言的不同。

要在 Django 完成这项工作，可使用便利的 vary_on_headers 视图装饰器。例如下面代码告诉Django读取缓存数据时需要同时考虑User-Agent和Cookie的不同。与此类似的装饰器还有@vary_on_cookie。

在 cache_control() 中，任何合法的Cache-Control HTTP 指令都是有效的。下面是完整列表
public=True
private=True
no_cache=True
no_transform=True
must_revalidate=True
proxy_revalidate=True
max_age=num_seconds
s_maxage=num_seconds
@vary_on_headers

### @vary_on_headers
缺省情况下，Django 的缓存系统使用所请求的路径(如blog/article/1)来创建其缓存键。这意味着不同用户请求同样路径都会得到同样的缓存版本，不考虑客户端user-agent, cookie和语言配置的不同, 除非你使用Vary头部通知缓存机制需要考虑请求头里的cookie和语言的不同。

### @never_cache
如果你想用头部完全禁掉缓存, 你可以使用@never_cache装饰器。如果你不在视图中使用缓存，服务器端是肯定不会缓存的，然而用户的客户端如浏览器还是会缓存一些数据，这时你可以使用never_cache禁用掉客户端的缓存。
```
from django.views.decorators.cache import never_cache

@never_cache
def myview(request):
# ...
```


## 其他常用装饰器
### @method_decorator
如果需要在基于类的视图上使用装饰器，我们需要使用到@method_decorator这个装饰器, 它的作用是将类伪装成函数方法
```
from django.contrib.auth.decorators import login_required
from django.utils.decorators import method_decoratorfrom django.views.generic import TemplateView

@method_decorator(login_required, name='dispatch')
class ProtectedView(TemplateView):
    template_name = 'secret.html'
```

### @require_http_methods
该装饰器的作用是限制用户的请求方法。如下例中仅接收GET和POST方法。与此类似的装饰器还有@require_POST, @require_GET和@require_safe
```
from django.views.decorators.http import require_http_methods

@require_http_methods(["GET", "POST"])
def my_view(request):
    # Only accept GET or POST method
    pass
```

### @gzip_page
该装饰器可以压缩内容，前提是用户客户端允许内容压缩的话。使用方法如下:
```
from django.views.decorators.gzip import gzip_page

@gzip_page
def my_view(request):
    # Only accept GET or POST method
    pass
```
## 模板注释
```
单行注释

{# This is a comment #}
多行注释

{% comment %}
This is a
multi-line comment.
{% endcomment %}


```