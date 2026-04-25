# ktg-common 模块说明文档

## 目录结构
```
com.ktg.common/
├── annotation/     # 注解包
├── config/         # 配置包
├── constant/       # 常量包
├── core/           # 核心包
├── enums/          # 枚举包
├── exception/      # 异常包
├── filter/         # 过滤器包
├── utils/          # 工具类包
└── xss/            # XSS防护包
```

---

## 1. annotation 包

### 代码结构
```
com.ktg.common.annotation/
├── DataScope.java      # 数据权限过滤注解
├── DataSource.java     # 多数据源切换注解
├── Excel.java          # Excel导出导入注解
├── Excels.java         # 多Excel注解容器
├── Log.java            # 操作日志记录注解
├── RateLimiter.java    # 接口限流注解
└── RepeatSubmit.java   # 防重复提交注解
```

### 依赖关系
- 依赖本模块的 `enums` 包（如 `DataSourceType`、`BusinessType`、`OperatorType`、`LimitType`）
- 依赖本模块的 `constant` 包（如 `Constants`）
- 依赖本模块的 `utils.poi` 包（如 `ExcelHandlerAdapter`）
- **第三方依赖**：
  - `java.lang.annotation`：Java 原生注解元注解（`@Target`、`@Retention`、`@Documented`、`@Inherited`）

### 功能
该包提供了系统核心功能的声明式注解支持，通过 AOP 切面在运行时拦截并增强方法行为：

| 注解 | 作用目标 | 功能说明 |
|------|---------|---------|
| @DataScope | METHOD | 数据权限过滤，标记需要进行部门/用户数据范围过滤的查询方法 |
| @DataSource | TYPE, METHOD | 多数据源切换，支持在类或方法级别指定数据源类型（MASTER/SLAVE） |
| @Excel/@Excels | FIELD | Excel 导入导出配置（列名、格式、字典转换、日期格式化等） |
| @Log | METHOD, PARAMETER | 操作日志记录，自动记录模块、功能类型、操作人、请求/响应参数 |
| @RateLimiter | METHOD | 接口限流，支持按时间窗口限制请求次数，支持按 IP 或默认策略 |
| @RepeatSubmit | METHOD | 防重复提交，在指定时间间隔内拦截重复请求 |

#### 关键注解属性说明

**@Excel 注解核心属性：**
- `name`：导出到 Excel 中的列名
- `dateFormat`：日期格式（如 yyyy-MM-dd）
- `dictType`：字典类型（如 sys_user_sex），自动进行字典值/标签转换
- `readConverterExp`：读取内容转表达式（如 0=男,1=女,2=未知）
- `cellType`：导出类型（0 数字，1 字符串，2 图片）
- `type`：字段类型（0 导出导入，1 仅导出，2 仅导入）
- `isStatistics`：是否自动统计数据

**@Log 注解核心属性：**
- `title`：模块名称
- `businessType`：业务类型（INSERT/UPDATE/DELETE/EXPORT/IMPORT 等）
- `operatorType`：操作人类别（MANAGE/MOBILE/OTHER）
- `isSaveRequestData`：是否保存请求参数
- `isSaveResponseData`：是否保存响应参数

---

## 2. config 包

### 代码结构
```
com.ktg.common.config/
├── MinioConfig.java    # MinIO 对象存储配置
└── RuoYiConfig.java    # 项目全局配置
```

### 依赖关系
- **RuoYiConfig**：
  - 依赖 Spring Boot 的 `@Component`、`@ConfigurationProperties` 注解
  - 无其他模块内部依赖
- **MinioConfig**：
  - 依赖 Spring 的 `@Configuration`、`@Bean`、`@ConfigurationProperties` 注解
  - **第三方依赖**：`io.minio.MinioClient`（MinIO Java SDK），用于构建 MinIO 客户端连接

### 功能
该包负责系统配置的加载和管理：

#### RuoYiConfig - 项目全局配置

**配置前缀：** `ktg-mes`

**配置项：**
| 属性 | 类型 | 说明 |
|------|------|------|
| name | String | 项目名称 |
| version | String | 版本号 |
| copyrightYear | String | 版权年份 |
| demoEnabled | boolean | 演示模式开关 |
| profile | String | 上传路径根目录 |
| addressEnabled | boolean | 获取地址开关 |
| captchaType | String | 验证码类型 |

**静态方法：**
- `getProfile()`：获取上传根路径
- `getImportPath()`：获取导入文件路径（{profile}/import）
- `getAvatarPath()`：获取头像上传路径（{profile}/avatar）
- `getDownloadPath()`：获取下载路径（{profile}/download/）
- `getUploadPath()`：获取上传路径（{profile}/upload）

#### MinioConfig - MinIO 对象存储配置

**配置前缀：** `minio`

**配置项：**
| 属性 | 类型 | 说明 |
|------|------|------|
| url | String | MinIO 服务地址 |
| accessKey | String | 访问密钥（用户名） |
| secretKey | String | 安全密钥（密码） |
| bucketName | String | 默认存储桶名称 |

**Bean 注册：**
- `getMinioClient()`：使用 `@Bean` 注解创建 `MinioClient` 实例，供 Spring 容器管理

---

## 3. constant 包

### 代码结构
```
com.ktg.common.constant/
├── Constants.java       # 通用常量
├── GenConstants.java    # 代码生成常量
├── HttpStatus.java      # HTTP状态码常量
├── ScheduleConstants.java  # 定时任务常量
└── UserConstants.java   # 用户相关常量
```

### 依赖关系
- **Constants**：
  - **第三方依赖**：`io.jsonwebtoken.Claims`（JWT 库），用于定义 JWT 令牌相关的常量键名
- 其他常量类无外部依赖，均为纯静态常量定义

### 功能
该包集中管理系统所有常量定义，提高代码可维护性：

#### Constants - 通用常量

**字符集：**
- `UTF8` = "UTF-8"
- `GBK` = "GBK"

**HTTP 协议：**
- `HTTP` = "http://"
- `HTTPS` = "https://"

**通用标识：**
- `SUCCESS` = "0"（成功）
- `FAIL` = "1"（失败）

**登录状态：**
- `LOGIN_SUCCESS` = "Success"
- `LOGOUT` = "Logout"
- `REGISTER` = "Register"
- `LOGIN_FAIL` = "Error"

**Redis Key 前缀：**
| 常量名 | 值 | 说明 |
|--------|-----|------|
| CAPTCHA_CODE_KEY | "captcha_codes:" | 验证码 |
| LOGIN_TOKEN_KEY | "login_tokens:" | 登录令牌 |
| REPEAT_SUBMIT_KEY | "repeat_submit:" | 防重提交 |
| RATE_LIMIT_KEY | "rate_limit:" | 限流 |
| SYS_CONFIG_KEY | "sys_config:" | 参数管理 |
| SYS_DICT_KEY | "sys_dict:" | 字典管理 |

**验证码有效期：**
- `CAPTCHA_EXPIRATION` = 2（分钟）

**JWT 声明键名：**
- `TOKEN` = "token"
- `TOKEN_PREFIX` = "Bearer "
- `LOGIN_USER_KEY` = "login_user_key"
- `JWT_USERID` = "userid"
- `JWT_USERNAME` = Claims.SUBJECT
- `JWT_AVATAR` = "avatar"
- `JWT_CREATED` = "created"
- `JWT_AUTHORITIES` = "authorities"

**其他：**
- `RESOURCE_PREFIX` = "/profile"（资源映射路径前缀）
- `LOOKUP_RMI` = "rmi:"（RMI 远程方法调用）
- `LOOKUP_LDAP` = "ldap:"（LDAP 远程方法调用）
- `LOOKUP_LDAPS` = "ldaps:"（LDAPS 远程方法调用）
- `JOB_WHITELIST_STR` = { "com.ruoyi" }（定时任务白名单）
- `JOB_ERROR_STR` = 定时任务违规字符数组

#### HttpStatus - HTTP 状态码常量

| 常量名 | 值 | 说明 |
|--------|-----|------|
| SUCCESS | 200 | 成功 |
| CREATED | 201 | 已创建 |
| ACCEPTED | 202 | 已接受 |
| NO_CONTENT | 204 | 无内容 |
| MOVED_PERM | 301 | 永久移动 |
| SEE_OTHER | 303 | 查看其他 |
| TEMPORARY_REDIRECT | 307 | 临时重定向 |
| PERMANENT_REDIRECT | 308 | 永久重定向 |
| BAD_REQUEST | 400 | 错误请求 |
| UNAUTHORIZED | 401 | 未授权 |
| FORBIDDEN | 403 | 禁止访问 |
| NOT_FOUND | 404 | 未找到 |
| BAD_METHOD | 405 | 方法不允许 |
| NOT_ACCEPTABLE | 406 | 不可接受 |
| CONFLICT | 409 | 冲突 |
| TOO_MANY_REQUESTS | 429 | 请求过多 |
| INTERNAL_SERVER_ERROR | 500 | 服务器内部错误 |
| NOT_IMPLEMENTED | 501 | 未实现 |
| BAD_GATEWAY | 502 | 错误网关 |
| SERVICE_UNAVAILABLE | 503 | 服务不可用 |
| GATEWAY_TIMEOUT | 504 | 网关超时 |

#### UserConstants - 用户相关常量

**用户名长度：**
- `USERNAME_MIN_LENGTH` = 2
- `USERNAME_MAX_LENGTH` = 20

**密码长度：**
- `PASSWORD_MIN_LENGTH` = 5
- `PASSWORD_MAX_LENGTH` = 20

**用户状态：**
- `USER_NORMAL` = "0"（正常）
- `USER_DISABLE` = "1"（停用）
- `USER_DELETE` = "2"（删除）

**菜单类型：**
- `TYPE_DIR` = "M"（目录）
- `TYPE_MENU` = "C"（菜单）
- `TYPE_BUTTON` = "F"（按钮）

---

## 4. core 包

### 代码结构
```
com.ktg.common.core/
├── controller/
│   └── BaseController.java        # 控制器基类
├── domain/
│   ├── AjaxResult.java            # 统一响应对象
│   ├── BaseEntity.java            # 实体基类
│   ├── TreeEntity.java            # 树形结构实体基类
│   ├── TreeSelect.java            # 树形选择数据结构
│   ├── entity/                    # 系统实体类
│   │   ├── ItemType.java
│   │   ├── SysAutoCodePart.java
│   │   ├── SysAutoCodeResult.java
│   │   ├── SysAutoCodeRule.java
│   │   ├── SysDept.java
│   │   ├── SysDictData.java
│   │   ├── SysDictType.java
│   │   ├── SysMenu.java
│   │   ├── SysRole.java
│   │   └── SysUser.java
│   └── model/                     # 数据传输模型
│       ├── LoginBody.java
│       ├── LoginUser.java
│       └── RegisterBody.java
├── page/
│   ├── PageDomain.java            # 分页参数域
│   ├── TableDataInfo.java         # 表格数据响应
│   └── TableSupport.java          # 表格参数支持
├── redis/
│   └── RedisCache.java            # Redis 缓存工具类
└── text/
    ├── CharsetKit.java            # 字符集工具
    ├── Convert.java               # 类型转换工具
    └── StrFormatter.java          # 字符串格式化工具
```

### 依赖关系

#### BaseController 依赖
- **本模块依赖**：
  - `constant.HttpStatus` - HTTP 状态码
  - `core.domain.AjaxResult` - 统一响应对象
  - `core.domain.model.LoginUser` - 登录用户模型
  - `core.page.PageDomain` - 分页参数域
  - `core.page.TableDataInfo` - 表格数据响应
  - `core.page.TableSupport` - 表格参数支持
  - `utils.DateUtils` - 日期工具
  - `utils.PageUtils` - 分页工具
  - `utils.SecurityUtils` - 安全工具
  - `utils.StringUtils` - 字符串工具
  - `utils.sql.SqlUtil` - SQL 工具

- **第三方依赖**：
  - `org.slf4j` - 日志框架
  - `org.springframework.web` - Web 数据绑定（WebDataBinder）
  - `com.github.pagehelper` - 分页插件

#### RedisCache 依赖
- **第三方依赖**：`org.springframework.data.redis.core.RedisTemplate`（Spring Data Redis）

### 功能

#### controller 子包 - BaseController

所有 Controller 的基类，提供通用的 Web 层数据处理能力。

**核心方法：**

| 方法 | 说明 |
|------|------|
| `initBinder(WebDataBinder)` | 日期参数绑定，将字符串自动转换为 Date 类型 |
| `startPage()` | 设置请求分页数据（调用 PageUtils.startPage()） |
| `startOrderBy()` | 设置请求排序数据 |
| `clearPage()` | 清理分页的线程变量 |
| `getDataTable(List<?>)` | 响应请求分页数据，封装为 TableDataInfo |
| `success()` / `success(String)` / `success(String, Object)` | 返回成功响应 |
| `error()` / `error(String)` | 返回失败响应 |
| `toAjax(int)` / `toAjax(boolean)` | 根据影响行数/布尔值返回响应 |
| `redirect(String)` | 页面跳转（重定向） |
| `getLoginUser()` | 获取当前登录用户信息 |
| `getUserId()` | 获取登录用户 ID |
| `getDeptId()` | 获取登录部门 ID |
| `getUsername()` | 获取登录用户名 |

#### domain 子包

**BaseEntity - 实体基类**

所有实体类的父类，包含通用审计字段：

| 字段 | 类型 | 说明 |
|------|------|------|
| searchValue | String | 搜索值 |
| createBy | String | 创建者 |
| createTime | Date | 创建时间（@JsonFormat 格式化） |
| updateBy | String | 更新者 |
| updateTime | Date | 更新时间（@JsonFormat 格式化） |
| remark | String | 备注 |
| params | Map<String, Object> | 请求参数（动态扩展） |

**AjaxResult - 统一响应对象**

继承 `HashMap<String, Object>`，所有 API 响应的统一格式。

**核心字段：**
- `code` - 状态码（int）
- `msg` - 消息（String）
- `data` - 数据对象（Object）

**静态工厂方法：**
```java
AjaxResult.success()                    // {code:200, msg:"操作成功"}
AjaxResult.success(Object)              // {code:200, msg:"操作成功", data:...}
AjaxResult.success(String)              // {code:200, msg:自定义消息}
AjaxResult.success(String, Object)      // {code:200, msg:..., data:...}
AjaxResult.error()                      // {code:500, msg:"操作失败"}
AjaxResult.error(String)                // {code:500, msg:自定义消息}
AjaxResult.error(String, Object)        // {code:500, msg:..., data:...}
AjaxResult.error(int, String)           // 自定义状态码和消息
```

**链式调用支持：**
- 重写 `put(String, Object)` 方法，返回 `this`，支持链式操作

**TreeEntity - 树形结构实体基类**

继承 BaseEntity，用于菜单、部门等树形结构数据。

**扩展字段：**
- `parentId` - 父节点 ID
- `parentName` - 父节点名称
- `ancestors` - 祖级列表
- `children` - 子节点列表

**TreeSelect - 树形选择数据结构**

用于前端树形下拉选择组件的数据结构：
- `id` - 节点 ID
- `label` - 节点标签
- `children` - 子节点列表

**entity 子包 - 系统核心实体**

| 实体类 | 说明 |
|--------|------|
| SysUser | 系统用户 |
| SysRole | 系统角色 |
| SysMenu | 系统菜单 |
| SysDept | 系统部门 |
| SysDictType | 字典类型 |
| SysDictData | 字典数据 |
| SysAutoCodeRule | 自动编码规则 |
| SysAutoCodePart | 自动编码规则组成部分 |
| SysAutoCodeResult | 自动编码生成结果 |
| ItemType | 物料类型 |

**model 子包 - 数据传输模型**

| 模型类 | 说明 |
|--------|------|
| LoginBody | 登录请求体（username, password, code, uuid） |
| RegisterBody | 注册请求体 |
| LoginUser | 登录用户信息（实现 UserDetails 接口） |

**LoginUser 核心字段：**
- `userId` - 用户 ID
- `deptId` - 部门 ID
- `token` - 登录令牌
- `loginTime` - 登录时间
- `expireTime` - 过期时间
- `permissions` - 权限列表
- `user` - 用户实体（SysUser）

#### page 子包 - 分页支持

**PageDomain - 分页参数域**

从请求参数中提取的分页信息：
- `pageNum` - 页码
- `pageSize` - 每页数量
- `orderBy` - 排序列
- `isAsc` - 排序方向（asc/desc）

**TableDataInfo - 表格数据响应**

分页查询的统一响应格式：
- `code` - 状态码
- `msg` - 消息
- `rows` - 数据列表
- `total` - 总记录数

**TableSupport - 表格参数支持**

从 HttpServletRequest 构建分页参数的工具类：
- `buildPageRequest()` - 从请求参数构建 PageDomain

#### redis 子包 - RedisCache

Spring RedisTemplate 的封装类，提供便捷的缓存操作方法。

**核心方法分类：**

**对象缓存（ValueOperations）：**
```java
setCacheObject(key, value)                    // 存储对象
setCacheObject(key, value, timeout, timeUnit) // 存储对象并设置过期时间
getCacheObject(key)                            // 获取对象
deleteObject(key)                               // 删除单个对象
deleteObject(collection)                        // 批量删除
expire(key, timeout)                            // 设置过期时间
expire(key, timeout, unit)                     // 设置过期时间（指定单位）
```

**List 缓存（ListOperations）：**
```java
setCacheList(key, dataList)      // 存储 List
getCacheList(key)                 // 获取 List
```

**Set 缓存（SetOperations）：**
```java
setCacheSet(key, dataSet)         // 存储 Set
getCacheSet(key)                   // 获取 Set
```

**Hash 缓存（HashOperations）：**
```java
setCacheMap(key, dataMap)                     // 存储 Map
getCacheMap(key)                               // 获取 Map
setCacheMapValue(key, hKey, value)             // 设置 Hash 中的单个值
getCacheMapValue(key, hKey)                     // 获取 Hash 中的单个值
delCacheMapValue(key, hKey)                     // 删除 Hash 中的单个值
getMultiCacheMapValue(key, hKeys)               // 批量获取 Hash 值
```

**键操作：**
```java
keys(pattern)  // 按模式获取键列表
```

#### text 子包 - 文本工具

**CharsetKit - 字符集工具**

提供字符集名称和 Charset 对象的转换：
- `toCharset(String)` - 字符串转 Charset
- `toCharset(String, Charset)` - 字符串转 Charset，带默认值
- 支持 UTF-8、GBK、ISO-8859-1 等常用字符集

**Convert - 类型转换工具**

支持各种基本类型和数组的转换：
- `toStr(Object)` / `toStr(Object, String)` - 转字符串
- `toInt(Object)` / `toInt(Object, int)` - 转 int
- `toLong(Object)` / `toLong(Object, long)` - 转 long
- `toDouble(Object)` / `toDouble(Object, double)` - 转 double
- `toFloat(Object)` / `toFloat(Object, float)` - 转 float
- `toBool(Object)` / `toBool(Object, boolean)` - 转 boolean
- `toBigDecimal(Object)` - 转 BigDecimal
- `toDate(Object)` - 转 Date
- `toEnum(Class<T>, Object)` - 转枚举
- `toStrMap(Map<?, ?>)` - 转 String 类型的 Map

**StrFormatter - 字符串格式化工具**

支持 `{}` 占位符的字符串格式化：
```java
StrFormatter.format("Hello, {}!", "World")  // "Hello, World!"
StrFormatter.format("Value: {}", 123)        // "Value: 123"
```

---

## 5. enums 包

### 代码结构
```
com.ktg.common.enums/
├── BusinessStatus.java    # 业务操作状态
├── BusinessType.java      # 业务操作类型
├── CycleMethodMnum.java   # 周期方法枚举
├── DataSourceType.java    # 数据源类型
├── HttpMethod.java        # HTTP请求方法
├── LimitType.java         # 限流类型
├── OperatorType.java      # 操作人类型
├── PartTypeEnum.java      # 编码部分类型
└── UserStatus.java        # 用户状态
```

### 依赖关系
- 所有枚举类均为纯定义，无外部依赖（除 Java 标准库）

### 功能
该包定义系统所有枚举类型，提高代码可读性和类型安全。

#### 枚举列表

| 枚举 | 枚举值 | 说明 |
|------|--------|------|
| **BusinessType** | OTHER, INSERT, UPDATE, DELETE, GRANT, EXPORT, IMPORT, FORCE, GENCODE, CLEAN | 业务操作类型，用于 @Log 注解 |
| **BusinessStatus** | SUCCESS, FAIL | 业务操作状态（成功/失败） |
| **DataSourceType** | MASTER, SLAVE | 数据源类型（主库/从库），用于 @DataSource 注解 |
| **HttpMethod** | GET, HEAD, POST, PUT, PATCH, DELETE, OPTIONS, TRACE | HTTP 请求方法 |
| **LimitType** | DEFAULT, IP, CUSTOMER | 限流类型，用于 @RateLimiter 注解 |
| **OperatorType** | OTHER, MANAGE, MOBILE | 操作人类型（管理端/移动端），用于 @Log 注解 |
| **UserStatus** | OK, DISABLE, DELETED | 用户状态（正常/停用/删除） |
| **CycleMethodMnum** | - | 周期方法枚举（用于编码规则） |
| **PartTypeEnum** | - | 编码部分类型枚举（用于自动编码规则） |

#### BusinessType 详解

```java
OTHER(0),      // 其它
INSERT(1),     // 新增
UPDATE(2),     // 修改
DELETE(3),     // 删除
GRANT(4),      // 授权
EXPORT(5),     // 导出
IMPORT(6),     // 导入
FORCE(7),      // 强退
GENCODE(8),    // 生成代码
CLEAN(9)       // 清空数据
```

---

## 6. exception 包

### 代码结构
```
com.ktg.common.exception/
├── base/
│   └── BaseException.java        # 基础异常
├── file/
│   ├── FileException.java        # 文件异常基类
│   ├── FileNameLengthLimitExceededException.java  # 文件名过长异常
│   ├── FileSizeLimitExceededException.java        # 文件大小超限异常
│   └── InvalidExtensionException.java             # 文件扩展名无效异常
├── job/
│   └── TaskException.java        # 定时任务异常
├── user/
│   ├── CaptchaException.java      # 验证码异常
│   ├── CaptchaExpireException.java  # 验证码过期异常
│   ├── UserException.java          # 用户异常基类
│   └── UserPasswordNotMatchException.java  # 密码不匹配异常
├── BussinessException.java        # 业务异常
├── DemoModeException.java         # 演示模式异常
├── GlobalException.java           # 全局异常处理器（接口）
├── ServiceException.java          # 服务层异常
└── UtilException.java             # 工具类异常
```

### 依赖关系
- **BaseException**：
  - 依赖本模块：`utils.MessageUtils`、`utils.StringUtils`
- **ServiceException**：无额外依赖，直接继承 RuntimeException
- 其他异常类：继承相应的基类异常

### 功能
该包定义系统所有异常类型和全局异常处理规范。

#### 异常继承体系

```
RuntimeException
├── BaseException                    # 基础异常（支持国际化）
│   ├── FileException                # 文件异常基类
│   │   ├── FileNameLengthLimitExceededException
│   │   ├── FileSizeLimitExceededException
│   │   └── InvalidExtensionException
│   ├── TaskException                # 定时任务异常
│   └── UserException                # 用户异常基类
│       ├── CaptchaException
│       ├── CaptchaExpireException
│       └── UserPasswordNotMatchException
├── ServiceException                 # 服务层异常（最常用）
├── BussinessException               # 业务异常（拼写遗留）
├── DemoModeException                # 演示模式异常
├── UtilException                    # 工具类异常
└── GlobalException                  # 全局异常处理器接口（标记）
```

#### BaseException - 基础异常

所有自定义异常的基类，支持国际化消息解析。

**核心字段：**
| 字段 | 类型 | 说明 |
|------|------|------|
| module | String | 所属模块 |
| code | String | 错误码 |
| args | Object[] | 错误码对应的参数 |
| defaultMessage | String | 默认消息 |

**getMessage() 方法逻辑：**
1. 如果 code 不为空，尝试通过 `MessageUtils.message(code, args)` 获取国际化消息
2. 如果获取失败或 code 为空，返回 defaultMessage

#### ServiceException - 服务层异常

业务代码中最常用的异常类型。

**核心字段：**
| 字段 | 类型 | 说明 |
|------|------|------|
| code | Integer | 错误码 |
| message | String | 错误提示 |
| detailMessage | String | 错误明细（内部调试用） |

**构造方法：**
```java
ServiceException()                                    // 空构造（反序列化）
ServiceException(String message)                      // 仅消息
ServiceException(String message, Integer code)        // 消息 + 错误码
```

**链式调用支持：**
```java
ServiceException.setMessage(String)
ServiceException.setDetailMessage(String)
```

#### 其他异常说明

| 异常类 | 触发场景 |
|--------|---------|
| FileException | 文件操作相关错误的基类 |
| FileSizeLimitExceededException | 上传文件大小超过限制 |
| FileNameLengthLimitExceededException | 文件名长度超过限制 |
| InvalidExtensionException | 文件扩展名不允许 |
| TaskException | 定时任务执行失败 |
| UserException | 用户相关异常基类 |
| CaptchaException | 验证码错误 |
| CaptchaExpireException | 验证码过期 |
| UserPasswordNotMatchException | 密码不匹配 |
| DemoModeException | 演示模式下禁止的操作 |
| UtilException | 工具类执行过程中发生错误 |

---

## 7. filter 包

### 代码结构
```
com.ktg.common.filter/
├── RepeatableFilter.java          # 可重复读请求过滤器
├── RepeatedlyRequestWrapper.java  # 可重复读请求包装器
├── XssFilter.java                 # XSS攻击防护过滤器
└── XssHttpServletRequestWrapper.java  # XSS请求包装器
```

### 依赖关系
- **所有过滤器类**：
  - 依赖本模块：`utils.StringUtils`
  - **第三方依赖**：`javax.servlet`（Servlet API），实现 `Filter` 接口
  - **RepeatedlyRequestWrapper** 还依赖 `org.springframework.http.MediaType` 判断请求内容类型

### 功能
该包提供 HTTP 请求的过滤器链组件。

#### XssFilter - XSS 攻击防护过滤器

防止跨站脚本（XSS）攻击的 Servlet 过滤器。

**过滤规则：**
- 仅过滤 POST/PUT 请求，GET/DELETE 请求不过滤
- 支持配置排除 URL 列表

**核心方法：**

| 方法 | 说明 |
|------|------|
| `init(FilterConfig)` | 初始化，读取 exclude 参数（排除 URL 列表，逗号分隔） |
| `doFilter(ServletRequest, ServletResponse, FilterChain)` | 执行过滤逻辑 |
| `handleExcludeURL(HttpServletRequest, HttpServletResponse)` | 判断当前 URL 是否在排除列表中 |

**过滤流程：**
1. 获取请求方法，如果是 GET/DELETE，直接放行
2. 检查当前 URL 是否在排除列表中，如果是，直接放行
3. 将请求包装为 `XssHttpServletRequestWrapper`
4. 传递包装后的请求给过滤器链

#### XssHttpServletRequestWrapper - XSS 请求包装器

继承 `HttpServletRequestWrapper`，对参数进行 HTML 转义处理。

**重写方法：**
- `getParameter(String)` - 获取参数值并转义
- `getParameterValues(String)` - 获取参数值数组并转义
- `getParameterMap()` - 获取参数 Map 并转义所有值
- `getHeader(String)` - 获取 Header 并转义

**转义实现：**
使用 `EscapeUtil` 进行 HTML 转义，将 `<script>alert('xss')</script>` 转换为 `&lt;script&gt;alert(&#39;xss&#39;)&lt;/script&gt;`

#### RepeatableFilter - 可重复读请求过滤器

解决 HttpServletRequest 的输入流只能读取一次的问题。

**应用场景：**
- 日志记录需要读取请求体
- 防重提交校验需要读取请求体
- 签名校验需要读取请求体

**过滤条件：**
- 仅对 `application/json` 类型的请求进行包装
- 其他类型请求直接放行

**过滤流程：**
1. 判断请求是否为 JSON 类型
2. 如果是，包装为 `RepeatedlyRequestWrapper`
3. 否则，使用原始请求

#### RepeatedlyRequestWrapper - 可重复读请求包装器

继承 `HttpServletRequestWrapper`，将输入流缓存到字节数组中。

**核心实现：**
```java
// 在构造方法中读取输入流并缓存
byte[] body = IOUtils.toByteArray(request.getInputStream());

// 重写 getInputStream()，返回缓存的 ByteArrayInputStream
@Override
public ServletInputStream getInputStream() {
    return new ByteArrayServletInputStream(body);
}

// 重写 getReader()，使用缓存的字节数组
@Override
public BufferedReader getReader() {
    return new BufferedReader(new InputStreamReader(getInputStream()));
}
```

这样，请求体可以被多次读取，而不会出现 "Stream closed" 错误。

---

## 8. utils 包

### 代码结构
```
com.ktg.common.utils/
├── barcode/
│   └── BarcodeUtil.java           # 条码/二维码工具
├── bean/
│   ├── BeanUtils.java             # Bean工具
│   └── BeanValidators.java        # Bean校验工具
├── file/
│   ├── FileTypeUtils.java         # 文件类型判断
│   ├── FileUploadUtils.java       # 文件上传工具
│   ├── FileUtils.java             # 文件操作工具
│   ├── ImageUtils.java            # 图片处理工具
│   ├── MimeTypeUtils.java         # MIME类型工具
│   └── MinioUtil.java             # MinIO文件操作
├── html/
│   ├── EscapeUtil.java            # HTML转义工具
│   └── HTMLFilter.java            # HTML过滤工具
├── http/
│   ├── HttpHelper.java            # HTTP请求辅助
│   └── HttpUtils.java             # HTTP请求工具
├── ip/
│   ├── AddressUtils.java          # IP地址解析
│   └── IpUtils.java               # IP获取工具
├── poi/
│   ├── ExcelHandlerAdapter.java   # Excel处理器适配器
│   └── ExcelUtil.java             # Excel导入导出工具
├── reflect/
│   └── ReflectUtils.java          # 反射工具
├── sign/
│   ├── Base64.java                # Base64编解码
│   └── Md5Utils.java              # MD5加密
├── spring/
│   └── SpringUtils.java           # Spring上下文工具
├── sql/
│   └── SqlUtil.java               # SQL工具
├── uuid/
│   ├── IdUtils.java               # ID生成工具
│   ├── Seq.java                   # 序列生成器
│   └── UUID.java                  # UUID工具
├── Arith.java                     # 精确计算工具
├── DateUtils.java                 # 日期工具
├── DictUtils.java                 # 字典工具
├── ExceptionUtil.java             # 异常工具
├── LogUtils.java                  # 日志工具
├── MessageUtils.java              # 消息工具
├── PageUtils.java                 # 分页工具
├── SecurityUtils.java             # 安全工具
├── ServletUtils.java              # Servlet工具
├── StringUtils.java               # 字符串工具
└── Threads.java                   # 线程工具
```

### 依赖关系

#### 核心工具类依赖

**StringUtils**：
- **第三方依赖**：`org.apache.commons.lang3.StringUtils`（继承扩展）、`org.springframework.util.AntPathMatcher`（路径匹配）
- 依赖本模块：`constant.Constants`、`core.text.StrFormatter`

**DateUtils**：
- **第三方依赖**：`org.apache.commons.lang3.time.DateUtils`（继承扩展）、`java.time.*`（Java 8 时间 API）

**SecurityUtils**：
- **第三方依赖**：`org.springframework.security.core.Authentication`、`org.springframework.security.core.context.SecurityContextHolder`、`org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder`
- 依赖本模块：`constant.HttpStatus`、`core.domain.model.LoginUser`、`exception.ServiceException`

#### 子包依赖

**barcode/BarcodeUtil**：
- **第三方依赖**：`net.sf.barcode4j`（条码生成）、`com.google.zxing`（二维码生成）

**file/MinioUtil**：
- **第三方依赖**：`io.minio.MinioClient`（MinIO SDK）
- 依赖本模块：`utils.ServletUtils`、`utils.spring.SpringUtils`

**poi/ExcelUtil**：
- **第三方依赖**：`org.apache.poi`（Apache POI Excel 库）
- 依赖本模块：`annotation.Excel/Excels`、`config.RuoYiConfig`、`core.domain.AjaxResult`、`core.text.Convert`、`exception.UtilException`、多个 utils 子工具类

**spring/SpringUtils**：
- **第三方依赖**：`org.springframework.beans.BeansException`、`org.springframework.context.ApplicationContext`

### 功能

#### 顶层工具类

##### StringUtils - 字符串工具

继承并扩展 `org.apache.commons.lang3.StringUtils`。

**核心方法：**

| 方法 | 说明 |
|------|------|
| `nvl(T, T)` | 获取参数不为空值（空则返回默认值） |
| `isEmpty(Collection/Map/Array/String)` | 判断是否为空 |
| `isNotEmpty(...)` | 判断是否非空 |
| `isNull(Object)` / `isNotNull(Object)` | 判断是否为 null |
| `isArray(Object)` | 判断是否为数组类型 |
| `trim(String)` | 去空格 |
| `substring(String, int)` / `substring(String, int, int)` | 截取字符串（支持负数索引） |
| `format(String, Object...)` | 格式化文本（{} 占位符） |
| `ishttp(String)` | 是否为 http(s):// 开头 |
| `str2Set(String, String)` | 字符串转 Set |
| `str2List(String, String, boolean, boolean)` | 字符串转 List |
| `toUnderScoreCase(String)` | 驼峰转下划线命名 |
| `convertToCamelCase(String)` | 下划线大写转驼峰（HELLO_WORLD -> HelloWorld） |
| `toCamelCase(String)` | 下划线转驼峰（user_name -> userName） |
| `matches(String, List<String>)` | 查找字符串是否匹配列表中的任意一个 |
| `isMatch(String, String)` | URL 路径匹配（支持 ?, *, ** 通配符） |
| `padl(Number, int)` / `padl(String, int, char)` | 数字/字符串左补齐 |

**路径匹配规则：**
- `?` - 匹配单个字符
- `*` - 匹配一层路径内的任意字符串，不可跨层级
- `**` - 匹配任意层路径

##### DateUtils - 日期工具

继承并扩展 `org.apache.commons.lang3.time.DateUtils`。

**日期格式常量：**
```java
YYYY = "yyyy"
YYYY_MM = "yyyy-MM"
YYYY_MM_DD = "yyyy-MM-dd"
YYYYMMDDHHMMSS = "yyyyMMddHHmmss"
YYYY_MM_DD_HH_MM_SS = "yyyy-MM-dd HH:mm:ss"
```

**核心方法：**

| 方法 | 说明 |
|------|------|
| `getNowDate()` | 获取当前 Date 型日期 |
| `getDate()` | 获取当前日期字符串（yyyy-MM-dd） |
| `getTime()` | 获取当前时间字符串（yyyy-MM-dd HH:mm:ss） |
| `dateTimeNow()` / `dateTimeNow(String)` | 获取当前日期时间字符串 |
| `parseDateToStr(String, Date)` | 日期转字符串（指定格式） |
| `dateTime(String, String)` | 字符串转日期（指定格式） |
| `datePath()` | 日期路径（年/月/日 如 2024/04/24） |
| `parseDate(Object)` | 智能解析日期字符串（支持多种格式） |
| `getServerStartDate()` | 获取服务器启动时间 |
| `differentDaysByMillisecond(Date, Date)` | 计算相差天数 |
| `getDatePoor(Date, Date)` | 计算两个时间差（返回如 "3天2小时30分钟"） |
| `toDate(LocalDateTime)` / `toDate(LocalDate)` | Java 8 Time API 转 Date |

**parsePatterns 支持的格式：**
```
"yyyy-MM-dd", "yyyy-MM-dd HH:mm:ss", "yyyy-MM-dd HH:mm", "yyyy-MM"
"yyyy/MM/dd", "yyyy/MM/dd HH:mm:ss", "yyyy/MM/dd HH:mm", "yyyy/MM"
"yyyy.MM.dd", "yyyy.MM.dd HH:mm:ss", "yyyy.MM.dd HH:mm", "yyyy.MM"
```

##### SecurityUtils - 安全工具

与 Spring Security 集成的安全相关工具。

**核心方法：**

| 方法 | 说明 |
|------|------|
| `getUserId()` | 获取当前登录用户 ID |
| `getDeptId()` | 获取当前登录部门 ID |
| `getUsername()` | 获取当前登录用户名 |
| `getLoginUser()` | 获取当前登录用户对象（LoginUser） |
| `getAuthentication()` | 获取 Authentication 对象 |
| `encryptPassword(String)` | 使用 BCrypt 加密密码 |
| `matchesPassword(String, String)` | 判断密码是否匹配 |
| `isAdmin(Long)` | 判断是否为管理员（userId == 1） |

**实现原理：**
- 从 `SecurityContextHolder.getContext().getAuthentication()` 获取认证信息
- Principal 中存储的是 `LoginUser` 对象

##### ServletUtils - Servlet 工具

封装 Servlet API 的常用操作。

**核心方法：**

| 方法 | 说明 |
|------|------|
| `getRequest()` / `getResponse()` | 获取当前 Request/Response（通过 RequestContextHolder） |
| `getParameter(String)` / `getParameterToInt(String)` | 获取请求参数 |
| `getHeader(String)` | 获取请求头 |
| `setCookie(HttpServletResponse, String, String)` / `deleteCookie()` | Cookie 操作 |
| `renderString(HttpServletResponse, String)` | 将字符串渲染到客户端 |
| `urlEncode(String)` / `urlDecode(String)` | URL 编解码 |
| `getIpAddr(HttpServletRequest)` | 获取客户端 IP 地址 |

##### PageUtils - 分页工具

与 PageHelper 集成的分页工具。

**核心方法：**

| 方法 | 说明 |
|------|------|
| `startPage()` | 开始分页（从请求参数读取 pageNum, pageSize） |
| `clearPage()` | 清理分页的线程变量 |

**实现原理：**
- 从 Request 中读取分页参数：
  - `pageNum` - 页码（默认 1）
  - `pageSize` - 每页数量（默认 10）
  - `orderByColumn` - 排序列
  - `isAsc` - 排序方向

##### DictUtils - 字典工具

从 Redis 缓存中获取字典数据。

**核心方法：**

| 方法 | 说明 |
|------|------|
| `getDictLabel(String, String, String)` | 根据字典类型和值获取标签 |
| `getDictValue(String, String, String)` | 根据字典类型和标签获取值 |
| `getDictCache(String)` | 获取字典缓存 |
| `setDictCache(String, List<SysDictData>)` | 设置字典缓存 |
| `removeDictCache(String, String)` | 删除指定字典缓存 |
| `clearDictCache()` | 清空所有字典缓存 |

**缓存 Key 格式：**
- `SYS_DICT_KEY + dictType`

##### Arith - 精确计算工具

解决浮点数精度问题的精确计算工具。

**核心方法：**

| 方法 | 说明 |
|------|------|
| `add(double, double)` | 精确加法 |
| `sub(double, double)` | 精确减法 |
| `mul(double, double)` | 精确乘法 |
| `div(double, double)` / `div(double, double, int)` | 精确除法（可指定保留小数位数） |
| `round(double, int)` | 四舍五入（指定小数位数） |

**实现原理：**
- 使用 `BigDecimal` 进行精确计算
- 避免 `double` 类型的精度丢失问题

##### 其他顶层工具

| 工具类 | 主要功能 |
|--------|---------|
| **Threads** | 线程工具（sleep、关闭线程池） |
| **MessageUtils** | 国际化消息工具（从 i18n 资源文件获取消息） |
| **LogUtils** | 日志操作记录工具（异步记录操作日志） |
| **ExceptionUtil** | 异常信息获取工具（获取异常堆栈信息） |

#### barcode 子包 - 条码/二维码工具

**BarcodeUtil** 基于 barcode4j 和 zxing 库生成一维码和二维码。

**核心方法：**

| 方法 | 说明 |
|------|------|
| `generateCode128(String, int, int)` | 生成 Code128 一维码 |
| `generateCode39(String, int, int)` | 生成 Code39 一维码 |
| `generateEAN13(String, int, int)` | 生成 EAN-13 一维码 |
| `generateQRCode(String, int, int)` | 生成 QR Code 二维码 |
| `decodeQRCode(BufferedImage)` | 解析二维码内容 |

**第三方依赖说明：**
- **barcode4j**：一维码生成（Code128、Code39、EAN-13 等）
- **zxing**：二维码生成和解析（QR Code）

#### bean 子包 - Bean 工具

**BeanUtils**：

| 方法 | 说明 |
|------|------|
| `copyBean(Object, Class<T>)` | Bean 属性复制（返回新对象） |
| `copyProperties(Object, Object)` | Bean 属性复制（目标对象已存在） |
| `beanToMap(Object)` | Bean 转 Map |
| `mapToBean(Map<String, Object>, Class<T>)` | Map 转 Bean |
| `getProperty(Object, String)` | 获取属性值 |
| `setProperty(Object, String, Object)` | 设置属性值 |

**BeanValidators**：JSR-303 Bean 校验工具

| 方法 | 说明 |
|------|------|
| `validate(T)` | 校验对象，返回约束冲突集合 |
| `validateWithException(T)` | 校验对象，失败则抛出 ConstraintViolationException |
| `validateProperty(T, String)` | 校验对象的单个属性 |
| `extractMessage(Set<ConstraintViolation<T>>)` | 提取校验错误消息 |

#### file 子包 - 文件工具

**FileUtils**：文件基础操作

| 方法 | 说明 |
|------|------|
| `writeBytes(byte[], String)` | 将字节数组写入文件 |
| `readFileToBytes(String)` | 读取文件到字节数组 |
| `writeString(String, String)` | 将字符串写入文件 |
| `readFileToString(String)` | 读取文件到字符串 |
| `copyFile(String, String)` | 复制文件 |
| `deleteFile(String)` / `deleteDirectory(String)` | 删除文件/目录 |
| `getFileExtension(String)` | 获取文件扩展名 |
| `getFileName(String)` | 获取文件名（不含路径） |

**FileUploadUtils**：文件上传处理

| 方法 | 说明 |
|------|------|
| `upload(String baseDir, MultipartFile)` | 上传文件（返回访问 URL） |
| `upload(MultipartFile)` | 上传文件到默认目录 |
| `getExtension(MultipartFile)` | 获取上传文件扩展名 |
| `assertAllowed(String extension)` | 校验文件扩展名是否允许 |

**配置项：**
- 最大文件大小：从 RuoYiConfig 获取
- 允许的扩展名：可配置白名单

**FileTypeUtils**：根据文件头判断真实文件类型

| 方法 | 说明 |
|------|------|
| `getFileType(String fileName)` | 根据文件名获取类型 |
| `getFileExtendName(byte[] headerBytes)` | 根据文件头字节获取扩展名 |

**支持的文件类型：**
- 图片：JPG, PNG, GIF, BMP
- 文档：PDF, DOC, DOCX, XLS, XLSX, PPT, PPTX
- 压缩包：ZIP, RAR, 7Z
- 视频：MP4, AVI, MOV
- 音频：MP3, WAV

**ImageUtils**：图片处理

| 方法 | 说明 |
|------|------|
| `getImage(String path)` | 读取图片为字节数组 |
| `compressImage(String sourcePath, String targetPath)` | 压缩图片 |
| `scaleImage(String sourcePath, String targetPath, int width, int height)` | 缩放图片 |
| `convertImageFormat(String sourcePath, String targetPath, String format)` | 转换图片格式 |

**MimeTypeUtils**：MIME 类型判断

| 方法 | 说明 |
|------|------|
| `getMimeType(String fileName)` | 根据文件名获取 MIME 类型 |
| `getExtension(String mimeType)` | 根据 MIME 类型获取扩展名 |
| `belongsTo(String mimeType, String parentType)` | 判断 MIME 类型是否属于某一类 |

**常用 MIME 类型映射：**
| 扩展名 | MIME 类型 |
|--------|-----------|
| .txt | text/plain |
| .html | text/html |
| .css | text/css |
| .js | application/javascript |
| .json | application/json |
| .xml | application/xml |
| .pdf | application/pdf |
| .doc | application/msword |
| .docx | application/vnd.openxmlformats-officedocument.wordprocessingml.document |
| .xls | application/vnd.ms-excel |
| .xlsx | application/vnd.openxmlformats-officedocument.spreadsheetml.sheet |
| .jpg | image/jpeg |
| .png | image/png |
| .gif | image/gif |
| .mp3 | audio/mpeg |
| .mp4 | video/mp4 |
| .zip | application/zip |

**MinioUtil**：MinIO 对象存储工具

| 方法 | 说明 |
|------|------|
| `uploadFile(String bucketName, String fileName, MultipartFile)` | 上传文件到 MinIO |

**上传流程：**
1. 从 Spring 容器获取 `MinioClient` Bean
2. 调用 `putObject()` 上传文件
3. 调用 `getPresignedObjectUrl()` 获取访问 URL
4. 去掉签名参数，返回永久访问 URL

**第三方依赖：**
- `io.minio.MinioClient` - MinIO Java 客户端
- 使用的方法：
  - `MinioClient.builder().endpoint().credentials().build()` - 构建客户端
  - `PutObjectArgs.builder().bucket().object().stream().contentType().build()` - 构建上传参数
  - `GetPresignedObjectUrlArgs.builder().bucket().object().method().build()` - 构建获取 URL 参数

#### html 子包 - HTML 工具

**EscapeUtil**：HTML/JS 转义与反转义

| 方法 | 说明 |
|------|------|
| `escape(String)` | HTML 转义（< 转 &lt; 等） |
| `unescape(String)` | HTML 反转义（&lt; 转 < 等） |
| `escapeJavaScript(String)` | JavaScript 转义 |
| `unescapeJavaScript(String)` | JavaScript 反转义 |

**转义规则：**
| 原字符 | 转义后 |
|--------|--------|
| < | &lt; |
| > | &gt; |
| " | &quot; |
| ' | &#39; |
| & | &amp; |
| / | &#47; |

**HTMLFilter**：基于白名单的 HTML 标签过滤

| 方法 | 说明 |
|------|------|
| `filter(String html)` | 过滤 HTML，只保留白名单标签 |

**白名单标签：**
- 允许的标签：`a`, `b`, `blockquote`, `br`, `caption`, `cite`, `code`, `col`, `colgroup`, `dd`, `del`, `div`, `dl`, `dt`, `em`, `h1`-`h6`, `i`, `img`, `li`, `ol`, `p`, `pre`, `q`, `small`, `span`, `strike`, `strong`, `sub`, `sup`, `table`, `tbody`, `td`, `tfoot`, `th`, `thead`, `tr`, `u`, `ul`
- 允许的属性：`href`, `src`, `width`, `height`, `alt`, `title`, `class`, `style`, `target`

**过滤策略：**
- 移除不在白名单中的标签
- 移除不在白名单中的属性
- 移除 `javascript:` 协议的链接
- 移除 `on*` 事件属性

#### http 子包 - HTTP 工具

**HttpUtils**：HTTP 客户端封装

| 方法 | 说明 |
|------|------|
| `sendGet(String url)` | 发送 GET 请求 |
| `sendGet(String url, Map<String, String> params)` | 发送带参数的 GET 请求 |
| `sendPost(String url, String data)` | 发送 POST 请求（JSON 数据） |
| `sendPost(String url, Map<String, String> params)` | 发送 POST 请求（表单数据） |
| `sendPost(String url, Map<String, String> headers, Map<String, String> params)` | 发送带 Header 的 POST 请求 |

**实现原理：**
- 使用 Java 原生 `HttpURLConnection`
- 支持 HTTP 和 HTTPS
- 自动处理编码和响应解析

**HttpHelper**：HTTP 请求辅助

| 方法 | 说明 |
|------|------|
| `getBodyString(HttpServletRequest)` | 从请求中获取 Body 字符串 |
| `getParameterMap(HttpServletRequest)` | 获取请求参数 Map |

#### ip 子包 - IP 工具

**IpUtils**：获取客户端真实 IP 地址

| 方法 | 说明 |
|------|------|
| `getIpAddr(HttpServletRequest)` | 获取客户端真实 IP |

**IP 查找顺序：**
1. `X-Forwarded-For`
2. `Proxy-Client-IP`
3. `WL-Proxy-Client-IP`
4. `HTTP_CLIENT_IP`
5. `HTTP_X_FORWARDED_FOR`
6. `X-Real-IP`
7. `request.getRemoteAddr()`

**特殊处理：**
- 多个 IP 时取第一个非 unknown 的 IP
- `0:0:0:0:0:0:0:1` 转换为 `127.0.0.1`

**AddressUtils**：通过 IP 地址解析地理位置

| 方法 | 说明 |
|------|------|
| `getRealAddressByIP(String ip)` | 获取 IP 对应的真实地址 |

**实现方式：**
- 支持纯真 IP 库（本地文件）
- 支持在线接口查询（如淘宝 IP 库）
- 内网 IP 返回 "内网IP"
- 解析失败返回 "XX XX"

#### poi 子包 - Excel 工具

**ExcelUtil**：功能强大的 Excel 导入导出工具

**核心功能：**
- 基于 `@Excel` 注解的配置化导出
- 日期格式化、字典转换、表达式转换
- 图片导出、下拉列表、数据统计
- 大数据量流式写入（SXSSF）防止内存溢出
- 导入数据解析、类型转换、校验

**导出示例：**
```java
// 1. 定义实体类
public class User {
    @Excel(name = "用户ID")
    private Long userId;
    
    @Excel(name = "用户名称")
    private String userName;
    
    @Excel(name = "性别", readConverterExp = "0=男,1=女,2=未知")
    private String sex;
    
    @Excel(name = "状态", dictType = "sys_user_status")
    private String status;
    
    @Excel(name = "创建时间", dateFormat = "yyyy-MM-dd HH:mm:ss")
    private Date createTime;
}

// 2. 导出
ExcelUtil<User> util = new ExcelUtil<>(User.class);
util.exportExcel(response, userList, "用户数据");

// 3. 导入
List<User> list = util.importExcel(inputStream);
```

**@Excel 注解常用属性：**
| 属性 | 类型 | 说明 |
|------|------|------|
| name | String | 列名 |
| sort | int | 导出排序 |
| dateFormat | String | 日期格式 |
| dictType | String | 字典类型 |
| readConverterExp | String | 读取转换表达式（如 "0=男,1=女"） |
| separator | String | 分隔符 |
| scale | int | BigDecimal 精度 |
| cellType | ColumnType | 列类型（NUMERIC/STRING/IMAGE） |
| type | Type | 导出类型（ALL/EXPORT/IMPORT） |
| isExport | boolean | 是否导出 |
| isStatistics | boolean | 是否自动统计 |

**ExcelUtil 核心方法：**

| 方法 | 说明 |
|------|------|
| `exportExcel(List<T>, String)` | 导出 Excel 到文件 |
| `exportExcel(HttpServletResponse, List<T>, String)` | 导出 Excel 到响应流 |
| `exportExcel(HttpServletResponse, List<T>, String, String)` | 导出 Excel（带标题） |
| `importExcel(InputStream)` | 从输入流导入 Excel |
| `importExcel(InputStream, int)` | 从输入流导入 Excel（指定标题行数） |
| `importTemplateExcel(HttpServletResponse, String)` | 导出导入模板 |

**大数据量处理：**
- 使用 `SXSSFWorkbook` 进行流式写入
- 内存中只保留指定数量的行（默认 500 行）
- 超过部分写入临时文件，避免 OOM

**图片导出：**
- 支持 `ColumnType.IMAGE` 类型
- 可以是图片路径或字节数组
- 自动调整单元格大小

**下拉列表：**
- 通过 `combo()` 属性设置下拉选项
- 通过 `prompt()` 属性设置提示信息
- 支持从字典动态获取选项

**数据统计：**
- 通过 `isStatistics = true` 启用
- 自动对数值列求和
- 在最后一行显示"合计"

**ExcelHandlerAdapter**：自定义数据处理器接口

```java
public interface ExcelHandlerAdapter {
    /**
     * 格式化
     * @param value 单元格数据值
     * @param args excel注解args参数组
     * @return 处理后的值
     */
    Object format(Object value, String[] args);
}
```

使用场景：需要自定义复杂的数据转换逻辑时，可以实现此接口。

#### reflect 子包 - 反射工具

**ReflectUtils**：反射操作工具类

**核心方法：**

| 方法 | 说明 |
|------|------|
| `getFieldValue(Object, String)` | 获取对象的字段值 |
| `setFieldValue(Object, String, Object)` | 设置对象的字段值 |
| `invokeMethod(Object, String, Class<?>[], Object[])` | 调用对象的方法 |
| `invokeGetter(Object, String)` | 调用对象的 getter 方法 |
| `invokeSetter(Object, String, Object)` | 调用对象的 setter 方法 |
| `getAccessibleField(Class<?>, String)` | 获取可访问的字段 |
| `getAccessibleMethod(Class<?>, String, Class<?>...)` | 获取可访问的方法 |
| `getSuperClassGenricType(Class<?>)` | 获取父类的泛型类型 |

**特殊处理：**
- 自动处理私有字段/方法的访问权限（setAccessible(true)）
- 支持多级属性访问（如 "user.dept.name"）
- 支持继承的字段和方法

#### sign 子包 - 签名/加密工具

**Base64**：Base64 编解码

| 方法 | 说明 |
|------|------|
| `encode(byte[])` | 字节数组编码为 Base64 字符串 |
| `encode(String)` | 字符串编码为 Base64 字符串 |
| `decode(String)` | Base64 字符串解码为字节数组 |
| `decodeToString(String)` | Base64 字符串解码为字符串 |

**Md5Utils**：MD5 加密

| 方法 | 说明 |
|------|------|
| `encrypt(String)` | MD5 加密（32 位小写） |
| `encrypt(String, String salt)` | MD5 加密（加盐） |
| `encryptUpper(String)` | MD5 加密（32 位大写） |

**加盐策略：**
- 通常将盐值存储在数据库中
- 加密方式：`MD5(password + salt)` 或 `MD5(salt + password)`

#### spring 子包 - Spring 上下文工具

**SpringUtils**：Spring 容器操作工具

| 方法 | 说明 |
|------|------|
| `getBean(String name)` | 通过名称获取 Bean |
| `getBean(Class<T> clazz)` | 通过类型获取 Bean |
| `getBean(String name, Class<T> clazz)` | 通过名称和类型获取 Bean |
| `containsBean(String name)` | 判断容器中是否包含指定名称的 Bean |
| `isSingleton(String name)` | 判断是否为单例 Bean |
| `getType(String name)` | 获取 Bean 的类型 |
| `getAliases(String name)` | 获取 Bean 的别名 |

**实现原理：**
- 实现 `ApplicationContextAware` 接口
- 在 Spring 容器启动时注入 `ApplicationContext`
- 通过 `ApplicationContext` 操作 Bean

**使用场景：**
- 非 Spring 管理的类需要访问 Spring Bean
- 动态获取 Bean（根据名称或类型）
- 检查 Bean 是否存在

#### sql 子包 - SQL 工具

**SqlUtil**：SQL 安全工具

| 方法 | 说明 |
|------|------|
| `escapeOrderBySql(String orderBy)` | 转义排序字段（防止 SQL 注入） |
| `filterKeyword(String value)` | 过滤 SQL 关键字 |
| `isValidOrderByField(String field)` | 验证排序字段是否合法 |
| `hasOrderByInject(String value)` | 检查是否包含 SQL 注入风险 |

**安全检查：**
- 检查是否包含 SQL 关键字（如 `UNION`, `SELECT`, `INSERT`, `DELETE`, `UPDATE`, `DROP`, `--`, `;` 等）
- 检查是否包含特殊字符
- 只允许字母、数字、下划线、逗号、空格
- 只允许 `asc` 和 `desc` 作为排序方向

**使用场景：**
- 动态排序时，对前端传递的排序字段进行校验
- 防止 SQL 注入攻击

#### uuid 子包 - UUID/ID 工具

**UUID**：UUID 生成工具

| 方法 | 说明 |
|------|------|
| `randomUUID()` | 生成随机 UUID（带横杠，如 550e8400-e29b-41d4-a716-446655440000） |
| `simpleUUID()` | 生成简化 UUID（去掉横杠，如 550e8400e29b41d4a716446655440000） |
| `fastUUID()` | 使用 ThreadLocalRandom 生成高性能 UUID |
| `uuid32()` | 生成 32 位 UUID（无横杠） |

**IdUtils**：ID 生成器

| 方法 | 说明 |
|------|------|
| `snowflakeId()` | 生成雪花算法 ID（long 类型） |
| `randomUUID()` | 生成 UUID 字符串 |
| `objectId()` | 生成 MongoDB ObjectId 风格的 ID |

**雪花算法（Snowflake）：**
- 64 位 long 类型
- 1 位符号位 + 41 位时间戳 + 10 位工作机器 ID + 12 位序列号
- 支持分布式环境下的唯一 ID 生成
- 趋势递增

**Seq**：序列生成器

| 方法 | 说明 |
|------|------|
| `getId()` | 生成日期+序列号格式的 ID（如 202404240001） |
| `getId(String prefix)` | 生成带前缀的 ID（如 ORDER202404240001） |
| `nextId()` | 生成下一个序列号 |

**实现原理：**
- 日期格式：yyyyMMdd
- 序列号：4 位数字，每日从 1 开始
- 支持线程安全

---

## 9. xss 包

### 代码结构
```
com.ktg.common.xss/
├── Xss.java              # XSS校验注解
└── XssValidator.java     # XSS校验器实现
```

### 依赖关系
- **Xss** 注解：
  - **第三方依赖**：`javax.validation`（Bean Validation API），使用 `@Constraint` 元注解
- **XssValidator**：
  - **第三方依赖**：`javax.validation.ConstraintValidator` 接口
  - 依赖本模块：`utils.html.HTMLFilter`（HTML 过滤逻辑）

### 功能
该包提供基于 Bean Validation 的 XSS 防护注解。

#### @Xss - XSS 校验注解

**作用目标：**
- `METHOD` - 方法
- `FIELD` - 字段
- `CONSTRUCTOR` - 构造器
- `PARAMETER` - 参数

**属性：**
| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| message | String | "不允许任何脚本运行" | 校验失败时的提示消息 |
| groups | Class<?>[] | {} | 校验分组 |
| payload | Class<? extends Payload>[] | {} | 负载信息 |

#### XssValidator - XSS 校验器实现

实现 `ConstraintValidator<Xss, String>` 接口。

**校验逻辑：**
```java
@Override
public boolean isValid(String value, ConstraintValidatorContext constraintValidatorContext) {
    if (value == null) {
        return true;  // null 值通过校验（由 @NotNull 负责）
    }
    // 使用 HTMLFilter 过滤后与原值比较
    String filtered = HTMLFilter.filter(value);
    return value.equals(filtered);
}
```

**校验原理：**
- 如果过滤后的字符串与原字符串不同，说明包含 HTML 标签或脚本
- 此时校验失败，返回 false

#### 与 XssFilter 的区别

| 特性 | XssFilter | @Xss 注解 |
|------|-----------|-----------|
| 实现方式 | Servlet Filter | Bean Validation |
| 作用时机 | 请求进入时 | 数据绑定时 |
| 处理方式 | HTML 转义 | 校验并拒绝 |
| 粒度 | 全局（可配置排除） | 字段级（精确控制） |
| 使用场景 | 通用防护 | 特定字段校验 |

**使用建议：**
- **XssFilter**：作为第一道防线，对所有请求参数进行转义
- **@Xss 注解**：作为第二道防线，对特定字段进行严格校验
- 两者配合使用，提供多层防护

#### 使用示例

```java
public class Article {
    /**
     * 文章标题（不允许包含 HTML）
     */
    @Xss(message = "标题不能包含脚本内容")
    private String title;
    
    /**
     * 文章内容（允许包含部分 HTML 标签）
     * 注意：这里不使用 @Xss，因为内容需要保留部分 HTML
     * 由业务代码使用 HTMLFilter 进行白名单过滤
     */
    private String content;
    
    /**
     * 作者名称
     */
    @Xss
    private String author;
}
```

**校验触发：**
- 在 Controller 方法参数上使用 `@Valid` 或 `@Validated` 注解
- Spring MVC 会自动执行校验

```java
@PostMapping("/article")
public AjaxResult add(@Valid @RequestBody Article article) {
    // article 已通过 @Xss 校验
    return AjaxResult.success();
}
```

---

## ktg-common 模块功能汇总

### 模块定位

ktg-common 是 ktg-mes 项目的**公共通用模块**，为整个系统提供基础设施和通用能力支持。该模块不包含任何业务逻辑，而是被其他所有模块（ktg-admin、ktg-framework、ktg-mes 等）依赖使用。

### 模块职责

#### 1. AOP 注解体系
提供声明式编程能力，通过切面在运行时增强方法行为：
- `@DataScope` - 数据权限过滤
- `@DataSource` - 多数据源切换
- `@Excel/@Excels` - Excel 导入导出配置
- `@Log` - 操作日志记录
- `@RateLimiter` - 接口限流
- `@RepeatSubmit` - 防重复提交

#### 2. 配置管理
统一管理系统配置项的读取和访问：
- `RuoYiConfig` - 项目全局配置（名称、版本、上传路径等）
- `MinioConfig` - MinIO 对象存储配置

#### 3. 常量定义
集中管理所有魔法数字和字符串常量：
- `Constants` - 通用常量（字符集、Redis Key 前缀、JWT 声明等）
- `HttpStatus` - HTTP 状态码
- `UserConstants` - 用户相关常量
- `GenConstants` - 代码生成常量
- `ScheduleConstants` - 定时任务常量

#### 4. 核心领域模型
定义系统基础实体类和统一响应：
- `BaseEntity` - 实体基类（审计字段）
- `AjaxResult` - 统一 API 响应对象
- `BaseController` - 控制器基类（分页、响应封装）
- `RedisCache` - Redis 缓存封装
- `PageDomain/TableDataInfo` - 分页支持

#### 5. 枚举类型
定义所有业务枚举，确保类型安全：
- `BusinessType` - 业务操作类型
- `DataSourceType` - 数据源类型
- `UserStatus` - 用户状态
- `LimitType` - 限流类型
- `OperatorType` - 操作人类型

#### 6. 异常体系
构建完整的自定义异常继承体系：
- `BaseException` - 基础异常（支持国际化）
- `ServiceException` - 服务层异常（最常用）
- `FileException` 系列 - 文件上传异常
- `UserException` 系列 - 用户认证异常

#### 7. Web 过滤器
提供 HTTP 请求的预处理和后处理：
- `XssFilter` - XSS 攻击防护
- `RepeatableFilter` - 可重复读请求体

#### 8. 工具类库
提供开发中最常用的各种工具方法：
| 工具类 | 功能 |
|--------|------|
| `StringUtils` | 字符串处理（空判断、转换、格式化） |
| `DateUtils` | 日期时间处理（格式化、解析、计算） |
| `SecurityUtils` | 安全工具（用户信息、密码加密） |
| `ServletUtils` | Servlet 操作（Request/Response、Cookie） |
| `ExcelUtil` | Excel 导入导出（基于注解） |
| `RedisCache` | Redis 缓存操作 |
| `BeanUtils` | Bean 属性复制 |
| `FileUtils/FileUploadUtils` | 文件操作和上传 |
| `HttpUtils` | HTTP 客户端 |
| `Arith` | 精确浮点计算 |
| `DictUtils` | 字典工具 |
| `BarcodeUtil` | 条码/二维码生成 |
| `MinioUtil` | MinIO 对象存储 |
| `IpUtils/AddressUtils` | IP 地址获取和解析 |

### 技术栈与第三方依赖

| 第三方库 | 版本 | 用途 | 关键类/方法 |
|---------|------|------|------------|
| **Spring Framework** | - | IoC/DI、Web 基础 | `ApplicationContext`, `WebDataBinder` |
| **Spring Security** | - | 安全认证与授权 | `SecurityContextHolder`, `BCryptPasswordEncoder` |
| **Spring Data Redis** | - | Redis 缓存操作 | `RedisTemplate`, `ValueOperations` |
| **PageHelper** | - | MyBatis 分页插件 | `PageHelper.startPage()`, `PageInfo` |
| **Apache Commons Lang3** | - | 基础工具类 | `StringUtils`, `DateUtils`（继承扩展） |
| **Apache Commons IO** | - | IO 操作 | `FileUtils`, `IOUtils` |
| **Apache POI** | - | Excel 处理 | `Workbook`, `Sheet`, `SXSSFWorkbook`（流式写入） |
| **Jackson** | - | JSON 序列化 | `ObjectMapper`, `@JsonFormat` |
| **Fastjson** | - | JSON 处理 | `JSON`, `JSONObject` |
| **MinIO SDK** | 8.2.1 | 对象存储 | `MinioClient`, `PutObjectArgs` |
| **Barcode4j** | 2.0 | 一维码生成 | `Code128Bean`, `EAN13Bean` |
| **ZXing** | 3.3.3 | 二维码生成/解析 | `QRCodeWriter`, `BitMatrix` |
| **JJWT** | - | JWT 令牌处理 | `Jwts`, `Claims` |
| **UserAgentUtils** | - | 客户端信息解析 | `UserAgent`, `Browser` |
| **Hutool** | 5.8.0.M3 | Java 工具包（备用） | 各种工具类 |
| **UReport** | 2.2.9 | 报表引擎 | 报表设计与生成 |
| **SnakeYAML** | - | YAML 解析 | 配置文件解析 |

### 模块依赖关系

```
                    ┌─────────────────┐
                    │   ktg-common    │
                    │  (本模块: 公共) │
                    └────────┬────────┘
                             │
            ┌────────────────┼────────────────┐
            │                │                │
            ▼                ▼                ▼
    ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
    │  ktg-admin  │  │ktg-framework│  │  ktg-mes    │
    │  (管理后台)  │  │  (框架层)   │  │  (MES业务)  │
    └─────────────┘  └─────────────┘  └─────────────┘
            │
            ▼
    ┌─────────────┐
    │ktg-generator│
    │ (代码生成)   │
    └─────────────┘
```

### 设计亮点

#### 1. 分层清晰
按功能职责划分为 9 个包，职责单一明确：
- `annotation` - 声明式注解
- `config` - 配置管理
- `constant` - 常量定义
- `core` - 核心模型和控制器
- `enums` - 枚举类型
- `exception` - 异常体系
- `filter` - Web 过滤器
- `utils` - 工具类库
- `xss` - XSS 防护注解

#### 2. 声明式编程
大量使用注解 + AOP 模式，使业务代码更简洁：
- 横切关注点（日志、权限、限流、事务）与业务逻辑分离
- 通过注解配置行为，而非硬编码

#### 3. 统一响应
所有 API 响应统一使用 `AjaxResult`：
- 前端处理标准化
- 状态码、消息、数据结构清晰
- 支持链式调用

#### 4. 完整异常体系
- 继承 `RuntimeException`，无需强制捕获
- 支持错误码和国际化消息
- 按业务场景细分异常类型

#### 5. 工具类封装
对第三方库进行二次封装：
- 提供更友好的 API
- 屏蔽底层实现细节
- 统一异常处理
- 便于未来替换实现

#### 6. 多层安全防护
- **XSS 防护**：`XssFilter`（全局转义）+ `@Xss`（字段校验）
- **SQL 注入防护**：`SqlUtil`（排序字段校验）
- **密码安全**：`BCryptPasswordEncoder`（不可逆加密）
- **防重提交**：`@RepeatSubmit`（时间窗口限制）
- **接口限流**：`@RateLimiter`（请求频率控制）

#### 7. 性能优化
- **Excel 大数据处理**：使用 `SXSSFWorkbook` 流式写入，避免 OOM
- **Redis 封装**：统一缓存操作，减少样板代码
- **请求体可重复读**：`RepeatableFilter` 支持日志记录和防重校验
- **日期工具优化**：缓存 `SimpleDateFormat`，避免重复创建

### 与其他模块的交互

#### 被依赖方式
ktg-common 作为所有其他模块的依赖，通过以下方式被使用：

1. **Controller 继承**
   ```java
   @RestController
   @RequestMapping("/user")
   public class UserController extends BaseController {
       // 继承 startPage()、success()、error() 等方法
   }
   ```

2. **实体继承**
   ```java
   public class SysUser extends BaseEntity {
       // 继承 createBy、createTime、updateBy、updateTime 等字段
   }
   ```

3. **注解使用**
   ```java
   @Log(title = "用户管理", businessType = BusinessType.INSERT)
   @PostMapping
   public AjaxResult add(@RequestBody SysUser user) { ... }
   ```

4. **工具类调用**
   ```java
   String username = SecurityUtils.getUsername();
   Date now = DateUtils.getNowDate();
   if (StringUtils.isEmpty(name)) { ... }
   ```

5. **异常抛出**
   ```java
   if (user == null) {
       throw new ServiceException("用户不存在");
   }
   ```

### 文件位置

本文档位于：`docs/modules/ktg-common-模块说明.md`

---

**文档生成时间**：2026-04-24  
**模块版本**：3.8.2  
**基于代码分析**：ktg-common/src/main/java/com/ktg/common/