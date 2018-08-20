# HiaMES-Server Schema

HiaMES-Server 业务设计

## 业务设计

> 业务设计，围绕Graphql标准构建。
Graphql是一种用于 API 的查询语言，通过Graphql Schema编写可实现数据和业务逻辑关系设计。设计结果通过Prisma Server进行数据存取，借助playground客户端实现API的在线调试。

### 相关工具  *学习参考,无须下载*

设计语言：[Graphql Schema](http://graphql.cn/learn/)

数据服务：[Prisma Server](https://www.prisma.io/)

测试工具：[playground](https://github.com/prisma/graphql-playground)

## 快速入门

### Step 1: 安装 Docker

[参考](https://docs.docker-cn.com/engine/installation/)

### Step 2: 安装 Prisma 工具

`npm install -g prisma`

    # or
    # yarn global add prisma

### Step 3: 获取业务设计基础代码

`git clone -b schema https://git.coding.net/guog/helium.git`

    .
    ├── database
    │   ├── docker-compose.yml      --- prisma服务的docker配置
    │   ├── models                  --- 业务模型设计目录
    │   │   ├── base.graphql        --- 基础模型
    │   │   ├── directive.graphql   --- 指令定义
    │   │   ├── enum.graphql        --- 枚举
    │   │   └── system.graphql      --- 系统模型
    │   ├── prisma.yml              --- prisma配置脚本
    │   └── seed
    │       └── datainit.graphql    --- 数据初始化脚本
    ├── .graphqlconfig.yml          --- graphql配置文件
    ├── docs                        --- 文档目录
    ├── LICENSE                     --- 授权
    ├── package.json                --- node 项目配置文件
    └── README.md                   --- 本说明文档

### Step 4: 启动prisma服务

转到`database`目录

`cd database`

启动docker容器

`docker-compose -up -d`

### Step 5: 业务设计

>模型文件在 `database/models` 目录,原有文件是基础模型文件,是MES系统的核心模型设计文件,业务设计过程可参考调用,不可修改.

1. 新模块业务设计在`database/models` 目录添加新模型文件,文件名称以模块名命令如:`new.graphql`

        type New{
            id:ID String!
            name: String!
        }

2. 添加新模型文件引用到 prisma.yml 文件

        datamodel:
        - ./models/directive.graphql
        - ./models/enum.graphql
        - ./models/base.graphql
        - ./models/system.graphql
        - ./models/new.graphql

### Step 6: 发布设计

在当前项目的跟目录执行发布命令:

`npm run deploy`

        > prisma deploy

        Deploying service `default` to stage `default` to server `local` 44ms
        Service is already up to date.

        post-deploy:
        Running graphql get-schema --project database ✔

### Step 7: 交互查询

在当前项目的跟目录执行发布命令:

`npm run play`

    > prisma playground -p 4000 -s

    Serving playground at http://localhost:4000/playground

在浏览器中打开 `http://localhost:4000/playground` 并输入:

    Query{
        news{
            id
            name
        }
    }

## 设计指导

设计指导 [Design guide](docs/designGuide.md)