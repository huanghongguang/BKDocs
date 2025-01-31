
### 请求方法

POST


### 请求地址

/api/c/compapi/v2/jobv3/operate_step_instance/


### 通用参数

| 字段 | 类型 | 必选 |  描述 |
|-----------|------------|--------|------------|
| bk_app_code  |  string    | 是 | 应用ID     |
| bk_app_secret|  string    | 是 | 安全密钥(应用 TOKEN)，可以通过 蓝鲸智云开发者中心 -> 点击应用ID -> 基本信息 获取 |
| bk_token     |  string    | 否 | 当前用户登录态，bk_token与bk_username必须一个有效，bk_token可以通过Cookie获取 |
| bk_username  |  string    | 否 | 当前用户用户名，应用免登录态验证白名单中的应用，用此字段指定当前用户 |


### 功能描述

用于对执行的实例的步骤进行操作，例如重试，忽略错误等。

### 请求参数



#### 接口参数

| 字段      |  类型      | 必选   |  描述      |
|-----------|------------|--------|------------|
| bk_biz_id   |  long       | 是     | 业务ID |
| job_instance_id   |  long       | 是     | 作业实例ID |
| step_instance_id |  long     | 是     | 步骤实例ID |
| operation_code |  int     | 是     | 操作类型：2、失败IP重做，3、忽略错误 6、确认继续 8、全部重试，9、终止确认流程，10-重新发起确认，11、进入下一步，12、强制跳过 |


##### operation_code 详细说明
| operation_code | 操作类型 | 适用步骤 | 描述 |
|-----------|------------|--------|------------|
| 2  | 失败IP重做   | 脚本执行，文件分发步骤 | 对失败的IP重新下发任务 |
| 3  | 忽略错误     | 脚本执行，文件分发步骤 | 忽略错误，继续执行     |
| 6  | 确认继续     | 人工确认步骤           | 确认继续执行           |
| 8  | 全部重试     | 脚本执行，文件分发步骤 | 对所有的IP重新下发任务 |
| 9  | 终止确认流程 | 人工确认步骤           | 确认终止执行           |
| 10 | 重新发起确认 | 人工确认步骤           | 重新发起确认           |
| 11 | 进入下一步   | 脚本执行，文件分发步骤 | 当步骤状态为终止成功，用于继续执行后续步骤 |
| 12 | 强制跳过     | 脚本执行，文件分发步骤 | 当步骤状态为终止中，用于强制跳过当前步骤，执行后续步骤|

### 请求参数示例

```json
{
    "bk_app_code": "esb_test",
    "bk_app_secret": "xxx",
    "bk_token": "xxx",
    "bk_biz_id": 1,
    "job_instance_id": 100,
    "step_instance_id": 200,
    "operation_code": 2
}
```

### 返回结果示例

```json
{
    "result": true,
    "code": 0,
    "message": "success",
    "data": {
        "step_instance_id": 200,
        "job_instance_id": 100
    }
}
```

### 返回结果参数说明

#### data

| 字段      | 类型      | 描述      |
|-----------|-----------|-----------|
| job_instance_id     | long      | 作业实例ID |
| step_instance_id    | long      | 步骤实例ID |