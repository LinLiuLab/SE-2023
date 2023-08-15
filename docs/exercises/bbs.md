# 清软论文说明

本项目依赖 `Flask 1.1.2` 及 `Python 3.8.x`

## 开发启动
安装依赖
```shell
$ pip install -r requirements.txt
```
根据项目模板提供的配置文件 `config.yaml` 启动开发服务器
```shell
$ python manage.py runserver
```
浏览器上访问 `localhost:5000` 即可看到项目主页

## Migration
初始化数据库，在根目录下创建一个 `migrations` 文件夹，并且在数据库中生成一个 alembic_version 表
```shell
$ python manage.py db init
```
创建迁移历史
```shell
$ python manage.py db migrate
```
更新数据库
```shell
$ python manage.py db upgrade
```
填充虚假数据
```shell
$ python manage.py init_db
```

## 测试
使用下列命令同时运行单元测试、集成测试和端到端测试
```shell
$ python manage.py test
```

## Gunicorn 启动
使用下列命令启动 Gunicorn 服务器
```shell
$ gunicorn -w4 -b 127.0.0.1:5000 --log-level=debug manage:app
```

## API 文档
在启动项目后，访问 http://localhost:5000/apidocs 即可查看 Swagger 文档

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
6. 获取当前登录账号个⼈信息
    ```
    Method: GET
    URL: /api/v1/user
    Response:
    {
        "id": 1,
        "username": "201901****",
        "nickname": "清⼩软",
        "url": "", # 个⼈链接地址
        "magic_number": "" # 个⼈幸运数字
        "mobile": “+86.13312341234”, # ⼿机号
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
        "nickname": "清⼩软",
        "created": "2020-08-14T23:15:49+08:00"
    }
    ```
8. 获取帖子列表
    ```
    Method: GET
    URL: /api/v1/post
    QueryParam:
    {
        'page': 1, # 可选，默认获取第⼀⻚
        'size': 10, # 可选，默认为10
        'userId': 1, # 可选，默认获取所有⽤户帖⼦
        'orderByReply': true # 默认表⽰按照主贴更新时间降序，为true表⽰按照最新回复时间降序
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
                "nickname": "清⼩软",
                "title": "hello, world", # 帖⼦标题
                "content": "welcome to simplebbs", # 帖⼦内容，为富⽂本
                "lastRepliedUserId": 2, # 最新回复⽤户id，默认为发帖⼈，⾮评分要求字段
                "lastRepliedNickname": "清⼤软", # 最新回复⽤户昵称，默认为发帖⼈，⾮评分要求字段
                "lastRepliedTime": "2020-08-22T20:18:19+08:00", # 最新回复时间，⾮评分要求字段
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
        "title": "欢迎使⽤清软论坛", # 帖⼦标题
        "content": "请同学们畅所欲⾔" # 帖⼦内容
    }
    Response:
    {
        'message': 'ok',
        'postId': 1
    }
    ```
10. 编辑当前用户发布的帖⼦
    ```
    Method: PUT
    URL: /api/v1/post/:postId
    # :postId 为帖⼦的id，需要是本⼈所发帖才能修改
    Request:
    {
        "title": "欢迎使⽤清软论坛", # 帖⼦标题
        "content": "请同学们畅所欲⾔！" # 帖⼦内容
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