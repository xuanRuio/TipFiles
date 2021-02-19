

# **Django Project**

---



## 环境准备

- Django3

- python3

- pyCharm

- 虚拟环境

  **virtualenvwrapper**【用来管理virtualenv的扩展包，方便env虚拟环境管理。】

  - 安装

  ```python
  pip install virtualenvwrapper
  ```

  - 创建虚拟环境

  ``` python
  mkvirtualenv [env name]
  ```

  - 创建指定解释器的虚拟环境

  ```python
  mkvirtualenv -p [python version] [env name]
  ```

  - 启动虚拟环境

  ``` python
  workon [env name]
  ```

  - 退出虚拟环境

  ```python
  deactivate
  ```

  - 删除虚拟环境

    ```python
    rmvirtualenv [env name]
    ```

  - 虚拟环境存储路径更改

    详见链接 https://blog.csdn.net/a200822146085/article/details/89048172

---



## 立项

- 建立远程仓库

  Github

  Gitee

  码云

- 克隆远程仓库

  ```python
  git clone  https://github.com/xxxx.git
  ```

- 创建Django工程

  ```python
  django-admin startproject [project name]
  ```

- 启动服务

  ```python
  **python manage.py runserver**
  python manage.py runserver 0.0.0.0:8000     默认本地8000端口
  python manager.py runserver --host 0.0.0.0 --port 9008
  ```

---



## 项目目录配置

- apps

  项目子应用存储

- libs

  项目第三方包存储

- templates

  项目模板存储

- utils

  项目工具包

- logs

  记录日志

- settings

  文件夹，用于存储配置文件

  

---



## settings配置

- 创建settings包

- 复制settings文件

- 粘贴为dev和prod两个文件至settings包中

  分别对应测试和生产环境的配置文件

  **注意：settings文件中的BASE_DIR需要重新定义，此步也可上线前部署**

---

## Jinja2模板配置

- utils 包中创建 jinja2_env.py
- 粘贴如下代码

```python
from jinja2 import Environment
from django.contrib.staticfiles.storage import staticfiles_storage
from django.urls import reverse

def environment(**options):
    env = Environment(**options)
    env.globals.update({
        'static': staticfiles_storage.url,
        'url': reverse,
    })
    return env
```

- 进入settings/dev中的TEMPLATES列表，添加Jinja2模板环境配置字典，添加在Django自带模板的上方
- 代码如下

```python
# Jinja2 模板
    {
        'BACKEND': 'django.template.backends.jinja2.Jinja2',
        'DIRS': [os.path.join(BASE_DIR, 'templates')],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
            # 补充Jinja2模板引擎环境
            'environment': 'utils.jinja2_env.environment',
        },
    },
```

---



## Redis配置（windows）

- 安装redis

  window版下载地址：https://github.com/MicrosoftArchive/redis/releases

- 进入解压包执行命令

```python
redis-server.exe --service-install redis.windows.conf --loglevel verbose
```

- 启动服务命令

```python
redis-server.exe  --service-start
```

- 关闭服务命令

```python
redis-server.exe  --service-stop
```

- 连接redis服务

```python
redis-cli.exe -h 127.0.0.1 -p 6379
```

- redis操作指令

  详见：https://www.cnblogs.com/toutou/p/redis_command.html

- Django中的Redis配置

  - django-redis 包
  - django工程中的settings配置如下

```python
# Redis配置
CACHES = {
    # 默认为0号数据库
    "default": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/0",
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
        }
    },
    # session为1号数据库
    "session": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/1",
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
        }
    },
}
SESSION_ENGINE = "django.contrib.sessions.backends.cache"
SESSION_CACHE_ALIAS = "session"
# 配置session存储3种方式
# 1.存储在数据库中，如下设置可写可不写，是默认存储模式
# SESSION_ENGINE = "django.contrib.sessions.backends.db"
# 2.存储在缓存中，存储在本机内存中，如果丢失则不能找回，比数据库的方式读写更快
# SESSION_ENGINE = "django.contrib.sessions.backends.cache"
# 3.混合存储：优先从本机内存中存取，如果没有则冲数据库中存取

```

---



## Mysql配置

- pymysql 包
- django工程中的settings配置如下

```python
# MySql 配置
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'HOST': '127.0.0.1',  # 数据库主机
        'PORT': 3306,  # 数据库端口
        'USER': 'root',  # 数据库用户名
        'PASSWORD': 'xuanRui',  # 数据库用户密码
        'NAME': 'django_demo'  # 数据库名字
    }
}
```

- 项目同名文件__init__.py中添加

  ```python 
  import  pymysql
  pymysql.version_info = (1, 4, 13, "final", 0)
  pymysql.install_as_MySQLdb()
  ```

- ```
  settings 文件注册
  """
  注册 User 模型类 替换系统自带的User模块
  # AUTH_USER_MODEL = '子应用的名称.模型类名字'
  """
  AUTH_USER_MODEL = 'users.User'
  ```

- 新建数据库

```python
create database [database name] charset=utf8 ;   (djangomall
```

- 注意权限，新建用户

```python
create user [user name] identified by [password] ;  (u:xuanRui  p:djangomall
```

- 将新建的数据库分配给用户

```python
grant all on [database name].* to '[user name]'@'%' ;
```

- 刷新权限

```python
 flush privileges ;
```

- mysql操作

  ```python
  一、清除mysql表中数据
  
  delete from 表名;
  truncate table 表名;
  不带where参数的delete语句可以删除mysql表中所有内容，使用truncate table也可以清空mysql表中所有内容。
  效率上truncate比delete快，但truncate删除后不记录mysql日志，不可以恢复数据。
  delete的效果有点像将mysql表中所有记录一条一条删除到删完，
  而truncate相当于保留mysql表的结构，重新创建了这个表，所有的状态都相当于新表。
  
  二、删除表中的某些数据
  
  delete from命令格式：delete from 表名 where 表达式
  ```

- 如下报错:

```python
raise ImproperlyConfigured('mysqlclient 1.4.0 or newer is required; you have %s.' % Database.__version__)
django.core.exceptions.ImproperlyConfigured: mysqlclient 1.4.0 or newer is required; you have 0.10.0.
```

> 解决：在工程文件的__init__.py中加上代码用来指定版本
>
> ```
> import  pymysql
> pymysql.version_info = (1, 4, 13, "final", 0)
> pymysql.install_as_MySQLdb()
> ```

---

## 静态文件配置

- 新建static文件夹（前端材料）
- setting(dev)中配置

```python
STATIC_URL = '/static/'
STATICFILES_DIRS = [os.path.join(BASE_DIR,'static')]
```

---

## 日志配置

- 新建logs文件夹
- setting(dev)中配置

```python
# 日志文件配置
LOGGING = {
    # 版本
    'version': 1,
    # 是否禁用已存在的日志器
    'disable_existing_loggers': False,
    'formatters': {
        'verbose': {
            'format': '%(levelname)s %(asctime)s %(module)s %(lineno)d %(message)s'
        },
        'simple': {
            'format': '%(levelname)s %(module)s %(lineno)d %(message)s'
        },
    },
    # 过滤器
    'filters': {
        'require_debug_true': {
            '()': 'django.utils.log.RequireDebugTrue',
        },
    },
    'handlers': {
        # 终端输出
        'console': {
            'level': 'DEBUG',
            'filters': ['require_debug_true'],
            'class': 'logging.StreamHandler',
            'formatter': 'simple'
        },
        # 文件输出
        'file': {
            'level': 'INFO',
            'class': 'logging.handlers.RotatingFileHandler',
            'filename': os.path.join(BASE_DIR, "logs/.log"),  # 日志文件的位置
            'maxBytes': 300 * 1024 * 1024,
            'backupCount': 10,
            'formatter': 'verbose'
        },
    },
    # 日志器
    'loggers': {
        'django': {  # 定义了一个名为django的日志器
            'handlers': ['console', 'file'],
            'propagate': True,  # 追加模式
            'level': 'INFO',  # 日志器接收的最低日志级别
        },
    }
}
# 实例化 单例模式
import logging
LOGGER = logging.getLogger("django")

```

- 配置完成后，启动服务，logs文件夹中会出现日志文件

---

##  路由配置

- url        path()      re_path()      include()

  > **url是Django 1.x中的写法,在Django2.1中，开始舍弃Django1.x中的url写法。在Django2.x中，描写url配置的有两个函数path和re_path，re_path()函数可以看做是django 1.x中得url函数,即可以在路径中使用正则。**



- path()函数内置转化器

  str：匹配任何非空字符串，但不含斜杠/，如果你没有专门指定转换器，那么这个是默认使用的； 

  int：匹配0和正整数，返回一个int类型 

  slug：可理解为注释。该转换器匹配任何ASCII字符以及连接符和下划线，比如’ building-your-1st-django-site‘；

   uuid：匹配一个uuid格式的对象。为了防止冲突，规定必须使用破折号，所有字母必须小写，例如’075194d3-6885-417e-a8a8-6c931e272f00‘ 。返回一个UUID对象；

   path：匹配任何非空字符串，重点是可以包含路径分隔符’/‘。这个转换器可以帮助你匹配整个url而不是一段一段的url字符串。

- inclue()   三种用法

  1。在任何时候，urlpatterns都可以“include”其他URLconf模块。这本质上是一组位于其他url之下的“roots”。

  ```python
  from django.urls import include, path
  
  urlpatterns = [
      path('community/', include('aggregator.urls')),
      path('contact/', include('contact.urls')),
  ]
  ```

  每当Django遇到时include()，它都会截断直到该处匹配的URL的任何部分，并将剩余的字符串发送到包含的URLconf中以进行进一步处理。

  

  2。另一种可能性是通过使用path()实例列表包括其他URL模块 。例如，考虑以下URLconf：

  ```python
  from django.urls import include, path
  
  from apps.main import views as main_views
  from credit import views as credit_views
  
  extra_patterns = [
      path('reports/', credit_views.report),
      path('reports/<int:id>/', credit_views.report),
      path('charge/', credit_views.charge),
  ]
  
  urlpatterns = [
      path('', main_views.homepage),
      path('help/', include('apps.help.urls')),
      path('credit/', include(extra_patterns)),
  ]
  ```

  /credit/reports/将由credit_views.report()视图处理 。

  

  3 。可用于从URLconf中删除重复使用单个模式前缀的冗余。

  ```python
  from django.urls import path
  from . import views
  
  urlpatterns = [
      path('<page_slug>-<page_id>/history/', views.history),
      path('<page_slug>-<page_id>/edit/', views.edit),
      path('<page_slug>-<page_id>/discuss/', views.discuss),
      path('<page_slug>-<page_id>/permissions/', views.permissions),
  ]
  ```

  可通过仅说明一次公共路径前缀并对不同的后缀进行分组：

  ```python
  from django.urls import include, path
  from . import views
  
  urlpatterns = [
      path('<page_slug>-<page_id>/', include([
          path('history/', views.history),
          path('edit/', views.edit),
          path('discuss/', views.discuss),
          path('permissions/', views.permissions),
      ])),
  ]
  ```

**详见：https://blog.csdn.net/weixin_44870139/article/details/105565242**

- 反向解析
  - Django1.x 和 Django2.x 存在一定区别
  - Django2.x

```python
# 首页（主路由urls）
    path('', include(('apps.contents.urls','contents'), namespace="contents")),
	                      子路由         子路由名字（一般保持与主路由相同） 主路由名字
# （视图函数）
	。。。
    return redirect(reverse('contents:index'))
						 主路由名    子路由名
    
### 建议使用如下方法：
    - 1.子路由urls文件中路由列表前添加 app_name = "index"
    - 2.需要反向解析的子路由入口后添加name参数 
     如下：
app_name = 'index'
urlpatterns = [
    path('', views.IndexView.as_view(),name='index_page'),
]
return redirect(reverse('index:index_page'))  即可反向解析
```



---

## 添加子应用

- 添加app

``` python
python manage.py startapp [appname]
```

- 注册app

  1。进入Django工程目录下的settings.py文件   -->    2。找到INSTALL_APPS进行注册

  [ 找到 app1 中的 apps 文件。 系统默认生成一个 config 的 class ，复制类名（这里是 AppConfig），按照文件定位书写]

  - 如下注册规则：**appname.apps.AppnameConfig**

---



## 前后端交互流程

- render函数

  django后台函数 - jinjia2模板变量 - js获取变量 - vue

---

## 模型类

### 自定义用户模型类

- apps.users.model.py

```python
from django.db import models
from django.contrib.auth.models import AbstractUser
class User(AbstractUser):
    # django自带 username password (加解密)
    # 添加字段 手机号 mobile
    mobile = models.CharField(max_length=11, unique=True, verbose_name="手机号")
    class Meta:
        # 表名称
        db_table = 'tb_uers'
        verbose_name = "用户"
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.username
```

- settings.py

```
"""
注册 User 模型类 替换系统自带的User模块
# AUTH_USER_MODEL = '子应用的名称.模型类名字'
"""
AUTH_USER_MODEL = 'users.User'
```

---

### 模型类迁移

```python
python manage.py makemigrations
python manage.py migrate
```

### 自定义用户认证类

- **用于多账号登录**

  - users.utils.py

  ```python
  """
  自定义后端多账号认证
  重写类方法
  """
  import re
  from django.contrib.auth.backends import ModelBackend
  from .models import User
  def User_get_by_account(account):
      try:
          # 判断用户名类型 手机号
          if re.match('^1[345789]\d{9}$', account):
              user = User.objects.get(mobile=account)
          else:
              user = User.objects.get(username=account)
      except:
          return None
      else:
          return user
  class UsernameMobileAuthBackend(ModelBackend):
      def authenticate(self, request, username=None, password=None, **kwargs):
          user = User_get_by_account(username)
          # 校验密码
          if user and user.check_password(password):
              return user
  ```

---

### 自定义模型基类

- 用于为所有的模型类添加字段

- utils.model.py

  ```python
  from django.db import models
  
  class BaseModel(models.Model):
      """ 为模型类补充字段 """
  
      create_time = models.DateTimeField(auto_now_add=True, verbose_name="创建时间")
      update_time = models.DateTimeField(auto_now=True, verbose_name="更新时间")
  
      class Meta:
          """ 表明是抽象模型类，用于继承使用，数据迁移时不会创建BaseModel表 """
          abstract = True
  ```

---

## UWSGI

- manage.py 同级下创建uwsgi.ini文件

- 配置如下

  ```shell
  [uwsgi]
  # 使用Nginx连接时使用，Django程序所在服务器地址
  #socket=172.16.21.25:8001
  # 直接做web服务器使用，Django程序所在服务器地址
  http=192.168.229.148:8001
  # 项目目录  目录是到base_dir
  chdir=/home/python/Desktop/27/meiduo_admin_27/meiduo_mall
  # 项目中wsgi.py文件的目录，相对于项目目录
  wsgi-file=meiduo_mall/wsgi.py
  # 进程数
  processes=4
  # 线程数
  threads=2
  # uwsgi服务器的角色
  master=True
  # 存放进程编号的文件
  pidfile=uwsgi.pid
  # 日志文件
  daemonize=uwsgi.log
  # 指定依赖的虚拟环境 ,到虚拟环境名即可
  virtualenv=/home/python/.virtualenvs/py3_django_44
  ```

- uwsgi命令

  ```shell
  # 启动
  uwsgi --ini uwsgi.ini
  # 停止
  uwsgi --stop uwsgi.pid
  ```

---

## Nginx

- nginx命令

  ```shell
  # 开启和关闭nginx服务1
  systemctl start nginx 
  systemctl stop nginx
  # 开启和关闭nginx服务2
  /etc/init.d/nginx start|stop|reload
  # 开启和关闭nginx服务3
  /usr/sbin/nginx
  # 配置参数
  nginx -V
  # 版本信息
  nginx -v
  # 停止nginx
  nginx -s stop
  # 检查默认配置文件
  nginx -t
  # 检查指定配置文件
  nginx -t -c file.conf
  ```

  

