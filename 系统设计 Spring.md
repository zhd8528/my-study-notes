# 系统设计 Spring

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

> 把对象创建交给 Spirng 容器，降低耦合

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

---

## 4. Spirng 事务失效场景

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

---

## 6. SpringBoot 自动配置原理

- @SpringBootApplication 组合了 @EnableAutoConfiguration
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

> 默认使用 CGLIB（因为 SpringBoot 中常用 @Transactional 在类上，且不强制要求接口）

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

### 6. SpringBoot 自动配置原理补充

> SpringBoot 2.7 + 已改用 META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports

**补充**：

> 条件注解：
>
> @ConditionalOnClass、@ConditionalOnMissingBean 等，控制配置类是否生效
>
> 自动配置如何覆盖：
>
> 用户自定义的 Bean 会覆盖自动配置中的默认 Bean

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