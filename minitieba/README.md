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

## Front end

### Slider

滑动框使用swiper.js插件。body相当于一个轮播图，用户可以左右滑动切换功能栏与内容部分。

### Pop-ups

实现弹出窗口的动画效果，这个部分调用了自己封装的```myAnimateY```函数，以实现点击加号输入文本框弹起，点击其他地方文本框落下的效果

```js
	// 添加帖子
    const appendPost = document.querySelector('.append-post');
    const appendTextarea = document.querySelector('.append-textarea');
    const appendTextareaHeight = 250;
    appendPost.addEventListener('touchend',e=>{
        console.log('append');
        // 要阻止事件冒泡,这样点击子盒子才不会触发父盒子是事件
        e.stopPropagation();
        // window.innerHeight-appendTextareaHeight为底部到盒子的距离
        myAnimateY(appendTextarea,window.innerHeight-appendTextareaHeight);
    });
    const appendParentNode = appendTextarea.parentElement;
    appendTextarea.addEventListener('touchend',e=>{
        e.stopPropagation();
        console.log('appendTextarea');
    })
    appendParentNode.addEventListener('touchend',e=>{
        console.log('appendParentNode',appendParentNode);
        // 点击父盒子,弹窗收回
        myAnimateY(appendTextarea,window.innerHeight);
    })
```

### Append posts

此段代码为核心代码，主要功能是实现向后端发出请求，得到post数据，再将这些数据渲染到主页中。本段代码参考课程6的infinite scroll部分每次只从后端读取quantity条数据，实现部分加载。

```js
//get the index-content div
    const contentBox = document.querySelector('.index-content');

    // Start with first post
    let counter = 1;

    // Load posts 20 at a time
    const quantity = 20;

    // When DOM loads, render the first 20 posts
    load();

    // If scrolled to bottom, load the next 20 posts
    window.addEventListener('touchend', () => {
        if ((contentBox.scrollTop + contentBox.clientHeight) / contentBox.scrollHeight >= 0.9) {
            load();
        }
    })

    // Load next set of posts
    function load() {

        // Set start and end post numbers, and update counter
        const start = counter;
        const end = start + quantity - 1;
        counter = end + 1;

        // Get new posts and add posts
        fetch(`/posts?start=${start}&end=${end}`)
            .then(response => response.json())
            .then(data => {
                data.posts.forEach(add_post);
                console.log(data);
            })
    };

    // Add a new post with given contents to DOM
    function add_post(contents) {

        // Create new post
        const post = document.createElement('div');
        post.className = 'index-word';
        post.innerHTML =`<div class="index-content-name">
                        <span>${contents['name']}</span>
                        <em>${contents["time"]}</em>
                        </div>
                        <div class="index-content-text">
                        ${contents["appendwords"]}
                        </div>`;

        // Add post to DOM
        contentBox.append(post);
    };
```

### Search posts

搜索帖子与上面的添加帖子大体类似，只是fetch的接口不同，但是如果一个帖子也搜不出来，就要更改content的内容，显示相关信息。

```js
    // Load next set of posts
    function load() {

        // Set start and end post numbers, and update counter
        const start = counter;
        const end = start + quantity - 1;
        counter = end + 1;
        const keywords = getQueryVariable('keywords');
        console.log(keywords);
        // Get new posts and add posts
        fetch(`/postSearch?keywords=${keywords}&start=${start}&end=${end}`)
            .then(response => response.json())
            .then(data => {
                if(data.posts.length !== 0 || keywords === ''){
                    havePosts = true;
                    data.posts.forEach(add_post);
                    console.log(data);
                }
                if(!havePosts){
                    add_when_null();
                    console.log('没有帖子');
                    
                }
                
            })
    };

    // Add a new post with given contents to DOM
    function add_post(contents) {

        // Create new post
        const post = document.createElement('div');
        post.className = 'index-word';
        post.innerHTML =`<div class="index-content-name">
                        <span>${contents['name']}</span>
                        <em>${contents["time"]}</em>
                        </div>
                        <div class="index-content-text">
                        ${contents["appendwords"]}
                        </div>`;

        // Add post to DOM
        contentBox.append(post);
    };

    function add_when_null() {
        // Add post to DOM
        contentBox.innerHTML =  `<div class="not-found">
        <span>什么都没找到哦</span>
        </div>`;
    };
});
```

## Back end

后端主要为前端提供接口，主要工作步骤：先配置路由，再在views.py写对应函数。下面重点分析几个views.py的函数：

###  Posts

为Append posts提供fetch接口。

这个函数参考课程6的posts函数，更改了返回值，使返回项不再是自动生成的post，而是从数据库读取的真实posts。其中getRowFromContentTable()为自己封装的数据库操作函数。

```python
def posts(request):

    # Get start and end points
    start = int(request.GET.get("start") or 0)
    end = int(request.GET.get("end") or (start + 9))

    data = getRowFromContentTable(start, end+1)
    for i in data:
        print(i)
    # Generate list of posts
    # data = []
    # for i in range(start, end + 1):
    #     data.append(f"Post #{i}")

    # Artificially delay speed of response
    time.sleep(0.5)
    # Return list of posts
    return JsonResponse({
        "posts": data
    })
```

### Post search

为Search posts提供fetch接口。

与上面的函数类似，但在获取数据前进行模糊查询。

```python
def postSearch(request):
    # Get start and end points
    start = int(request.GET.get("start") or 0)
    end = int(request.GET.get("end") or (start + 9))
    keywords = request.GET.get("keywords")
    # Generate list of posts
    data = getSpecificRowFromContentTable(start, end+1, keywords)
    for i in data:
        print(i)

    # Artificially delay speed of response
    time.sleep(0.5)
    # Return list of posts
    return JsonResponse({
        "posts": data
    })
```

### A little problem of search

这里必须写两个函数再通过重定向的方式搜索，否则在搜索界面多次搜索会出现路由错误。

```python
def search(request):
    return render(request, "xiaotieba/search.html")

def searchWork(request):
    keywords = request.GET.get("keywords")
    return redirect(f'/xiaotieba/search/?keywords={keywords}')
```

### Register

这个函数实现用户注册的功能，通过读取request的相关项得到username与password，id由nanoid库直接生成，以保证id的唯一性。然后将前面值与随机生成的cookie一同存入数据库中，再重定向到主页，并设置response header的cookie为刚刚生成的cookie，name为前面的name。

```python
def registerWork(request):
    username = request.GET.get("username")
    password = request.GET.get("password")
    id = generate(size=5)
    cookie = generate(size=10)
    operateInsertUserTable(id, username, password, cookie)
    response = HttpResponseRedirect('/xiaotieba/')
    response.set_cookie(
        'cookie', cookie, expires=datetime.now()+timedelta(days=14))
    response.set_cookie('username', username,
                        expires=datetime.now()+timedelta(days=14))
    return response
```

### Log in

这个函数实现登录功能，校验request的参数与数据库的username与password是否匹配。

```python
def loginWork(request):
    username = request.GET.get("username")
    password = request.GET.get("password")
    verifyResult = operateVerifyUserTable(username, password)
    # 登录成功
    if (verifyResult == 0):
        cookie = operateUserTable_getCookie(username)
        response = HttpResponseRedirect('/xiaotieba/')
        response.set_cookie(
            'cookie', cookie, expires=datetime.now()+timedelta(days=14))
        response.set_cookie('username', username,
                            expires=datetime.now()+timedelta(days=14))
        return response
    # 查找不到该用户名
    elif (verifyResult == 1):
        return HttpResponseRedirect('/?error=查找不到该用户名')
    # 用户输入密码错误
    else:
        return HttpResponseRedirect('/?error=用户输入密码错误')
```

### Append post

这个函数实现发送时间记录并将帖子存入数据库的操作

```python
def append(request):
    id = generate(size=5)
    appendwords = request.POST.get('appendwords')
    username = request.COOKIES['username']
    now = datetime.now()
    nowTime = now.strftime("%Y-%m-%d %H:%M")
    print(now, nowTime)
    operateInsertContentTable(id, username, appendwords, nowTime)
    return redirect('index')
```

### Some operating function

此外,为了更好地操作数据库，自己封装了几个数据库操作函数，实现比较简单，不再赘述。

# Project summary

这次项目主要实现了首页帖子刷新，增添和搜索功能，‘我的’等其他部分还未实现，但是实现原理与首页类似。整体来说项目主体功能完全实现，可以进行正常发帖，浏览。

##  deficiencies

一些函数应该用post发送和接收的，但是使用了get，因为当时csrf出现一些错误无法解决。最后实现的效果相同但是可能会发生csrf攻击。此外用户密码为明文存储，后期应改为hash后再存储。
