# Spring Boot 初学学习参考

从认识后端到读懂接口、数据库与常见报错



如果你刚开始接触 Java 后端，可以用这份资料认识 Spring Boot 的常见组成；如果已经写过接口，也可以按目录查找数据库、登录、事务和联调问题。它是一份学习参考，不是开发计划，也不是可直接上线的完整工程。技术组合、目录和示例用于说明知识，不需要逐项照搬。

## 1. Spring Boot 是什么，负责什么

可以先把一个 Web 应用理解为三部分：前端负责页面与交互，后端接收请求、检查规则并处理数据，数据库负责持久保存。Spring Boot 常用于搭建 Java 后端，前端通过 HTTP 接口与它交互，不必使用 Java 编写。

Java 是编程语言；Spring 提供依赖注入等基础能力；Spring Boot 在 Spring 之上帮助组织依赖、自动配置和应用启动；Spring MVC 处理常见 Web 请求；Spring Security 提供认证与授权基础设施。它们不是五种互相替代的语言，也不会自动替你设计业务规则。

阅读代码时会经常遇到类、接口、异常、集合和 Maven。如果这些词还不熟悉，可以边查 Java 基础边看一个小接口，不必先背完所有注解，也不必同时学习很多中间件。

### 沿一次请求理解后端

浏览器点击“保存订单” → HTTP 请求 → 身份检查 → 参数校验 → 业务服务 → 数据库事务 → 保存记录 → 返回结果 → 页面展示。

能够沿代码找到这条路径，比背出一串注解更重要。

### 常见组件各做什么

| 部分 | 参考选择 | 作用 |
| --- | --- | --- |
| 前端 | Vue / React 等 | 页面、输入、请求与结果反馈 |
| 后端 | Java + Spring Boot + Spring MVC | HTTP API 与业务处理 |
| 数据访问 | Spring Data JPA，或 MyBatis / JDBC 任选一条 | 查询、持久化与事务配合 |
| 数据库 | PostgreSQL；已熟悉其他关系库也可等价实现 | 共享保存、约束、事务与并发控制 |
| 身份安全 | Spring Security | 认证与服务端授权的基础设施 |
| 参数校验 | Bean Validation | 请求结构和字段校验 |
| 数据迁移 | Flyway 或等价工具 | 记录并执行 schema 变更 |
| 自动测试 | 项目版本配套的 Spring 测试、JUnit | 单测与集成测试入口 |
| xlsx 导出 | Apache POI 等成熟库 | 生成电子表格文件 |

这张表用于认识职责，不是安装清单。初学时通常选择一条数据访问路线即可，不必同时引入 JPA、MyBatis 和多套重复的数据层。Redis、JWT、消息队列、微服务、Kubernetes 都不是学习第一条接口的前提。

## 2. 官方资料从哪里看

官方入门教程通常只解释一件事，适合配合代码阅读。教程里的临时内存库、内存用户和演示页面是为了缩小示例范围，不代表真实应用也应采用同样的存储和安全设计。

| 编号 | 官方资料 | 主要知识 | 阅读时留意 |
| --- | --- | --- | --- |
| 1 | S01 REST 入门 | 请求、Controller、JSON | 路径如何映射到方法，返回值如何变成响应 |
| 2 | S02 JPA 入门 | Entity、Repository、持久化 | 示例使用什么数据库，数据何时写入 |
| 3 | S03 Spring Security 入门 | 登录与受保护资源 | 未登录与没有权限有什么区别 |
| 4 | S04 文件上传入门 | Multipart、存储边界 | 文件内容与数据库记录不是同一种存储 |
| 5 | S05 运行要求与 S06 入门 | JDK、构建工具、启动 | 教程版本是否与你的工程匹配 |
| 6 | S07 数据初始化与迁移 | 数据库结构的来源 | 自动建表与版本迁移有什么区别 |
| 7 | S08/S09 事务 | 代理、回滚条件 | 注解在哪个调用边界生效 |
| 8 | S10—S13 安全专题 | 权限、密码、CSRF、CORS | 每种保护分别解决什么问题 |
| 9 | S14/S15 测试 | 测试范围与隔离依赖 | Mock 测试和真实数据库测试的边界 |

末尾给出完整官方链接。可以从当前不理解的概念进入，不需要一次读完整个 Spring 文档。

## 3. 环境、版本和第一条接口

### 3.1 先确定一组能配合的版本

可以从 JDK 21 这样的长期支持版本开始考虑，但必须核对所选 Spring Boot 的运行要求。若你已有 Java 17 或其他版本，不要凭这份资料推断一定兼容。

在 Spring Initializr 选择非 SNAPSHOT、非里程碑的正式版本，再核对该版本的文档、JDK 和构建要求。记录具体版本，不在 README 里只写“最新版”。初学阶段不要跟着旧教程手动覆盖 Boot 管理的 Spring Framework、Security 或 Hibernate 版本。[S05、S06、S16]

本资料不固定 Boot 的最新补丁号，避免将来照着过期号码安装。它也不要求已能稳定运行的同学临时升级。Boot 3 与 Boot 4 的依赖、测试模块和部分用法不能想当然混用，示例注解以你的版本文档为准。

Boot 3/4 的 Web 校验等场景使用 `jakarta.*`，不要与旧版教程中的对应 `javax.*` 混贴。并非 Java 中所有 `javax` 包都要替换。

### 3.2 在 Windows 先检查这些

以下是检查命令，不会替你安装工具：

```powershell
java -version
javac -version
Get-Command java
$env:JAVA_HOME
```

进入你生成的后端目录后，再执行：

```powershell
.\mvnw.cmd -v
```

重点看 Maven 实际使用的 Java 版本，而不只是 IDE 右下角选了什么。`JAVA_HOME` 通常指向 JDK 目录，不是 `bin` 目录。IDE、终端、构建工具可能正在使用不同的 JDK。

保留生成工程里的 `mvnw`、`mvnw.cmd` 和 `.mvn/wrapper/`。它们帮助按项目约定启动 Maven，第一次运行可能需要联网下载；不是离线依赖包。[S17]

### 3.3 用 Initializr 创建，而不是从陌生整包改名

在 Spring Initializr 选择 Java、Maven、Jar，并按所选版本的界面添加 Web、Validation、数据访问、数据库驱动、Security 等实际依赖。迁移和测试组件也要按该版本核对；不要把不同教程的 `pom.xml` 拼在一起。

建议先生成工程、确认构建，再逐步配置数据库与安全。若生成时已经添加数据库和 Security，启动前就需要处理对应配置与默认保护；不要把启动失败理解为框架坏了。

生成的是通用脚手架。它帮助准备依赖和启动入口，不会自动生成业务规则、数据库设计或完整登录功能。

### 3.4 第一条只读接口长什么样

下面只说明 Controller 的作用，不是可直接交付的完整文件集合：

```java
package com.example.demo.web;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class PingController {
    @GetMapping("/api/v1/ping")
    public PingResponse ping() {
        return new PingResponse("ok");
    }

    public record PingResponse(String status) {}
}
```

将应用入口放在 `com.example.demo` 等共同父包下，便于默认组件扫描覆盖子包。`@RestController` 接收 HTTP 请求并返回数据，`@GetMapping` 绑定路径；JSON 输出不是在数据库中保存了一条记录。[S01、S06]

如果已加 Security，该请求可能先被认证保护；这正说明请求会先经过安全链。不要为了看到 `ok`，把整个 `/api/**` 都改成允许匿名。若需要匿名探针，仅精确开放这个不含业务数据的只读路径。

在工程已经配置完成、依赖可下载且端口可用时，命令形式为：

```powershell
.\mvnw.cmd spring-boot:run
```

另一个终端请求你实际配置的端口，例如后端确实使用 8080 时：

```powershell
Invoke-RestMethod -Uri 'http://localhost:8080/api/v1/ping'
```

启动完成后用原终端 `Ctrl+C` 停止。探针成功只能说明这条请求路径可用，不能证明数据库连接、登录和其他业务接口都正常。

## 4. Java 项目的文件放在哪里

下面是一种常见的 Maven 后端结构，用来解释文件职责。`orders` 和 `inventory` 只是业务包名称示例，不必把这棵树全部建出来。

```text
demo-backend/
  pom.xml
  mvnw
  mvnw.cmd
  .mvn/wrapper/
  README.md
  .gitignore
  src/
    main/java/com/example/demo/
      DemoApplication.java
      common/
      config/
      security/
      orders/
      inventory/
      ...实际业务模块
    main/resources/
      application.yml
      db/migration/
    test/java/com/example/demo/
    test/resources/
  target/                    本机构建产物，通常不纳入源码版本管理
```

| 文件或目录 | 完成什么职责 | 常见误放 |
| --- | --- | --- |
| `pom.xml` | 依赖、Java 版本、构建及测试插件 | 把库版本全手填，破坏版本配套 |
| `DemoApplication` | 程序入口、启动与组件扫描根 | 放得太深，扫描不到旁边模块 |
| `config/` | 已采用的应用配置装配 | 明文密码、所有业务规则 |
| `security/` | 身份、安全链、权限相关组件 | 用前端传入的角色当真实身份 |
| `orders/`、`inventory/` | 按业务组织实际代码 | 所有业务堆进一个 Controller |
| Controller | 路径、请求与响应适配 | 复杂库存计算、直接跨表写库 |
| DTO / request / response | 明确 API 输入和允许输出字段 | 直接把数据库实体原样返回 |
| Service | 业务规则、事务边界、协调写入 | 只转调 Repository，规则却散在页面 |
| Repository / Mapper | 数据查询与持久化 | 自己信任客户端给的操作者权限 |
| `db/migration/` | Flyway 已采用时的版本化 SQL | 修改已执行迁移，或只留建库截图 |
| `src/test/` | 可执行测试和合成测试数据 | 测试意外连接日常开发库 |
| `target/` | 编译结果、打包文件和测试报告 | 把构建产物当作源码修改 |

Controller、DTO、Service 等可以放在各自业务模块内部；小模块允许合并，不要求每层建一堆空文件。命名和包结构服务于职责，不按目录数量判断质量。

`src/main/java` 存放应用源码，`src/main/resources` 存放配置和其他资源；`src/test/java`、`src/test/resources` 对应测试代码和测试资源。前端如果采用 Node 工具链，会有自己的依赖和构建方式，不与 Maven 混用。

## 5. 数据库与配置：保存不是“刷新后还在”

### 5.1 配置从哪里读取

下面是 PostgreSQL + JPA 路线的配置片段，仅在已添加对应依赖和迁移工具时适用：

```yaml
server:
  port: ${SERVER_PORT:8080}

spring:
  datasource:
    url: ${DB_URL}
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}
  jpa:
    hibernate:
      ddl-auto: validate
    open-in-view: false
  flyway:
    enabled: true
```

`DB_URL` 类似 `jdbc:postgresql://localhost:5432/demo_dev`。库、账号和权限需要事先准备。配置中的 `validate` 不会替你创建表；需要迁移先建立 schema。关闭 Open EntityManager in View 后，查询到 DTO 的转换也应放在合适的服务/查询边界，不依赖页面序列化时偷偷查库。[S07、S27]

Spring Boot 不会仅因为根目录有一个 `.env`，就自动按某个 Node 工具的方式加载它。使用进程环境变量、IDE 运行配置，或经过明确配置的外部文件导入；不要把“写进 `.env`”等同于“运行进程已经读到”。[S18]

不要把真实密码写入 `application.yml`、README 或发给 AI。配置示例仅给键名和占位值。测试必须使用隔离数据库，不能让未设置的测试地址回退到开发库或其他项目的库。

### 5.2 schema 要有历史

如果采用 Flyway，常见版本迁移名如 `V1__create_customer.sql`。新增变更应新增迁移，而不是反复修改已经执行过的版本。新数据库也应能仅通过现存迁移达到当前结构。[S07]

不要把 Hibernate 的 `ddl-auto=update` 当作完整的交付迁移方案，更不能在正常库启用 `create-drop`。同一数据库不要让 Flyway、Hibernate 自动建表和随意执行的初始化脚本争夺 schema 管理权。

JPA 的字段注解不代表业务约束都已经落到数据库。核对实际迁移中的外键、唯一键、非空、检查约束和索引。测试“两个请求同时创建相同身份库存”时，数据库唯一约束必须确实存在。

### 5.3 JPA 与 MyBatis 不是一道都要选的题

JPA 路线适合用实体和 Repository 组织持久化；MyBatis / JDBC 路线更直接表达 SQL。两种路线都能开发数据库应用，也都需要正确处理事务、参数绑定、数据库约束和测试。

选 JPA 时，不建议把所有关联都改为立即加载来修复报错；先明确一次接口需要什么字段，再设计查询与 DTO。选 MyBatis 时，不要拼接用户输入生成 SQL；排序字段等不能直接参数绑定的位置，要使用允许列表映射。

### 5.4 金额与时间

金额可用 `BigDecimal` 或整数分表示，并与数据库类型及接口约定一致。`new BigDecimal("0.10")` 能直接表达这个十进制值；从 `double` 构造可能带入二进制浮点误差。保留几位小数、何时舍入、先逐项舍入还是先合计，应由业务约定确定，不能在不同地方随意处理。[S19]

接口可约定金额使用十进制字符串，再由前端按约定格式展示；不要让 Java 后端正确计算后，前端又用浮点数重算出另一套利润。

交期可用 `LocalDate` 表达“某一天”；事件可用 `Instant` 等表达时间点。按日统计时需要明确业务时区，例如北京时间可使用 `ZoneId.of("Asia/Shanghai")`。设置一个 JSON 时区选项，不等于数据库、调度、浏览器和业务自然日都已经统一。[S20]

### 5.5 参数、业务规则和异常分开处理

请求 DTO 描述允许输入的字段。`@NotNull`、`@Positive`、`@NotBlank` 等约束在已配置的校验入口触发时检查字段；Controller 的请求参数可按对应版本使用 `@Valid`。数量为正只是第一步，“不能超过剩余可发货量”仍要由业务服务依据数据库重新校验。[S28]

不要直接把请求绑定到可持久化实体，让客户端顺带修改状态、成本、创建人或权限字段。操作者从认证上下文得到；可写字段通过明确 DTO 控制。

可以使用 `@RestControllerAdvice` 等机制统一适配 Controller 异常，将参数、引用不存在、业务冲突和内部错误转为已约定的结构。不要把所有错误都返回 HTTP 200，也不向客户端输出 SQL、堆栈或密码。Security 过滤链上的认证/拒绝响应需要在安全层相应处理，不能假定 Controller 的异常处理器会接住所有错误。[S29、S30]

## 6. 前后端怎样真正接起来

前端不需要因为后端换成 Java 而全部重写。关键在双方约定相同的路径、参数、认证、响应和错误，而不是采用相同编程语言。

### 6.1 先写一张接口卡

以“读取客户列表”为例，接口说明通常需要回答：

| 项目 | 要写清的内容 |
| --- | --- |
| 方法与路径 | 实际方法和 `/api/v1/...` |
| 谁能调用 | 登录要求、角色、资源范围 |
| 输入 | 参数类型、必填、长度、分页限制 |
| 输出 | 允许字段、空列表、金额与时间格式 |
| 失败 | 401、403、404、409 等实际约定 |
| 写库相关 | 是否写库；事务和幂等是否适用 |
| 检查方式 | 正常结果、空列表和错误响应分别是什么 |

API 路径和响应是你自己设计并维护的合同，不要照着示意写完文档，却让 Controller 返回另一种结构。

### 6.2 开发代理与 CORS 选清楚

若前端开发服务器和后端端口不同，可以采用开发代理，让浏览器请求前端同源的 `/api`，再转发给后端；或者明确配置允许的 Origin。开发代理不是生产部署配置。

`localhost` 与 `127.0.0.1`、不同端口都可能形成不同 Origin。采用 Cookie 会话时，前端凭据模式、代理、Cookie 属性与 CORS 配置需要配合；携带凭据的跨域请求不能简单用通配 Origin 处理。[S12、S13]

CORS 解决浏览器跨源限制，不是身份验证。不要用“关闭 Security”“允许所有写请求”“给所有路径加星号”来排除联调故障。

### 6.3 用请求证据定位

浏览器 Network 中检查请求 URL、方法、状态码、响应、Cookie/请求头和预检。后端检查同一 requestId 对应的日志；必要时在隔离库只读核对记录。

页面出现“网络错误”不一定是 CORS，也可能是服务器没启动、URL 错误、认证失败、异常被吞、代理转发失败。后端不可用时显示真实错误和可恢复草稿，不能回退到固定样例列表。

## 7. 登录、权限和字段：三种检查要分开

### 7.1 认证

使用真实账号或已说明的受控认证方案。密码使用框架提供的密码哈希机制，不存明文，也不把普通 SHA-256 作为密码存储方案。Spring Security 提供密码编码基础设施，但你仍要设计账号与认证接入。[S03、S11]

Cookie 会话和 JWT 是常见的认证相关设计选择，不是必须同时使用的组件。采用会话时，需要理解会话存储、过期、退出、Cookie 属性和 CSRF；采用 JWT 时，需要理解签名验证、有效期、存储方式及退出或撤销策略。JWT 不是“加上就安全”。

### 7.2 角色与资源

角色检查回答“普通成员是否允许删除记录”；资源检查回答“这条记录是否属于当前用户或其可管理范围”。两者都需要后端判断，不相信 body 中的 `operatorId`、`departmentId` 或自造的角色头。[S10]

`hasRole("ADMIN")` 的约定通常涉及 `ROLE_` 前缀，`hasAuthority(...)` 则按实际权限值匹配；配置与授予值必须一致。如果采用方法级注解，确认该版本已启用方法安全，而不只是贴了注解。[S10]

### 7.3 字段与文件

允许读取一条订单，不代表允许读取其中全部成本、联系方式等敏感字段。后端可以用针对权限的响应 DTO 或允许字段投影；不要将实体原样序列化后，再指望前端隐藏敏感数据。

字段保护应同时考虑详情、列表、嵌套字段和附件/导出。附件也不能通过猜 URL 绕开对象权限。

### 7.4 CSRF 不要机械关闭

使用浏览器会自动携带的认证凭据，尤其 Cookie 会话时，需要评估并实现 CSRF 防护。不能因为采用 JSON 或称接口为 REST，就推断没有 CSRF 风险。测试中的 POST 返回 403，先判断是身份、权限还是 CSRF，不要统一改成匿名可写。[S12]

仅当确实采用不同威胁模型，例如不依赖浏览器自动携带凭据的无状态令牌接口，才根据对应版本官方文档设计安全链；不能把教程中的 `disable` 片段当作通用答案。

## 8. 事务、幂等和并发

这三件事不是同一个概念：事务解决约定范围内的一起成功或失败；幂等解决同一次意图重试不多做；并发控制解决不同请求同时竞争数据。加一个 `@Transactional` 不会自动完成后两件事。

### 8.1 事务应该覆盖什么

以库存调整为例：如果库存数量和流水必须同时更新，可以将这些数据库写入放在同一事务里。失败后不能只减了库存，却没有对应流水。若还涉及消息或外部服务，就要额外考虑它们与本地数据库的一致性，不能假定一个注解可以覆盖所有系统。

Spring 常见的声明式事务通过代理工作；同一个对象内直接调用另一个事务方法，不能据此保证该注解通过代理生效。默认情况下，运行时异常与 Error 引发回滚，而受检异常是否回滚要按配置明确处理；不要捕获异常后返回“成功”。[S08、S09]

常见安全做法是由 Controller 调用独立 Service 的公开事务方法，并让失败正确传播。在你的测试里主动制造第二步失败，验证第一步有没有真正回滚，而不是凭注解判断。

### 8.2 减库存不能“先查后覆盖”

错误思路：两个请求都查到 10，各减 7，再分别写回 3；两人都成功，账已经错了。

下面是 PostgreSQL 的原子扣减思路，`:参数` 是说明占位，不是可直接粘贴到数据库终端执行的 SQL：

```sql
UPDATE part_inventory
SET quantity = quantity - :amount,
    version = version + 1
WHERE id = :id
  AND quantity >= :amount;
```

后端必须先验证 `amount` 是正整数，并检查实际受影响行数。零行需要按合同区分不存在与库存不足，不能直接返回成功。约束、实际 before/after 与流水要在同一事务中处理；如果使用 JPA 批量更新，还要防止持久化上下文里的旧实体值误导后续逻辑。[S21]

这只是并发策略片段，不含完整鉴权、幂等、流水或错误处理。也可以使用悲观锁或乐观版本检查，但要能说明事务边界、冲突反馈与有限重试。单纯加 `synchronized` 不能替代数据库层跨进程保护。

### 8.3 首次创建也会竞争

两个请求都查不到成品库存，然后各自创建，是另一类竞争。采用稳定、明确的库存身份键及数据库唯一约束；不要认为“先查一下”能够保证唯一。[S22]

约束冲突后的事务状态也要处理。例如 PostgreSQL 事务中发生语句错误后，不能在同一已失败事务里直接继续查询。可以使用适当的冲突处理 SQL，或让外层协调器在原事务回滚后开启新的有限重试；重试保留原业务意图，不能绕开幂等。[S21、S23]

### 8.4 幂等要记录到数据库

例如创建订单时，浏览器因超时重试，用户通常并不想创建两张订单。一种常见设计是让同一次意图携带稳定的幂等键，将它与操作者、操作类型、请求摘要和结果关联，并用数据库约束协调竞争。是否需要幂等以及键的有效范围，要根据接口语义决定。

- 同键同输入：返回同一业务结果，不新增第二条记录。
- 同键不同输入：返回约定的 409 冲突，不能重放别的输入结果。
- 并发同键：只能一次业务影响，由唯一约束与事务协调。
- 响应超时：结果未知，复用原 key 重试或查询，不生成新意图。
- 服务重启：成功结果仍能查到，不能只依赖 Java 内存 Map。
- 认证失败：先拒绝，不因猜到 key 泄露旧结果。

这些是这种设计需要考虑的情况，不是 Spring Boot 的内置默认行为。数据库错误能否重试、重试次数、结果保留多久，也属于业务与可靠性设计。

### 8.5 数据库事务不等于文件事务

`@Transactional` 不会自动回滚磁盘文件，也不会撤回已经发出的邮件。比如文件已经写入磁盘，但数据库记录保存失败，就可能留下孤立文件。理解这条边界后，才能进一步设计临时文件、失败状态、清理或补偿机制。具体采用哪种方式，取决于数据重要性和失败后的恢复需求。

## 9. 常见扩展能力的知识索引

下面这些能力经常出现在 Web 应用中。它们是遇到相关需求时的查阅入口，不是需要全部实现的功能清单。

| 能力 | 涉及哪些知识 | 常见错误 |
| --- | --- | --- |
| 附件 | Multipart、类型/大小校验、服务端存储键、下载鉴权 | 用原始文件名拼路径，公开整个上传目录 |
| xlsx 导出 | POI、查询范围、字段裁剪、数据快照 | 导出固定样例数据，或泄露无权查看的字段 |
| 通知 | 事件、接收者、持久化、去重、未读状态 | 把一次页面提示误当作可查询的通知记录 |
| 统计看板 | 聚合查询、日期范围、时区、计算口径 | 写死统计数，列表与统计使用不同公式 |
| 错误排查 | 结构化日志、requestId、异常分类 | 只记录“失败”，找不到对应请求 |
| 业务审计 | 操作者、动作、资源、时间、访问保护 | 把普通调试日志当完整业务审计 |
| 请求重试 | 超时、未知结果、幂等、有限重试 | 超时就生成新 key，重复执行写操作 |
| 数据恢复 | 数据库备份、附件关联、恢复校验 | 只有备份文件，从未验证能否恢复 |

POI 是生成文件的工具，不会替你完成导出权限或字段裁剪。导出用户输入到表格时，不要把它自动变成公式；用户文本按文本写入，不调用公式写入接口。[S04、S24]

框架和库通常只提供其中一部分机制。例如 POI 能帮助生成表格，却不会自动判断用户能看哪些数据。

## 10. 测试怎样写才算有用

### 10.1 三层证据分别解决不同问题

纯逻辑单测检查数量公式、金额和动作守卫；API 加真实隔离数据库测试检查认证、约束、事务、幂等和并发；页面联调检查实际点击、错误反馈和多角色切换。只 Mock Repository 的测试不能证明真实数据库保护成立。

Spring 的 Web 测试切片和完整上下文测试用途不同；测试注解、模块依赖和导入路径按所用版本文档配置。需要时可以用 Testcontainers 启动隔离数据库，但这要求可用的容器环境；没有容器也可以明确配置独立测试库，不因此增加 Docker 必做项。[S14、S15]

H2 通过不等于 PostgreSQL 或 MySQL 的锁、类型与 SQL 都通过。并发和约束应至少在实际选定的数据库引擎上验证。

### 10.2 优先检查这些真实场景

| 场景 | 观察什么 | 涉及知识 |
| --- | --- | --- |
| 无凭据、伪造角色、访问他人记录 | 被拒绝，不写库，不泄露敏感数据 | 认证与授权 |
| 重放相同 key 与输入 | 同一记录、同一结果，不翻倍 | 幂等 |
| 同 key 改数量 | 明确冲突，原数据不变 | 请求身份 |
| 库存 10，两条独立请求同时减 7 | 仅一次成功，最终 3，流水正确 | 并发控制 |
| 相同身份的库存同时首次创建 | 唯一性成立，数量与有效操作相符 | 唯一约束 |
| 流水写入前制造失败 | 库存与流水一致回滚 | 事务 |
| 当前状态不允许时请求执行动作 | 服务端拒绝，原数据不变 | 业务前置条件 |
| 响应丢失后重试，再重启服务重试 | 不假报成功，不重复影响 | 超时与持久化 |
| 无权限用户下载附件或导出记录 | 不泄露文件和受保护字段 | 文件授权 |
| 新数据库迁移并启动 | 不依赖本机残留表和数据 | 迁移与配置 |
| 恢复数据库及附件到隔离环境 | 引用可解析，关键记录可核对 | 备份与恢复 |

这些场景用于说明不同测试在检查什么，可以按需要选读。并发测试要使用独立连接/事务和可控起跑点，不能只是顺序调用两次 Service。若测试整体包在一个自动回滚事务里，还要确认线程和真实提交边界是否符合要测的情况。

### 10.3 Maven 命令不等于测试一定被发现

先看自己的 POM。以下命令形式仅在工程确实配置相应入口、数据库和依赖后使用：

```powershell
.\mvnw.cmd test
.\mvnw.cmd verify
.\mvnw.cmd package
```

`test` 通常由 Surefire 执行相应测试；命名为 `*IT` 的集成测试如果未配置 Failsafe 或等价入口，不会因为你运行 `verify` 就必然被执行。检查发现数量、失败、跳过数量及报告，不以 BUILD SUCCESS 单独判定业务通过。[S25、S26]

不要用 `-DskipTests` 或 `-Dmaven.test.skip=true` 的结果宣称已完成测试。测试证据记录命令、版本、环境、场景、预期、实际、失败和复测；本资料里的预期不是你的实际执行结果。

## 11. 启动和联调出错，先查哪一层

| 现象 | 优先检查 | 不要这样处理 |
| --- | --- | --- |
| 找不到 `java` / `javac` | JDK 安装、PATH、终端是否重开 | 在每份教程间来回换框架 |
| Wrapper 使用错误 Java 版本 | `.\mvnw.cmd -v`、JAVA_HOME、IDE JDK | 只改项目编辑器的语法版本 |
| `UnsupportedClassVersionError` | 编译目标与实际运行 JDK | 把异常隐藏后继续演示 |
| 依赖下载失败 | 网络、仓库配置、代理、具体构件版本 | 连续手填不同 Spring 大版本 |
| `Port 8080 ... already in use` | 端口监听者是否就是已启动工程 | 未确认就结束所有 Java 进程 |
| 数据库连接失败 | JDBC URL、库与用户、服务、网络、权限 | 将生产库地址临时填入测试 |
| schema 不存在或迁移校验失败 | 是否执行迁移、版本历史、实际库 | 删除正常库或改历史 SQL 硬过 |
| 401 / 跳转登录 | 凭证、会话、认证入口及 API 响应约定 | 假装登录成功 |
| 403 | 角色、资源、CSRF、安全配置 | 全局 `permitAll` |
| OPTIONS 失败或浏览器 CORS 提示 | Origin、代理、预检、安全链顺序 | 关闭所有认证 |
| JPA 懒加载或序列化异常 | 查询边界、DTO、实际需要的字段 | 直接返回实体并一律强制立即加载 |
| 重启后数据消失 | 是否内存库、连接了哪个库、是否建表删除数据 | 改成前端样例数据兜底 |
| 提示成功却重复或少记流水 | 事务、幂等、并发与异常传播 | 仅禁用按钮就结束排查 |

Windows 只读查看端口示例：

```powershell
Get-NetTCPConnection -LocalPort 8080 -State Listen
```

这条命令不启动也不关闭服务；无监听时可能提示无匹配对象。取得监听进程 ID 后确认归属，再决定复用已有进程或换端口。不要凭“端口被占用”就断定电脑缺少某个依赖。

## 12. 常用概念与注解速查

读代码时不需要先记住所有名词。下面可以作为前文的简短索引，具体行为仍以采用的版本和配置为准。

| 名词 | 可以怎样理解 | 容易混淆的地方 |
| --- | --- | --- |
| JDK / JVM | JDK 提供编译与开发工具；JVM 执行 Java 字节码 | 装了能运行的环境，不代表终端一定能找到编译器 |
| Maven / POM | Maven 管理构建流程；`pom.xml` 描述依赖和构建配置 | Maven 不是 Java 语言，也不是数据库 |
| Bean / 依赖注入 | Bean 是由 Spring 容器管理的对象；依赖注入由容器提供所需对象 | 不是所有手动 `new` 出来的对象都受容器管理 |
| Controller | 将 HTTP 请求适配为方法调用，再返回响应 | 不应承担全部业务规则和数据库操作 |
| DTO / Entity | DTO 表达接口数据；Entity 在 JPA 中表达持久化映射 | 表里的字段不等于接口可以全部返回的字段 |
| Service / Repository | Service 组织业务；Repository 组织数据访问 | 分了目录不等于事务和权限就自动正确 |
| `@RestController` | 声明处理请求并返回响应数据的组件 | 返回 JSON 不等于数据已经保存 |
| `@GetMapping` | 将 GET 请求映射到方法 | 路径存在不代表匿名用户就能访问 |
| `@Valid` 与校验约束 | 在合适的校验入口检查请求数据 | 字段合法不代表当前用户有权限执行 |
| `@Transactional` | 在框架支持的调用边界声明事务行为 | 不自动处理幂等、全部并发问题或磁盘回滚 |
| 认证 / 授权 | 认证判断“你是谁”；授权判断“你能做什么” | 登录成功不代表可以读取所有记录 |
| CORS / CSRF | 前者涉及浏览器跨源访问；后者涉及借用自动携带凭据发起请求的风险 | 两者都不是“关掉就能安全联调”的开关 |
| 迁移 / 测试数据 | 迁移维护数据库结构历史；测试数据用于验证行为 | 有几条样例记录不代表数据库结构可重建 |

## 13. 按问题查阅

| 当前疑问 | 可以先看哪里 | 阅读重点 |
| --- | --- | --- |
| Java、Spring 和 Spring Boot 是什么关系？ | 第 1、12 节 | 语言、框架和组件分别负责什么 |
| 不知道装什么，启动时 Java 版本不对 | 第 3、11 节 | JDK、JAVA_HOME、Wrapper 实际使用的版本 |
| 看不懂文件为什么放在这些目录 | 第 4、12 节 | 入口、资源、测试与各层职责 |
| 接口返回成功，但数据库没有记录 | 第 5、8、11 节 | 实际连接的数据库、事务与异常 |
| 浏览器调用失败，终端请求却成功 | 第 6、11 节 | URL、Origin、Cookie、预检和代理 |
| 已登录为什么还是 403？ | 第 7、11 节 | 角色、资源范围及 CSRF 分别排查 |
| 重试为什么生成两条记录？ | 第 8 节 | 同一次意图、幂等键与持久化结果 |
| 两个人同时操作，数量为什么不对？ | 第 8、10 节 | 原子更新、约束、锁与真实并发测试 |
| 想查上传、表格导出或通知 | 第 9、14 节 | 组件能力与权限、存储边界 |
| 构建成功，为什么仍不能说明功能正确？ | 第 10 节 | 测试发现数量、真实依赖和检查范围 |

看教程或向 AI 提问时，尽量带上具体版本、最小相关代码、原始错误信息，以及“预期是什么、实际发生了什么”。不要提供密码、令牌或真实个人数据。生成的代码若出现不存在的类、旧注解或失效配置，应回到对应版本的官方文档核对。

## 14. 官方参考索引

以下页面于 2026-11-05 在线核对。无版本路径可能随官方默认版本变化；使用时切到与你工程一致的版本。本资料没有下载并启动教程工程，也没有运行 Java/数据库示例；代码、SQL、配置和预期均为学习说明，不是已通过的验收证据。

- S01 REST 入门：https://spring.io/guides/gs/rest-service
- S02 JPA 入门：https://spring.io/guides/gs/accessing-data-jpa
- S03 登录与资源保护入门：https://spring.io/guides/gs/securing-web
- S04 文件上传入门：https://spring.io/guides/gs/uploading-files
- S05 Boot 系统要求：https://docs.spring.io/spring-boot/system-requirements.html
- S06 Boot 入门：https://docs.spring.io/spring-boot/tutorial/first-application/index.html
- S07 数据初始化与迁移：https://docs.spring.io/spring-boot/how-to/data-initialization.html
- S08 声明式事务与代理：https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/annotations.html
- S09 回滚规则：https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/rolling-back.html
- S10 方法安全：https://docs.spring.io/spring-security/reference/servlet/authorization/method-security.html
- S11 密码存储：https://docs.spring.io/spring-security/reference/features/authentication/password-storage.html
- S12 CSRF：https://docs.spring.io/spring-security/reference/servlet/exploits/csrf.html
- S13 CORS：https://docs.spring.io/spring-security/reference/servlet/integrations/cors.html
- S14 Boot 应用测试：https://docs.spring.io/spring-boot/reference/testing/spring-boot-applications.html
- S15 Testcontainers 集成：https://docs.spring.io/spring-boot/reference/testing/testcontainers.html
- S16 Spring Initializr：https://start.spring.io/
- S17 Maven Wrapper：https://maven.apache.org/wrapper/
- S18 外部配置：https://docs.spring.io/spring-boot/reference/features/external-config.html
- S19 BigDecimal：https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/math/BigDecimal.html
- S20 Java 日期与时间：https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/package-summary.html
- S21 PostgreSQL 事务隔离：https://www.postgresql.org/docs/current/transaction-iso.html
- S22 PostgreSQL 约束：https://www.postgresql.org/docs/current/ddl-constraints.html
- S23 PostgreSQL 事务教程：https://www.postgresql.org/docs/current/tutorial-transactions.html
- S24 Apache POI 表格指南：https://poi.apache.org/components/spreadsheet/quick-guide.html
- S25 Maven Surefire：https://maven.apache.org/surefire/maven-surefire-plugin/
- S26 Maven Failsafe：https://maven.apache.org/surefire/maven-failsafe-plugin/
- S27 Boot SQL 数据库与 JPA：https://docs.spring.io/spring-boot/reference/data/sql.html
- S28 MVC 参数校验：https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-validation.html
- S29 MVC 异常处理：https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-exceptionhandler.html
- S30 Servlet 安全架构：https://docs.spring.io/spring-security/reference/servlet/architecture.html
