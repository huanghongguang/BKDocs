# API 简介

蓝鲸 API 文档已同步到社区版 6.0 版本。在 6.0 版本基础上，API 接口文档更加规范和全面。

蓝鲸系统允许你以编程方式获取和修改蓝鲸的配置，并提供对数据的访问。

主要用于：
 - SaaS 研发过程中，通过 API 来使用蓝鲸各产品的数据；
 - 将蓝鲸与第三方系统集成；
 - 自动化执行常规任务等。

API 主要包含以下几种方法：get, create/add, update, delete, find/search, send, import, export 等

**注意：**
部分 API 预计于下一个版本下线（2020 年 6 月之后）[下线 API 公示](../开发指南/即将下掉的API接口和变量/readme.md)

## API 调用

调用 API 主要有两种方式，一种是使用 SaaS 开发框架中提供的 SDK 包，一种是根据 API 地址直接访问。

## API 调用说明

请求 API 时，需提供 APP 应用认证信息(bk_app_code、bk_app_secret)和用户认证信息(bk_token、bk_username)，用以对 APP 应用和当前用户进行认证。

## SaaS 应用认证信息

使用 API 前，需先根据已有应用或创建一个应用，获取应用 ID 和安全密钥，作为应用认证信息。

- 应用 ID: 应用唯一标识，创建应用时由创建者指定，可在应用基本信息中通过 应用 ID 获取

- 安全密钥: 应用密钥，创建应用后由平台生成，可在应用基本信息中通过 安全密钥 获取

## 用户认证信息

用户认证，通过验证用户登录态实现。用户登录态 bk_token，在用户登录后，存储在浏览器的 Cookies 中。

调用 API 时，若无法提供用户登录态，可将应用 ID 添加到应用免登录态验证白名单中，则此应用请求 API 时，提供当前用户的 bk_username 即可。

- functioncontroller: 通用白名单管理，通过指定不同的功能 code，维护不同的白名单

- user_auth::skip_user_auth: "应用免登录态验证白名单" 功能 code，添加此功能 code，然后将应用 ID 添加到"功能测试白名单"中
。

## 使用 SaaS 开发框架中的 SDK 包

使用 SDK 包访问 API 有两种方式 shortcuts 或 ComponentClient。使用示例如下：

- shortcuts -- get_client_by_request
```json
    from blueking.component.shortcuts import get_client_by_request
    # 默认从django settings中获取APP认证信息：应用ID和安全密钥
    # 默认从django request中获取用户登录态bk_token
    client = get_client_by_request(request)
    # 参数
    kwargs = {'bk_biz_id': 1}
    result = client.cc.get_app_host_list(kwargs)
```

- shortcuts -- get_client_by_user
```json
    from blueking.component.shortcuts import get_client_by_user
    # 默认从django settings中获取APP认证信息：应用ID和安全密钥
    # 默认从user中获取username，user为User对象或直接为User中username数据
    user = 'username'
    client = get_client_by_user(user)
    # 参数
    kwargs = {'bk_biz_id': 1}
    result = client.cc.get_app_host_list(kwargs)
```

- ComponentClient
```json
     from blueking.component.client import ComponentClient
     # APP应用ID
     bk_app_code = 'xxx'
     # APP安全密钥
     bk_app_secret = 'xxx-xxx-xxx-xxx-xxx'
     # 用户登录态信息
     common_args = {'bk_token': 'xxx'}
     # APP应用ID和APP安全密钥如未提供，默认从django settings中获取
     client = ComponentClient(
         bk_app_code=bk_app_code,
         bk_app_secret=bk_app_secret,
         common_args=common_args
     )
     # 参数
     kwargs = {'bk_biz_id': 1}
     result = client.cc.get_app_host_list(kwargs)
 ```

### APP 开发框架中的 SDK 包

SDK 包默认包含系统提供的所有 API；

用户开发的组件及自助接入的 API，可以通过以下步骤添加到 SDK 包：

- 在 APP 开发框架 blueking/component/apis 目录下，创建系统包文件，如系统名称为 agent，可创建系统包文件 agent.py
- 系统包文件中添加 API 信息，如 agent.py 中添加 create_task 的访问入口

```json
# -*- coding: utf-8 -*-
from ..base import ComponentAPI

# 系统API集合类的名称，一般为 Collections + 系统名
class CollectionsAGENT(object):

    def __init__(self, client):
        self.client = client

        # create_task为API名，method为请求API使用的方法，path为API路径，API域名为系统默认域名
        self.create_task = ComponentAPI(
            client=self.client, method='POST', path='/api/c/compapi/agent/create_task/',
            description=u'',
        )
```

- 在 blueking/component/collections.py 中加入系统 API 文件信息（若已加入则忽略此步骤）

```json
# 导入路径
from .apis.agent import CollectionsAGENT

AVAILABLE_COLLECTIONS = {
    'cc': CollectionsCC,
    'job': CollectionsJOB,
    'bk_login': CollectionsBkLogin,

    # 此处加入新包信息
    'agent': CollectionsAGENT,
}
```

## 根据 API 地址直接访问

请求参数包括：应用信息(bk_app_code + bk_app_secret)，用户信息(bk_token 或 bk_username)，及请求参数

用 curl 直接访问示例如下：

```bash
    curl -d '{"bk_app_code": "xxx", "bk_app_secret": "xxx", "bk_token": "xxx", "bk_biz_id": 1}' 'http://domain.com/path/'

    curl 'http://domain.com/path/?bk_app_code=xxx&bk_app_secret=xxx&bk_token=xxx&bk_biz_id=1'   # 数据需urlencode编码
```

## 深入了解 API

你现在知道怎么使用蓝鲸 API ，不要停下来。
去深入了解 **蓝鲸** 吧。
我们建议你查阅：
[API 网关](../开发指南/扩展开发/API网关/README.md)、
[蓝鲸统一登录 API 列表](../API文档/BK_LOGIN/README.md)、
[蓝鲸开发者中心 API 列表](../API文档/BK_PAAS/README.md)、
[消息管理 API 列表](../API文档/CMSI/README.md)、
[配置平台 API 列表](../API文档/CC/README.md)、
[管控平台 API 列表](../API文档/GSE/README.md)、
[作业平台 API 列表](../API文档/JOB/README.md)、
[监控平台 API 列表](../API文档/MONITOR_V3/README.md)、
[标准运维 API 列表](../API文档/SOPS/README.md)。
