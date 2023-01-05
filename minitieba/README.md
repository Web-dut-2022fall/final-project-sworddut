# Project Introduction

本项目为模仿百度贴吧与comin等论坛网站制作的一个移动端网页，功能包括用户的注册与登录，用户发帖以及模糊搜索相关帖子。

# The technology used

使用Django搭建后端服务器，sqlite作为数据库，以及原生js渲染后端传来的html页面

#  Project Structure

```shell
  └─minitieba
    │  db.sqlite3
    │  manage.py
    │  README.md
    │  user.db
    │  
    ├─minitieba
    │  │  asgi.py
    │  │  settings.py
    │  │  urls.py
    │  │  wsgi.py
    │  │  __init__.py
    │  │  
    │  └─__pycache__
    │          
    ├─static
    │  ├─css
    │  │      common.css
    │  │      common.less
    │  │      index-content.css
    │  │      index.css
    │  │      index.less
    │  │      login.css
    │  │      normalize.css
    │  │      swiper-bundle.min.css
    │  │      swiper.css
    │  │      
    │  ├─images
    │  ├─js
    │  │      animate.js
    │  │      index.js
    │  │      init.js
    │  │      jquery-3.6.0.js
    │  │      post-search.js
    │  │      post.js
    │  │      section.js
    │  │      swiper-bundle.min.js
    │  │      
    │  └─upload
    └─xiaotieba
        │  admin.py
        │  apps.py
        │  models.py
        │  tests.py
        │  urls.py
        │  views.py
        │  __init__.py
        │  
        ├─migrations
        │  │  0001_initial.py
        │  │  0002_remove_userinfo_age.py
        │  │  __init__.py
        │  │  
        │  └─__pycache__
        │          0001_initial.cpython-38.pyc
        │          0002_remove_userinfo_age.cpython-38.pyc
        │          __init__.cpython-38.pyc
        │          
        ├─templates
        │  └─xiaotieba
        │          collection.html
        │          follow.html
        │          history.html
        │          index.html
        │          login.html
        │          member.html
        │          mine.html
        │          register.html
        │          search.html
        │          
        └─__pycache__
```



#  Build a server

你可以使用一下代码运行服务器，必须保证该目录下有manage.py文件

```shell
D:\webProject\小贴吧\minitieba>  python manage.py runserver 0.0.0.0:8000  
```

# Open a client

打开你的浏览器输入一下任意网址:

```python
http://localhost:8000/
http://127.0.0.1:8000/
http://{your private ip address}:8000/
```

如果你使用最后一个网址，你可以在关闭防火墙的前提下，用你的手机内网访问该网站。内网ip可以通过```ipconfig```查询。

# Main code analysis



