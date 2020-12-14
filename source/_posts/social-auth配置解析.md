---
title: social-auth配置解析
date: 2020-09-22 19:55:31
categories: 项目开发
tags: python
---
# 在settings设置安装apps
```
INSTALLED_APPS = (
    ...
    'social_django',
    ...
)
```
# 配置认证后端
```
    'social_core.backends.open_id.OpenIdAuth',
    'social_core.backends.google.GoogleOpenId',
    'social_core.backends.google.GoogleOAuth2',
    'social_core.backends.google.GoogleOAuth',
    'social_core.backends.twitter.TwitterOAuth',
    'social_core.backends.yahoo.YahooOpenId',
    ...
    'django.contrib.auth.backends.ModelBackend',
)
```
# 添加urls
```
    ...
    url('', include('social_django.urls', namespace='social'))
    ...
)
```
如果需要可以添加命名空间
```
SOCIAL_AUTH_URL_NAMESPACE = 'social'
```

# 模板设置
```   
 {
        ...
        'OPTIONS': {
            ...
            'context_processors': [
                ...
                'social_django.context_processors.backends',
                'social_django.context_processors.login_redirect',
                ...
            ]
        }
    }

```
# 个性化的流水线设置
```

social_details - Get the information we can about the user and return it in a simple format to create the user instance later. On some cases the details are already part of the auth response from the provider, but sometimes this could hit a provider API.

social_uid - Get the social uid from whichever service we’re authing thru. The uid is the unique identifier of the given user in the provider.

auth_allowed - Verifies that the current auth process is valid within the current project, this is where emails and domains whitelists are applied (if defined).

social_user - Checks if the current social-account is already associated in the site.

get_username- Make up a username for this person, appends a random string at the end if there’s any collision.

create_user - Create a user account if we haven’t found one yet.

associate_user - Create the record that associated the social account with this user.

extra_data - Populate the extra_data field in the social record with the values specified by settings (and the default ones like access_token, etc).

user_details - Update the user record with any changed info from the auth service.

```
# social-auth-pipline 示例
```
SOCIAL_AUTH_PIPELINE = (
    'social_core.pipeline.social_auth.social_details',
    'social_core.pipeline.social_auth.social_uid',
    'social_core.pipeline.social_auth.social_user',
    'social_core.pipeline.user.get_username',
    'social_core.pipeline.social_auth.associate_by_email',
    'social_core.pipeline.user.create_user',
    'social_core.pipeline.social_auth.associate_user',
    'social_core.pipeline.social_auth.load_extra_data',
    'social_core.pipeline.user.user_details',
)
```

# python 中的self
 相当于java中的this，表示示例本身

 ## github backend 是怎么实现重定向的
 在login.html页面，点击github登录的按钮
 github 连接反向解析
 ```
 <a class="social-button" id="google-connect" href="{% url 'social:begin' 'google-oauth2' %}?next={{ domain }}{{ request.path }}
 ```

 由于urls.py 中有将所有的social_urls include在同一个命名空间内，所以要找到backend，取出backend的名字

 先看login函数 
 ```
 def login(request, message=None):
    """login will either show the user a button to login with github, and then a link
       to their collections (given storage is set up) or a link to connect storage (if it 
       isn't)
    """
    if message is not None:
        messages.info(request, message)

    context = None
    if request.user.is_authenticated:
        if not request.user.agree_terms:
            return render(request, "terms/usage_agreement_login.html", context)
        context = validate_credentials(user=request.user)
    return render(request, "social/login.html", context)

 ```
 login函数是看request中是否含有user信息，如果有user信息并且该user已经被授权登录，那么将这个request中的context填充到登录页面中
 如果该用户没有被授权，就将该用户的context等信息返回到terms/usage_agreement_login.html

 接下来看social_user函数
 ```
 def social_user(backend, uid, user=None, *args, **kwargs):
   """OVERRIDED: It will give the user an error message if the
       account is already associated with a username."""
    provider = backend.name
    social = backend.strategy.storage.user.get_social_auth(provider, uid)

    if social:
        if user and social.user != user:
            msg = "This {0} account is already in use.".format(provider)
            return login(request=backend.strategy.request, message=msg)
            # raise AuthAlreadyAssociated(backend, msg)
        elif not user:
            user = social.user

    return {
        "social": social,
        "user": user,
        "is_new": user is None,
        "new_association": social is None,
    }
 ```
如果得到的用户已经被绑定了，那么应该给用户一个提示信息
 这个函数的参数是backend，uid，user和两个看不懂的参数，provider用来保存backend的名字，social通过backend.strategy.user.get_social_auth函数得到social

 函数的返回时一个字典，分别是social，user，is——new new——association

 谁调用了这个social_user 函数呢？



 ## social_auth_pipeline
 ```
  "social_core.pipeline.social_auth.social_details", # 看一下social_auth中social的详细信息
    "social_core.pipeline.social_auth.social_uid",# 得到social_auth的uid
    "social_core.pipeline.social_auth.auth_allowed",# 是否允许
    "shub.apps.users.views.social_user",# 本项目中的social_user函数
    "shub.apps.users.views.redirect_if_no_refresh_token",# 本项目中的social重定向刷新特别是google
    "social_core.pipeline.user.get_username",#得到用户名
    "social_core.pipeline.social_auth.associate_by_email",  # <--- must share same email
    "social_core.pipeline.user.create_user",#新建一个user
    "social_core.pipeline.social_auth.associate_user",#与user联系
    "social_core.pipeline.social_auth.load_extra_data",#载入抽取的信息
    "social_core.pipeline.user.user_details",# 用户的详细信息
 ```

 ### social_core.pipeline.social_auth.social_details
 ```
 返回一个字典

 return {'details':dict(backend.get_user_details(response),**details)}
 ```
 ### social_core.pipeline.social_auth.social_uid
 ```
 return {'uid':backend.get_user_id(details,response)}
 ```
 ### social_core.pipeline.social_auth.auth_allowed
 ```
 if not backend.auth_allowed(response,details):
    raise AuthForbidden(backend)
 ```
 ### shub.apps.users.views.social_user
```
原social_user

def social_user(backend,uid,user=None,*args,**kwargs):
    provide = backend.name
    social = backend.strategy.storage.user.get_social_auth(provider,id)
    if social:
        if user and social.user!=user:
            msg = 'This account is already in use'
            raise AuthAlreadyAssociated(backend,msg)
        elif not user:
            user = social.user;
        return {'social':social,
                'user':user,
                'is_new':user is None,
                'new_association' :social is None

        }
```
 ### social_core.pipeline.user.get_username


# github 的重定向url
```
点击login with github后跳转
https://github.com/login?client_id=776ca8fdb3919f16e2f8&return_to=%2Flogin%2Foauth%2Fauthorize%3Fclient_id%3D776ca8fdb3919f16e2f8%26redirect_uri%3Dhttp%253A%252F%252Fwww.hustshub.cn%252Fcomplete%252Fgithub%252F%26response_type%3Dcode%26scope%3Drepo%252Cuser%26state%3D4ZDP9Ug7hEoce1a001zLnO1XywIZUw7U

输入密码，用户后

https://github.com/login/oauth/authorize?scope=repo,user&client_id=776ca8fdb3919f16e2f8&state=EgnOpSAlCCcr7H0SGNQwlopRANLJfpxL&response_type=code&redirect_uri=http://www.hustshub.cn/complete/github/

https://github.com/login/oauth/authorize?client_id=776ca8fdb3919f16e2f8&redirect_uri=http%3A%2F%2Fwww.hustshub.cn%2Fcomplete%2Fgithub%2F&response_type=code&scope=repo%2Cuser&state=4ZDP9Ug7hEoce1a001zLnO1XywIZUw7U

我们应该跳转的第三方url
https://login.cngrid.org/oauth/oauth2/auth?max_age=0&scope=openid&response_type=code&redirect_uri=http://hustshub.cn/login&state=mvXXDydoWTmYbpIKAIYyGEAm&nonce=tyAMtoznlXGVdldPYqbNxDvM&client_id=auth_husthub&audience=&prompt=#
```

# 我决定重写一些方法，尤其是url的拼接还没有看懂是怎么回事



# GithubOAuth2 所含的方法
```
参数：
name 
API——URL
AUTHORIZAION_URL
ACCESS_TOKEN_URL
ACCESS_TOKEN_METHOD = 'POST'
SCOPE_SEPARATER = ','
REDIRECT_STATE = False
STATE_PARAMETER = True
SEND_USER_AGENT = True #这个目前还不知道什么意思

def api-url（self） 得到api url
def get_user_details（self,response）
def user_date(self,access_token,*args,**kwargs) 从服务器获取用户数据,实际去调用_user_date
def _user_date() 利用url拼接去请求服务器，参数是access_token,设置了请求头
```

## base.py
```
def setting（） # 调用self.strategy.setting(name,default =default,backend =self)

def start() # #


```





# 过程顺序
1 点击跳转的登录按钮
反向解析social begin 解析到应该调用 social_django 中的urls
然后因为是/login/grdi 开头便开始与backend 为grid的Oauth进行适配，然后调用social——auth——core
的 Actions 下的do_auth 方法，查看如果redirect_name 在 date 中就调用backend.start() 方法

## githubOauth2中的start（）方法 并没有找到，因此去调用最基本的base.py 中的 oauth的start() 方法
```
start 方法的定义如下

def start(self):
    if self.user_redirect():
        return self.strategy.redirect(self.auth_url()) 方法
    else：
        return self.strategy.html(self.auth_html()) 

```
## 然后我们继续深入
    寻找strategy中的redirect
```
Oauth.py中有auth_url()的定义
    state = self.get_or_create_state()
    params = self.auth_params(state)
    params.update(self.get_scope_argument())
    params.update(self.auth_extra_arguments())
    params =  urlencode(params)
    if not self.REDIRECT_STATE:
        params = unquote(params)
    return '{0}?{1}'.format(self.authorization_url(),params)
```

## linux怎么刷新本地的dns缓存
```
sudo /etc/init.d/networking restart # 不管用

sudo  /etc/init.d/dns-clean start
```

## shub/uers/views/auth.py中的logout函数
```
from django.contrib.auth import logout as auth_logout


django包的__init__py下面定义的
```

**python getattr**
```
getattr() 函数用于返回一个对象属性值

getattr(object, name[, default])

object -- 对象。
name -- 字符串，对象属性。
default -- 默认返回值，如果不提供该参数，在没有对应属性时，将触发 AttributeError
```
**python hasattar**

```
```
