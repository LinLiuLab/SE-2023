# 清软论文说明

本项目后端部分依赖 `Django 4.1.4` 及 `Python 3.8.x`，前端部分依赖 `React` 和 `node 18.x.x`

项目分为前端和后端，前端代码在 `frontend` 目录下，后端代码在 `backend` 目录下


## 开发启动前端
在 `frontend` 目录下。

安装依赖
```shell
$ npm install --package-lock=package-lock.json
```
启动开发服务器
```shell
$ npm start
```
浏览器上访问 `localhost:3000` 即可看到项目主页

## 构建前端
在 `frontend` 目录下。

```shell
$ npm run build
```
构建后的文件在 `frontend/build` 目录下，可以使用 Nginx 等 Web 服务器进行部署
## 开发启动后端
在 `backend` 目录下。

安装依赖
```shell
$ pip install -r requirements.txt
```
启动开发服务器
```shell
$ python manage.py runserver
```
浏览器上访问 `localhost:8000/admin` 即可看到 Django 的后台管理页面

## Migration
在 `backend` 目录下。

初始化数据库，在 `user` 和 `post` 目录下各生成一个 `migrations` 文件夹，用于记录数据库的迁移历史
```shell
$ python manage.py makemigrations app user post
```
更新数据库
```shell
$ python manage.py migrate
```
填充虚假数据
```shell
$ python manage.py init_db
```

## 测试
在 `backend` 目录下。

使用下列命令同时运行单元测试、集成测试和端到端测试
```shell
$ python manage.py test --filter test_basic
$ python manage.py test --filter test_api
$ python manage.py test --filter test_e2e
```
## 部署
在 `backend` 目录下。

测试环境下使用的是 SQLite 数据库，生产环境下使用的是 MySQL 数据库。

首先启动 MySQL 服务器，并创建数据库 `thss`。

接下来在 `app/settings_prod.py` 中配置 MySQL 数据库的连接信息，然后使用如下命令初始化数据库
```shell
$ python manage.py migrate --settings=app.settings_prod
```

## Gunicorn 启动
在 `backend` 目录下。

使用下列命令启动 Gunicorn 服务器
```shell
$ DJANGO_MODULE_SETTINGS=app.settings_prod gunicorn -w4 -b 0.0.0.1:8000 --log-level=debug app.wsgi
```



## API 文档

1. 测试 API-无身份验证
    ```
    Method: GET
    URL: /api/v1/hello
    Response:
    {
        'message': 'Hello world'
    }
    ```
2. 测试API-有身份验证
    ```
    Method: GET
    URL: /api/v1/hello-user
    Response:
    {
        'message': 'Hello world, USERNAME' # USERNAME是登录者的学号
    }
    ```
3. 注册
    ```
    Method: POST
    URL: /api/v1/register
    Request:
    {
        'username': '',
        'password': '',
        'nickname': '',
        'url': '',
        'magic_number': ''
        'mobile': '',
    }
    Response:
    {
        'message': 'ok'
    }
    ```
4. 登录
    ```
    Method: PATCH
    URL: /api/v1/login
    Request:
    {
        'username': '',
        'password': ''
    }
    Response:
    {
        'username': '',
        'nickname': '',
        "userId": 1,
        'jwt': ''
    }
    ```
5. 登出
    ```
    Method: PATCH
    URL: /api/v1/logout
    Response:
    {
        'message': 'ok'
    }

    ```
6. 获取当前登录账号个人信息
    ```
    Method: GET
    URL: /api/v1/user
    Response:
    {
        "id": 1,
        "username": "201901****",
        "nickname": "清小软",
        "url": "", # 个人链接地址
        "magic_number": "" # 个人幸运数字
        "mobile": “+86.13312341234”, # 手机号
        "created": "2020-08-14T23:15:49+08:00" # 创建时间
    }
    ```
7. 获取用户昵称
    ```
    Method: GET
    URL: /api/v1/user/:userId
    Response:
    {
        "id": 1,
        "nickname": "清小软",
        "created": "2020-08-14T23:15:49+08:00"
    }
    ```
8. 获取帖子列表
    ```
    Method: GET
    URL: /api/v1/post
    QueryParam:
    {
        'page': 1, # 可选，默认获取第一页
        'size': 10, # 可选，默认为10
        'userId': 1, # 可选，默认获取所有用户帖子
        'orderByReply': true # 默认表示按照主贴更新时间降序，为true表示按照最新回复时间降序
    }
    # Example: GET /api/v1/post?page=1&size=10&orderByReply=true
    Response:
    {
        'page': 1,
        'size': 10,
        'total': 1,
        'posts': [
            {
                "id": 1,
                "userId": 1,
                "nickname": "清小软",
                "title": "hello, world", # 帖子标题
                "content": "welcome to simplebbs", # 帖子内容，为富文本
                "lastRepliedUserId": 2, # 最新回复用户id，默认为发帖人，非评分要求字段
                "lastRepliedNickname": "清大软", # 最新回复用户昵称，默认为发帖人，非评分要求字段
                "lastRepliedTime": "2020-08-22T20:18:19+08:00", # 最新回复时间，非评分要求字段
                "created": "2020-08-14T00:00:00+08:00",
                "updated": "2020-08-14T00:00:00+08:00"
            }
        ]
    }
    ```
9. 发帖
    ```
    Method: POST
    URL: /api/v1/post
    Request:
    {
        "title": "欢迎使用清软论坛", # 帖子标题
        "content": "请同学们畅所欲言" # 帖子内容
    }
    Response:
    {
        'message': 'ok',
        'postId': 1
    }
    ```
10. 编辑当前用户发布的帖子
    ```
    Method: PUT
    URL: /api/v1/post/:postId
    # :postId 为帖子的id，需要是本人所发帖才能修改
    Request:
    {
        "title": "欢迎使用清软论坛", # 帖子标题
        "content": "请同学们畅所欲言！" # 帖子内容
    }
    Response:
    {
        'message': 'ok'
    }
    ```
11. 获取帖子详情与回帖列表
    ```
    Method: GET
    URL: /api/v1/post/:postId
    # :postId 为帖子的id
    Response:
    {
        "id": 1,
        "userId": 1,
        "nickname": "清小软",
        "title": "hello, world", # 帖子标题
        "content": "welcome to simplebbs", # 帖子内容
        "created": "2020-08-14T00:00:00+08:00",
        "updated": "2020-08-14T00:00:00+08:00",
        "lastRepliedTime": "2020-08-22T20:18:19+08:00", # 最新回复时间，非评分要求字段
        "reply": [ # 回帖列表，创建时间升序
            {
                "id": 1,
                "userId": 1,
                "nickname": "清小软",
                "postId": 1,
                "replyId": 0, # 回复主帖
                "content": "Hello, EveryOne!",
                "created": "2020-08-15T11:31:41+08:00",
                "updated": "2020-08-15T11:31:41+08:00"
            },
            {
                "id": 2,
                "userId": 1,
                "nickname": "清小软",
                "postId": 1,
                "replyId": 1, # 回复上一条回帖
                "content": "reply to the first reply",
                "created": "2020-08-15T11:33:23+08:00",
                "updated": "2020-08-15T11:33:23+08:00"
            }
        ]
    }
    ```
12. 回帖
    ```
    Method: POST
    URL: /api/v1/post/:postId/reply
    # :postId 为回复帖子的id
    Request:
    {
        "content": "", # 回帖内容
        "replyId": 1 # 回复目标回复Id，不提供表示回复主楼
    }
    Response:
    {
        'message': 'ok'
    }
    ```
13. 编辑当前用户发布的回帖
    ```
    Method: PUT
    URL: /api/v1/post/:postId/reply/:replyId
    # :postId 为回复帖子的id
    # :replyId 为修改回复的id
    Request:
    {
        content": "", # 修改回帖内容
    }
    Response:
    {
        'message': 'ok'
    }
    ```