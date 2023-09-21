# 作业要求

!!! warning "前置要求"
    在进行本次作业前，请先阅读[清软论坛说明](./bbs.md)，**确保**对项目的基本操作和开发流程有所了解。

## 代码风格测试
在该部分中，你需要为SimpleBBS增加代码风格检查，要求如下：

* 完善flake8配置文件

    * 要求忽略且仅忽略.git，\_\_pycache\_\_文件夹，migrations文件夹
    * 对 `tests/test_e2e.py` 忽略E501错误，对 `tests/test_api.py` 忽略E501错误，对 `driver.py` 忽略E501错误，对 `app/settings.py` 忽略E501错误，对 `user/views.py` 忽略E722错误，对 `app/settings_prod.py` 忽略F401和F403错误
    
* 完善格式化脚本 `lint.sh`，脚本执行命令如下

    * 使用 autopep8 对代码自动格式化
    * 使用 autoflake 对代码自动格式化
    * 使用 isort 对代码自动格式化
    * 使用 flake8 检查代码风格

* 评测时执行脚本检测是否正确格式化且符合 flake8 格式，Windows 用户如果不使用 Git Bash 或者 cmder 的话，请使用 PowerShell 而非 cmd 运行脚本

    === "MacOS 或 Linux 用户"
        ```shell
        $ chmod +x lint.sh
        $ ./lint.sh
        $ echo $?
        ```

    === "Windows 用户"
        ```powershell
        $ .\lint.ps1
        ```

!!! note "Windows 用户注意事项"
    - Windows 用户需要补全的也是 **`lint.sh`** 而非 `lint.ps1`。
    - Windows 用户在 PowerShell 中运行脚本时可能会遇到权限问题，可以使用**管理员权限**打开 PowerShell 并在其中输入 `Set-ExecutionPolicy RemoteSigned` 来解决。

经过正确配置 autoflake、autopep8、isort 对代码自动格式化后，执行 flake8 检查代码风格时，**不会输出任何错误或警告**
## 单元测试
补充基础函数和单元测试分别占 50%

在该部分中，同学们需要以**测试驱动开发的方式**补完下列函数，并使用 unittest 补充相应的单元测试

1. `register_params_check` 函数，该函数在 **`utils/register_params_check.py`** 文件中，实现注册账号 API 参数的校验。接收参数如下：

    * `username`: 必填，用户账号
    * `password`: 必填，用户密码
    * `nickname`: 必填，用户昵称
    * `url`: 必填，用户个人地址链接
    * `mobile`: 必填，手机号
    * `magic_number`: 选填，用户喜欢的幸运数字

    **参数要求：**

    - 用户账号为长度 5-12 的字母串加数字，且必须包含这两种类型，所有字母串必须在数字前面，大写字母和小写字母均合法
    - 用户密码为长度 8-15 的字符串，由大写、小写字母、数字和标点符号组成且必须包含这四种类型，有效的标签符号为-_*^
    - 用户的手机号的格式为+[区号].[手机号]，其中区号必须为两位数字，手机号必须为 **12** 位数字
    - 用户的个人地址链接包含协议和域名两部分
        - 协议部分必须为 http:// 或者 https://
        - 域名部分包含 1 到多个点 `.`，表示以点 `.` 分隔的标签序列，且总长度不超过 48 个字符。标签序列只能由下列字符组成：
            - 大小写字母 `A` 到 `Z` 和 `a` 到 `z`
            - 数字 `0` 到 `9`，但最后一段顶级域名不能是纯数字（如 `163.com` 可以但 `163.126` 不可以）
            - 连字符`-`，但不能作为首尾字符
    - magic_number为非负数 <span style="color: red">int</span> 数值，可选参数（在设计测试用例时无需考虑最大值上界）

    **返回值要求：**

      * 返回错误或缺失字段名（如有多个只需要按前述顺序返回第一个）以及一个 bool 值表示是否出错
      * 如果正确，返回 `"ok"` 以及 `True`
      * 如果 magic_number 缺失，请为 content 添加默认值为 0 的 magic_number 字段

    **黑盒测试参数提示：**

      * 传入的内容为 `JSON` 格式，其中 `Key` 可能为任何值，只需关注有用的字段即可，多余的 `Key` 忽略
      * 传入的 `JSON` 格式的内容中，`username` 这一参数可能不是预期的类型

2. 对 `register_params_check` 补充单元测试
    
    请在 `tests/test_basic.py` 的 `TODO` 处补充相应的单元测试，并使得行覆盖率不低于 <span style="color: red">80％</span>，在文档中说明的所有测试用例应在测试代码中有完整体现。

## 集成测试
在该部分中，同学们需要为 SimpleBBS 添加集成测试，请补充 `tests/test_api.py` 中的 `TODO` 部分为注册路由、登录路由和登出路由添加测试，提供了部分注册路由测试代码供同学们参考。

## 端到端测试
在该部分中，同学们需要在 `tests/test_e2e.py` 中使用 unittest 框架和 selenium 为 SimpleBBS 补充端到端测试，selenium 提供了自动化控制浏览器的能力，同学们需要使用 selenium 控制浏览器实现用户的登录、发帖、更新帖子、删除帖子操作，在 `tests/test_e2e.py` 中提供了实现自动登录的部分供同学们参考。

由于 selenium 需要用到 WebDriver 控制浏览器，可在如下链接下载对应浏览器类型及版本的 WebDriver ，并放置于 `drivers` 目录，将 `tests/test_e2e.py` 中的 `DRIVER_PATH` 变量指向 WebDriver 的实际路径，如果出现错误请首先仔细阅读 [环境搭建](./setup.md) 章节的内容。 


!!! question "注意事项"
    端到端测试需要使用浏览器和前端，但是由于助教已经在测试文件中启动了前端，所以你并不需要手动启动。

!!! note "Windows 用户注意事项"
    使用 Windows 的同学在运行端到端测试时可能会出现如下提示
    ![](../../images/none-error-in-windows.png)
    这是正常现象，不会影响测试结果，点击 `确定` 即可。


## 应用部署
本次作业需要修改项目清软论坛中的 `Dockerfile`、`docker-compose.yaml`、`app/settings_prod.py` 和 `nginx/app.conf` 文件来实现容器化部署。具体要求如下：

1. ssh 连接至你个人的远端服务器，安装 Docker 并保持后台运行。
2. 编写合适的 Dockerfile 以及 docker-compose 配置（必要时你需要调整清软论坛的代码）来实现：
    - 清软论坛以 MySQL 为数据库、通过 nginx 以 8000 端口向外提供服务；
    - 通过你的服务器 `ip:8000` 可以访问到论坛前端并正常进行各项操作；
    - 通过你的服务器 `ip:8000/api/v1` 可以直接访问后端的各项 API；
3. 其他要求
    - 你的 Python 版本需要恰好为 3.8.x，你的Nginx版本为 latest，MySQL 版本恰好为5.7；
    - 各个 service 之间的依赖关系应当合理；
    - 清软论坛镜像 container 名称为 app，nginx 镜像 container 名称为 nginx，MySQL 镜像的container 名称为 mysql；（你可以在 docker-compose 配置中指定 container name）
    - 你的数据库使用账号 root（默认值），其密码为你的学号，数据库名称为 thss，使用端口3306（默认值）。请注意 MySQL 默认镜像的时区为 UTC，字符集是 latin1，你也需要进行调整。可以通过在 docker-compose 中指定下述值实现：
        ```yaml
        environment:
        - MYSQL_ROOT_PASSWORD=<你的学号>
        - MYSQL_DATABASE=thss
        - TZ=Asia/Shanghai
        command: ['mysqld', '--character-set-server=utf8mb4', '--collation-server=utf8mb4_unicode_ci']
        ```
    - 你的前端文件应该通过 nginx 的静态文件服务实现（在目前的项目中是通过单独的 React 服务实现的，你需要将前端通过 `npm run build` 打包后的产物，放在后端的 **`build`** 目录下，将其改为 nginx 静态文件服务实现）。你可以使用 volume 实现也可以构建⼀个基于 nginx 的镜像实现。同时你需要在 nginx 中实现反向代理，将 `/api/v1` 的请求转发到后端容器中；
    - nginx 与论坛后端处于⼀个 network，论坛后端与数据库处于⼀个 network。也即通过 nginx 所在容器无法访问数据库容器；
    - 仅 nginx 容器将端口映射给宿主机，端口号为 8000；
    - MySQL 镜像需要指定 `/home/ubuntu/mysql/` 文件夹为持久化存储 Volume，将镜像内 `/var/lib/mysql` 目录挂载到宿主机的 `/home/ubuntu/mysql/` 目录；
    - 你的服务需要至少持续工作至作业截止日期后 10 天。如果无法访问，助教会与你取得联系，请保持联系方式的畅通。

!!! tip "提示"
    - 多使用 `docker logs` 和 `docker exec` 进行调试
    - 编写 `docker-compose.yaml` 时，可以从一个容器开始，在它能够正常运行后再添加其他容器

!!! Danger "注意事项"
    服务器上的所有文件的最后修改时间和服务开始运行的时间需要**保持在作业截止日期之前**，否则你的作业将会视情况扣除一定分数。
    你提交的代码将被作为**查重的依据**，请务必保证你的代码是自己独立完成的。


## 评测说明
- “代码风格测试”部分的评测采用脚本对代码风格进行检查，正确通过即可全分；
- “单元测试”部分采用黑盒脚本对基础函数进行测试，正确通过测试用例即可全分，单元测试人工评测，覆盖率达到要求，测试用例设计恰当合理即可全分；
- “集成测试”部分采用 Monkey Patch 对集成测试进行测试，同学们按照顺序正确测试路由函数即可全分
- “端到端测试”部分采用 Monkey Patch 对端到端测试进行测试，同学们按照顺序正确控制浏览器即可全分
- “服务器端部署” 部分实验采用脚本测试+人工测试的方式进行，完成要求即可得分，请注意一定按照要求来进行实现，脚本无法进行评测会扣除大量分数；
    - 注意：如未使用 Docker，最多只能获得 <span style="color: red">40%</span> 的分数。
- DDL 日期之后，按照 $0.9^{迟交天数}$ 的衰减系数计算分数，迟交时间未满一天记作一天，最多不超过一周，一周后不再接受作业。
