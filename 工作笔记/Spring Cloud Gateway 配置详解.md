# Spring Cloud Gateway 配置详解

重拾微服务开发，最近有一个新项目使用 Spring Cloud 进行开发,下面让我来记录一下网关的一些核心概念和操作。

## 网关的核心概念

### 路由 (Routes)

路由是网关的核心,由服务 ID、服务 URI、断言和过滤器组成。

### 服务 ID (Service ID)

需要转发的服务的唯一标识符。

### 服务 URI (Service URI)

对应服务的 URI 地址。

### 断言 (Predicates)

用于确定请求是否应该被路由到对应的服务。断言可以基于请求的各种条件(如请求头、请求参数等)进行匹配。

### 过滤器 (Filters)

过滤器用于对请求和响应进行修改,可用于验证、日志记录、权限控制等场景。

通过合理配置路由、断言和过滤器,我可以实现灵活的请求路由和处理逻辑。

### 配置中文文档

https://springdoc.cn/spring-cloud-gateway/

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTg3MTY1MjI5N119
-->