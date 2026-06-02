# 系统设计 - Spring

## 核心内容

- IOC
- AOP
- 事务
- Bean 生命周期
- Boot 自动配置

## 重点

- IOC
- AOP
- 事务传播与失效场景
- Bean 生命周期
- @Autowired 和 @Resource 区别、@Component 和 @Bean 区别

## 1. IOC

**控制反转**：

> 将对象的创建、组装、管理权交给 Spring 容器，由容器控制对象的生命周期，而不是由代码主动 new，从而降低耦合

**DI 依赖注入**：

> @Autowired 自动装配

---

## 2. AOP

- 面向切面编程，底层动态代理（JDK 代理接口/CGLIB 代理类）
- 应用
  - 日志、事务、权限

---

## 3. Bean 生命周期

1. 实例化
2. 属性填充
3. Aware 接口
4. BeanPostProcessor 前置
5. InitializingBean
6. init-method
7. BeanPostProcessor 后置
8. 使用
9. 销毁

**简化记忆**：

1. **实例化（new 对象）**
2. **属性填充（依赖注入）**
3. **初始化（Aware -> BeanPostProcessor 前置 -> init -> BeanPostProcessor 后置）**
4. **销毁**

---

## 4. Spring 事务失效场景

1. 非 public 方法
2. 自调用（同类方法无代理）
3. 异常被 try-catch 吞掉
4. 传播行为配置错误

---

## 5. @Autowired VS @Resource

**@Autowired**：

> Spring 提供，按类型注入

**@Resource**：

> JDK 提供，优先按名称注入

| 对比项   | @Autowired                                     | @Resource                          |
| -------- | ---------------------------------------------- | ---------------------------------- |
| 提供方   | Spring                                         | JDK（javax.annotation）            |
| 注入方式 | 默认按类型（type），可配合 `@Qualifier` 按名称 | 默认按名称（name），找不到再按类型 |
| 适用范围 | 构造器、方法、参数、字段                       | 方法、字段                         |
| 是否必须 | `required=true` （默认，找不到抛异常）         | 找不到则按类型匹配，仍找不到抛异常 |
| 空值处理 | 可设置 `required=false`                        | 无直接等效配置                     |

---

## 6. Spring Boot 自动配置原理

- @Spring BootApplication 组合了 @EnableAutoConfiguration
- 从 META-INF/spring.factories 加载自动配置类
- 按 @Conditional 条件注解决定是否失效

---

## 追问

### 1. IOC 细节补充

**IOC 容器的底层实现是什么？**

> BeanFactory（顶级接口，延迟加载）和 ApplicationContext（继承 BeanFactory，预加载，扩展国际化、事件等）
>
> 最常用的是 ClassPathXmlApplicationContext 或 AnnotationConfigApplicationContext

**依赖注入的 3 种方式？**

1. 构造器注入（推荐，final 字段，循环依赖检测）
2. Setter 注入
3. 字段注入（@Autowired，最简洁但有循环依赖风险）

**Spring 如何解决循环依赖？**

> **三级缓存**：
>
> singletonObjects（一级）、earlySingletonObjects（二级）、singletonFactories（三级）
>
> 通过提前暴露半成品对象（ObjectFactory）解决
>
> 注意：仅支持单列模式，构造器注入无法解决

**三级缓存解决循环依赖**：

| 级别 | 缓存名称                | 存储内容                            | 作用                    |
| ---- | ----------------------- | ----------------------------------- | ----------------------- |
| 一级 | `singletonObjects`      | 完全初始化好的 Bean                 | 直接使用                |
| 二级 | `earlySingletonObjects` | 提前暴露的半成品 Bean（未填充属性） | 提前暴露引用            |
| 三级 | `singletonFactories`    | `ObjectFactory`（用于生成代理对象） | 解决 AOP 代理的循环依赖 |

**流程**：

1. A 创建时，将 A 的 `ObjectFactory` 放入三级缓存
2. A 发现依赖 B，去创建 B
3. B 发现依赖 A，从三级缓存拿到 `ObjectFactory` 创建 A 的代理（或原始对象），放入二级缓存
4. B 完成初始化，A 继续完成初始化

---

### 2. AOP 细节补充

**Spring AOP 默认用哪种代理？**

> 如果目标类实现接口，默认用 JDK 动态代理，否则用 CGLIB
>
> 可通过 proxy-target-class=true 强制使用 CGLIB

**JDK 动态代理和 CGLIB 区别？**

> JDK 只能代理接口，利用反射生成代理类；
>
> CGLIB 通过生成子类（继承），可代理普通类，但不能代理 final 类/方法

**Spring Boot 2.x 默认代理？**

> 默认使用 CGLIB（因为 Spring Boot 中常用 @Transactional 在类上，且不强制要求接口）

---

### 3. Bean 生命周期补充（关键回调接口的作用）

| 接口/方法               | 作用                             | 执行时机             |
| ----------------------- | -------------------------------- | -------------------- |
| BeanNameAware           | 获取 Bean 在容器中的名称         | 属性填充后           |
| BeanFactoryAware        | 获取 BeanFactory                 | 同上                 |
| ApplicationContextAware | 获取 ApplicationContext          | 同上                 |
| BeanPostProcessor       | 对 Bean 进行自定义处理（如代理） | 初始化前后           |
| InitializingBean        | 所有属性设置后执行自定义初始化   | afterPropertiesSet() |
| @PostConstruct          | 同上，注解方式                   | 同上                 |
| DisposableBean          | 销毁回调                         | 容器关闭时           |

**@PostConstruct 和 afterPropertiesSet() 执行顺序？**

> @PostConstruct 在先（通过 CommonAnnotationBeanPostProcessor），然后才是 InitializingBean.afterPropertiesSet()

---

### 4. 事务失效场景补充

**非 public 方法**：

> Spring 事务默认只对 public 方法生效（源码 AbstractFallbackTransactionAttributeSource 中会检查修饰符）

**自调用（同类方法无代理）**：

> 通过 this 调用不走代理，事务切面无法生效
>
> 解决：
>
> AopContext、currentProxy() 或注入自己

**异常被 try-catch 吞掉**：

> 事务只在抛运行时异常或指定异常时回滚
>
> 吞掉后相当于没有异常

**传播行为配置错误**：

> 比如用 PlatformTransactionMangerBean，事务注解无效

**数据库引擎不支持事务**（如 MyISAM）：

> 事务注解不会报错，但不会生效

**补充**：

> @Transactinal 的 rollbackFor 属性默认只对 RuntimeException 和 Error 回滚，检查型异常（IOException）不回滚，需指定 rollbackFor = Exception.class

---

### 5. @Component 和 @Bean 的区别

| 区别     | @Component         | @Bean                                    |
| -------- | ------------------ | ---------------------------------------- |
| 适用位置 | 类上（自己写的类） | 方法上（通常用于配置类中，导入第三方类） |
| 控制权   | 类自己声明         | 手动 new 对象返回                        |
| 参数     | 无                 | 可指定初始化 / 销毁方法                  |

**@Bean 和 @Component 同时使用时，Spring 如何保证单例？**

> 默认单例，但 @Bean 方法如果每次调用都 new 则不是单例；
>
> 正确做法是方法内返回容器中的 Bean（通过参数注入）

---

### 6. Spring Boot 自动配置原理补充

**❗️版本变化**：

- **Spring Boot 2.7 之前**：`META-INF/spring.factories`

- **Spring Boot 2.7 +**：`META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`

**补充**：

> 条件注解：
>
> @ConditionalOnClass、@ConditionalOnMissingBean 等，控制配置类是否生效
>
> 自动配置如何覆盖：
>
> 用户自定义的 Bean 会覆盖自动配置中的默认 Bean

### 7. 补充Spring Boot 和 Spring 的 关系

**Spring Boot VS Spring**：

| 特性     | Spring                     | Spring Boot                    |
| -------- | -------------------------- | ------------------------------ |
| 配置     | XML / Java Config 手动配置 | 自动配置（约定大于配置）       |
| 依赖管理 | 手动管理版本               | Starter 统一管理               |
| 内嵌容器 | 需要部署到 Tomcat 等       | 内嵌 Tomcat / Jetty / Undertow |
| 起步     | 配置复杂，需要大量样板     | 快速创建独立应用               |


---

## 可选补充

### 1. @Primary 和 @Qualifier

> 解决多 Bean 注入冲突

### 2. @Lazy

> 延迟初始化 Bean、解决循环依赖或加速启动

### 3. @Scope

> 单例、原型、请求、会话等作用域

### 4. Spring 事件机制

> ApplicationEventPublisher 和 @EventListener
>
> Filter VS Interceptor：
>
> Filter 是 Servlet 规范，Interceptor 是 Spring 的 AOP 实现

**Filter VS Interceptor**：

| 对比项              | Filter                       | Interceptor          |
| ------------------- | ---------------------------- | -------------------- |
| 规范                | Servlet 规范                 | Spring 框架          |
| 范围                | 所有请求（包括静态资源）     | 只对 Controller 请求 |
| 执行时机            | Servlet 前后                 | Handler 前后         |
| 使用场景            | 字符编码、鉴权（简单）、日志 | 权限校验、日志、事务 |
| 能否修改请求 / 响应 | ✅️（包装）                    | ❌️（只能读取）        |
