---
title: django2.0教程3
date: 2020-12-10 09:24:47
categories: 开发
tags: Django
---
# 数据库配置
现在，打开 mysite/settings.py 。这是个包含了 Django 项目设置的 Python 模块。

通常，这个配置文件使用 SQLite 作为默认数据库。如果你不熟悉数据库，或者只是想尝试下 Django，这是最简单的选择。Python 内置 SQLite，所以你无需安装额外东西来使用它。当你开始一个真正的项目时，你可能更倾向使用一个更具扩展性的数据库，例如 PostgreSQL，避免中途切换数据库这个令人头疼的问题。

如果你想使用其他数据库，你需要安装合适的 database bindings ，然后改变设置文件中 DATABASES 'default' 项目中的一些键值：

ENGINE -- 可选值有
```
 'django.db.backends.sqlite3'，'django.db.backends.postgresql'，'django.db.backends.mysql'，或 'django.db.backends.oracle'。其它 可用后端。
```
NAME - 数据库的名称。如果使用的是 SQLite，数据库将是你电脑上的一个文件，在这种情况下， NAME 应该是此文件的绝对路径，包括文件名。默认值 os.path.join(BASE_DIR, 'db.sqlite3') 将会把数据库文件储存在项目的根目录。
如果你不使用 SQLite，则必须添加一些额外设置，比如 USER 、 PASSWORD 、 HOST 等等。想了解更多数据库设置方面的内容，请看文档：DATABASES 。

如果你使用 SQLite，那么你不需要在使用前做任何事——数据库会在需要的时候自动创建

编辑 mysite/settings.py 文件前，先设置 TIME_ZONE 为你自己时区。

此外，关注一下文件头部的 INSTALLED_APPS 设置项。这里包括了会在你项目中启用的所有 Django 应用。应用能在多个项目中使用，你也可以打包并且发布应用，让别人使用它们。

通常， INSTALLED_APPS 默认包括了以下 Django 的自带应用：

```
django.contrib.admin -- 管理员站点， 你很快就会使用它。
django.contrib.auth -- 认证授权系统。
django.contrib.contenttypes -- 内容类型框架。
django.contrib.sessions -- 会话框架。
django.contrib.messages -- 消息框架。
django.contrib.staticfiles -- 管理静态文件的框架。
这些应用被默认启用是为了给常规项目提供方便。
```

默认开启的某些应用需要至少一个数据表，所以，在使用他们之前需要在数据库中创建一些表。请执行以下命令：

```
$ python manage.py migrate
```

这个 migrate 命令检查 INSTALLED_APPS 设置，为其中的每个应用创建需要的数据表，至于具体会创建什么，这取决于你的 mysite/settings.py 设置文件和每个应用的数据库迁移文件（我们稍后会介绍这个）。这个命令所执行的每个迁移操作都会在终端中显示出来。如果你感兴趣的话，运行你数据库的命令行工具，并
```
输入 \dt (PostgreSQL)， SHOW TABLES; (MySQL)， .schema (SQLite)或者 SELECT TABLE_NAME FROM USER_TABLES; (Oracle) 来看看 Django 到底创建了哪些表。
```
为了方便大多数项目，我们默认激活了一些应用，但并不是每个人都需要它们。如果你不需要某个或某些应用，你可以在运行 migrate 前毫无顾虑地从 INSTALLED_APPS 里注释或者删除掉它们。 migrate 命令只会为在 INSTALLED_APPS 里声明了的应用进行数据库迁移。

在这个简单的投票应用中，需要创建两个模型：问题 Question 和选项 Choice。Question 模型包括问题描述和发布时间。Choice 模型有两个字段，选项描述和当前得票数。每个选项属于一个问题。

这些概念可以通过一个简单的 Python 类来描述。按照下面的例子来编辑 polls/models.py 文件

```
polls/models.py
from django.db import models


class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')


class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```
代码非常直白。每个模型被表示为 django.db.models.Model 类的子类。每个模型有一些类变量，它们都表示模型里的一个数据库字段。

每个字段都是 Field 类的实例 - 比如，字符字段被表示为 CharField ，日期时间字段被表示为 DateTimeField 。这将告诉 Django 每个字段要处理的数据类型。

每个 Field 类实例变量的名字（例如 question_text 或 pub_date ）也是字段名，所以最好使用对机器友好的格式。你将会在 Python 代码里使用它们，而数据库会将它们作为列名。

你可以使用可选的选项来为 Field 定义一个人类可读的名字。这个功能在很多 Django 内部组成部分中都被使用了，而且作为文档的一部分。如果某个字段没有提供此名称，Django 将会使用对机器友好的名称，也就是变量名。在上面的例子中，我们只为 Question.pub_date 定义了对人类友好的名字。对于模型内的其它字段，它们的机器友好名也会被作为人类友好名使用。

定义某些 Field 类实例需要参数。例如 CharField 需要一个 max_length 参数。这个参数的用处不止于用来定义数据库结构，也用于验证数据，我们稍后将会看到这方面的内容。

Field 也能够接收多个可选参数；在上面的例子中：我们将 votes 的 default 也就是默认值，设为0。

注意在最后，我们使用 ForeignKey 定义了一个关系。这将告诉 Django，每个 Choice 对象都关联到一个 Question 对象。Django 支持所有常用的数据库关系：多对一、多对多和一对一。

## 激活模型
上面的一小段用于创建模型的代码给了 Django 很多信息，通过这些信息，Django 可以：

为这个应用创建数据库 schema（生成 CREATE TABLE 语句）。
创建可以与 Question 和 Choice 对象进行交互的 Python 数据库 API。
但是首先得把 polls 应用安装到我们的项目里。

Django 应用是“可插拔”的。你可以在多个项目中使用同一个应用。除此之外，你还可以发布自己的应用，因为它们并不会被绑定到当前安装的 Django 上。

为了在我们的工程中包含这个应用，我们需要在配置类 INSTALLED_APPS 中添加设置。因为 PollsConfig 类写在文件 polls/apps.py 中，所以它的点式路径是 'polls.apps.PollsConfig'。在文件 mysite/settings.py 中 INSTALLED_APPS 子项添加点式路径后，它看起来像这样：

```
mysite/settings.py
INSTALLED_APPS = [
    'polls.apps.PollsConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]

```

现在你的 Django 项目会包含 polls 应用。接着运行下面的命令：
```

$ python manage.py makemigrations polls
```
你将会看到类似于下面这样的输出：
```
Migrations for 'polls':
  polls/migrations/0001_initial.py:
    - Create model Choice
    - Create model Question
    - Add field question to choice
```

通过运行 makemigrations 命令，Django 会检测你对模型文件的修改（在这种情况下，你已经取得了新的），并且把修改的部分储存为一次 迁移。

迁移是 Django 对于模型定义（也就是你的数据库结构）的变化的储存形式 - 没那么玄乎，它们其实也只是一些你磁盘上的文件。如果你想的话，你可以阅读一下你模型的迁移数据，它被储存在 polls/migrations/0001_initial.py 里。别担心，你不需要每次都阅读迁移文件，但是它们被设计成人类可读的形式，这是为了便于你手动修改它们。

Django 有一个自动执行数据库迁移并同步管理你的数据库结构的命令 - 这个命令是 migrate，我们马上就会接触它 , 但是首先，让我们看看迁移命令会执行哪些 SQL 语句。sqlmigrate 命令接收一个迁移的名称，然后返回对应的 SQL：
```

$ python manage.py sqlmigrate polls 0001
```

你将会看到类似下面这样的输出（我把输出重组成了人类可读的格式）：
```
BEGIN;
--
-- Create model Choice
--
CREATE TABLE "polls_choice" (
    "id" serial NOT NULL PRIMARY KEY,
    "choice_text" varchar(200) NOT NULL,
    "votes" integer NOT NULL
);
--
-- Create model Question
--
CREATE TABLE "polls_question" (
    "id" serial NOT NULL PRIMARY KEY,
    "question_text" varchar(200) NOT NULL,
    "pub_date" timestamp with time zone NOT NULL
);
--
-- Add field question to choice
--
ALTER TABLE "polls_choice" ADD COLUMN "question_id" integer NOT NULL;
ALTER TABLE "polls_choice" ALTER COLUMN "question_id" DROP DEFAULT;
CREATE INDEX "polls_choice_7aa0f6ee" ON "polls_choice" ("question_id");
ALTER TABLE "polls_choice"
  ADD CONSTRAINT "polls_choice_question_id_246c99a640fbbd72_fk_polls_question_id"
    FOREIGN KEY ("question_id")
    REFERENCES "polls_question" ("id")
    DEFERRABLE INITIALLY DEFERRED;

COMMIT;
```

请注意以下几点：
输出的内容和你使用的数据库有关，上面的输出示例使用的是 PostgreSQL。
数据库的表名是由应用名(polls)和模型名的小写形式( question 和 choice)连接而来。（如果需要，你可以自定义此行为。）
主键(IDs)会被自动创建。(当然，你也可以自定义。)
默认的，Django 会在外键字段名后追加字符串 "_id" 。（同样，这也可以自定义。）
外键关系由 FOREIGN KEY 生成。你不用关心 DEFERRABLE 部分，它只是告诉 PostgreSQL，请在事务全都执行完之后再创建外键关系。
生成的 SQL 语句是为你所用的数据库定制的，所以那些和数据库有关的字段类型，比如 auto_increment (MySQL)、 serial (PostgreSQL)和 integer primary key autoincrement (SQLite)，Django 会帮你自动处理。那些和引号相关的事情 - 例如，是使用单引号还是双引号 - 也一样会被自动处理。
这个 sqlmigrate 命令并没有真正在你的数据库中的执行迁移 - 它只是把命令输出到屏幕上，让你看看 Django 认为需要执行哪些 SQL 语句。这在你想看看 Django 到底准备做什么，或者当你是数据库管理员，需要写脚本来批量处理数据库时会很有用。
如果你感兴趣，你也可以试试运行 python manage.py check ;这个命令帮助你检查项目中的问题，并且在检查过程中不会对数据库进行任何操作。

现在，再次运行 migrate 命令，在数据库里创建新定义的模型的数据表：
```
$ python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, polls, sessions
Running migrations:
  Rendering model states... DONE
  Applying polls.0001_initial... OK
```
这个 migrate 命令选中所有还没有执行过的迁移（Django 通过在数据库中创建一个特殊的表 django_migrations 来跟踪执行过哪些迁移）并应用在数据库上 - 也就是将你对模型的更改同步到数据库结构上。

迁移是非常强大的功能，它能让你在开发过程中持续的改变数据库结构而不需要重新删除和创建表 - 它专注于使数据库平滑升级而不会丢失数据。我们会在后面的教程中更加深入的学习这部分内容，现在，你只需要记住，改变模型需要这三步：
```
编辑 models.py 文件，改变模型。
运行 python manage.py makemigrations 为模型的改变生成迁移文件。
运行 python manage.py migrate 来应用数据库迁移。
数据库迁移被分解成生成和应用两个命令是为了让你能够在代码控制系统上提交迁移数据并使其能在多个应用里使用；这不仅仅会让开发更加简单，也给别的开发者和生产环境中的使用带来方便。
```
通过阅读文档 Django 后台文档 ，你可以获得关于 manage.py 工具的更多信息

