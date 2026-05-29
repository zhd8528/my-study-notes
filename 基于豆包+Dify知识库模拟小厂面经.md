# 基于豆包+Dify知识库模拟小厂面经

## 后端核心考点

### 1.Java基础 & 面向对象

- 说说Java的基本数据类型和引用数据类型区别？
  - Java 的基本数据类型（如 int、double）直接存储值，变量中保存的就是数据本身，内存空间固定。引用数据类型（如 String、Array、Object）存储的是对象在堆内存中的地址，变量中保存的是指向对象的引用。
- 面向对象的三大特性分别怎么理解？用项目举例
  - 面向对象的三大特性是封装、继承和多态。   封装是将数据和操作数据的方法绑定在一起，通过 private 限制外部直接访问，比如在项目中用 User 类封装用户名和密码，并提供 get/set 方法来控制访问。   继承是子类可以复用父类的属性和方法，比如在电商项目中，Order（订单）类继承自 BaseEntity（基础实体），自动拥有创建时间、更新时间等公共字段。   多态是同一个方法在不同对象中有不同的表现形式，比如在支付模块中，PayService 定义一个 pay() 方法，具体实现由 Alipay、WechatPay 等子类完成，程序运行时根据实际对象调用对应逻辑。
- ArrayList和LinkedList的区别？HashMap的底层实现（JDK7/JDK8）？
  - ArrayList 底层是动态数组，支持随机访问，查询快，但插入删除较慢；LinkedList 底层是双向链表，插入删除快，但不支持快速随机访问。   JDK 7 中 HashMap 底层是数组加链表，当哈希冲突时用链表存储；JDK 8 中引入了红黑树，当链表长度超过阈值（默认8）时，链表会转为红黑树，提升查找效率。
- 多线程的创建方式？synchronized和Lock的区别？volatile的作用？
  - 多线程的创建方式有：继承 Thread 类、实现 Runnable 接口、实现 Callable 接口并结合 Future，以及使用线程池（如 ExecutorService）。   synchronized 是关键字，自动加锁和释放，不可中断；Lock 是接口，需要手动加锁和解锁，支持可中断、超时、公平锁等更灵活的控制。   volatile 保证变量的可见性，当一个线程修改了 volatile 变量，其他线程能立即看到最新值，但不保证原子性。
- IO流的分类？字节流和字符流的区别？
  - IO流按方向分为输入流和输出流，按数据单位分为字节流和字符流。   字节流以字节为单位处理数据，适合读写二进制文件；字符流以字符为单位，自动处理编码，适合读写文本文件，底层会进行字符与字节的转换。

> 我在项目中用Java做后端开放时，主要用的是面向对象的思想来拆分模块，比如用封装来隐藏数据细节，用继承减少重复代码，用多态来做接口的不同实现。
>
> 集合方面，我常用ArrayList做有序存储，HashMap做键值对缓存，也了解它在JDK8里引入红黑树优化的逻辑。
>
> 多线程部分，我在做批量数据导入时用过线程池，也了解过synchronized的锁升级过程，确保数据一致性，

---

### 2.Spring/SpringBoot框架

- Spring的IOC和AOP是什么？项目里怎么用的？
  - Spring 的 IOC（控制反转）是将对象的创建和管理交给 Spring 容器，通过配置或注解自动装配依赖，减少代码耦合。在项目中，常用 @Component、@Service、@Autowired 等注解来实现类的注册和依赖注入。 Spring 的 AOP（面向切面编程）是在不修改原有代码的情况下，动态添加横切逻辑，如日志记录、权限校验、事务管理。在项目中，用 @Aspect 和 @Around 等注解定义切面，比如在服务方法前加日志打印或事务控制。
- SpringBoot的自动配置原理？
  - SpringBoot 的自动配置原理是基于条件化配置，通过 @EnableAutoConfiguration 启动自动加载机制。它会扫描 spring.factories 文件中定义的自动配置类，根据类路径下是否存在某些依赖或配置，自动装配相应的 Bean。 在项目中，只要引入了对应的 starter 依赖（如 spring-boot-starter-web），SpringBoot 就会自动配置 Web 相关的组件（如 Tomcat、DispatcherServlet），无需手动写大量配置。
- 说说SpringMVC的执行流程？
  - SpringMVC 的执行流程是：客户端请求先到达前端控制器 DispatcherServlet，它根据请求路径查找处理器映射器（HandlerMapping）找到对应的处理器方法。   然后由处理器适配器（HandlerAdapter）调用该方法，执行业务逻辑并返回 ModelAndView，最后由视图解析器（ViewResolver）解析视图名称，渲染页面并返回响应给客户端。
- 项目里怎么处理事务的？@Transactional的失效场景？
  - 项目中通常在服务层方法上使用 @Transactional 注解来控制事务，确保多个数据库操作要么全部成功，要么全部回滚。   @Transactional 失效的常见场景包括：方法不是 public 修饰、被自身调用（代理失效）、异常被捕获未抛出、使用了不支持事务的传播行为，或数据库引擎不支持事务（如 MyISAM）。

> 我项目里主要用SpringBoot来快速搭建后端服务，Spring的IOC容器帮我们管理对象依赖，AOP用来做日志记录和权限经验。
>
> 自动配置的核心是@EnableAutoConfiguration注解，它会根据类路径下的依赖自动配置对应的Bean。
>
> 事务方面，我用@Transactional注解来保证操作的原子性，也知道如果方法不是public、或者内部调用的话会失效，所以项目里会尽量避免这些场景。

---

### 3.MySQL数据库

- 数据库的事务隔离级别？脏读、不可重复读、幻读怎么理解？
  - 数据库的事务隔离级别有四种：读未提交、读已提交、可重复读、串行化。   脏读是指一个事务读到了另一个未提交事务的数据；不可重复读是同一个事务中两次读取同一数据，结果不一致，因为中间有其他事务修改并提交了；幻读是同一个事务中两次查询相同条件的数据量不同，因为有其他事务插入了新数据。
- 索引的类型？B+树索引的原理？
  - 索引的类型包括：主键索引、唯一索引、普通索引和组合索引，它们用于加速数据查询。   B+树索引的原理是：所有数据存储在叶子节点，非叶子节点只存键值和指针，叶子节点之间用指针连接，形成有序链表，支持范围查询和高效查找，且树的高度低，能减少磁盘 I/O 次数。
- 怎么优化慢SQL？
  - 优化慢 SQL 的方法包括：使用 EXPLAIN 分析执行计划，避免全表扫描；在查询条件字段上添加合适的索引，尤其是组合索引要符合最左匹配原则；避免在索引字段上进行函数操作或类型转换；减少不必要的字段查询，只查需要的列；拆分大查询，避免一次处理过多数据。
- 说说InnoDB和MyISAM的区别？
  - InnoDB 支持事务、行级锁和外键，适合高并发、需要数据一致性的场景；MyISAM 不支持事务和行级锁，只支持表级锁，查询速度快但不支持崩溃后自动恢复。

> 我项目里用MySQL存业务数据，常用的索引是B+树索引，它的结构让查询效率很高。
>
> 事务方面，默认用的是可重复读隔离级别，能解决大部分脏读和不可重复读的问题。
>
> 优化慢SQL的话，我一般会先通过explain分析执行计划，看有没有走索引，然后根据查询条件建联合索引，避免select *和函数操作索引列。

---

### 4.RESTful接口设计

- 什么是RESTful？接口设计要遵循什么规范？
  - RESTful 是一种基于 HTTP 协议的接口设计风格，强调资源的唯一标识和统一操作方式。   接口设计要遵循：使用标准 HTTP 方法（GET、POST、PUT、DELETE）表示对资源的操作，用 URL 表示资源，返回 JSON 格式数据，状态码准确反映请求结果。
- 前后端接口怎么约定返回格式？怎么处理异常？
  - 前后端接口约定返回格式通常统一为 JSON，包含 code（状态码）、message（提示信息）和 data（业务数据）三个字段，方便前端解析。   异常处理通过全局异常处理器（如 @ControllerAdvice）捕获异常，统一返回标准错误格式，避免直接抛出堆栈信息，保证接口安全和可读性。

> RESTful接口是用于HTTP方法来定义操作，比如GET查、POST增、PUT改、DELETE删，路径用名词复数，不用动词。
>
> 我项目里统一用{code: 200, data: {}, message: ''}的格式返回，异常会用全局异常处理器统一捕获，返回对应的错误码和信息，方便前端处理。

---

## 前端Vue考点

### 1.Vue核心语法

- Vue的双向绑定原理？
  - Vue 的双向绑定原理是通过数据劫持结合发布订阅模式实现的，主要利用 Object.defineProperty() 或 Proxy 来监听数据变化。   当数据改变时，会触发视图更新；同时用户操作视图（如输入框）也会自动更新数据，从而实现数据和视图的双向同步。
- v-if和v-show的区别？
  - v-if 是条件渲染，根据表达式真假决定是否创建或销毁元素，适合切换频率低的场景。   v-show 是通过 CSS display 属性控制元素显示隐藏，始终保留元素，适合频繁切换的情况。
- 组件通信的方式有哪些？
  - 组件通信的方式包括：父传子用 props，子传父用 $emit；通过 $refs 直接访问子组件实例；使用事件总线（Event Bus）进行任意组件间通信；利用 Vuex 或 Pinia 等状态管理工具共享数据；通过 provide/inject 实现跨层级组件通信。
- Vue的生命周期？created和mounted的区别？
  - created 阶段实例已经创建完成，可以访问 data、methods 等数据，但此时 DOM 还未生成，不能操作真实 DOM。   mounted 阶段 DOM 已经渲染完成，可以操作真实 DOM，通常用于发起请求或初始化第三方插件。

> Vue的双向绑定是用Object.defineProperty（Vue2）或Proxy（Vue3）实现的，数据劫持+发布订阅模式，监听数据变化更新视图。
>
> 组件通信的话，父子组件用props和$emit，兄弟组件用EventBus或者Vuex/Pinia，跨级用provide/inject。
>
> 生命周期里，created的时候可以拿到数据，mounted的时候DOM已经渲染完成，可以操作DOM。

---

### 2.Vue的生态工具

- Vue Router的路由模式？hash和history的区别？
  - Vue Router 的路由模式有两种：hash 模式和 history 模式。   hash 模式通过 URL 的 # 后面部分实现路由，不向服务器发送请求，兼容性好，但 URL 看起来不美观。   history 模式利用 HTML5 的 History API，URL 看起来更干净，但需要服务器配置支持，否则刷新页面会返回 404。
- Vuex/Pinia的核心概念？怎么用？
  - Vuex 和 Pinia 的核心概念包括：state（状态）、getters（计算属性）、mutations（同步修改状态）、actions（异步操作）和 modules（模块化）。   在项目中，通过 store 定义 state 存储数据，用 getters 读取计算值，用 actions 调用 mutations 来修改 state，实现数据的集中管理与共享。
- 怎么实现路由懒加载？
  - 路由懒加载通过动态导入（import()）实现，将组件定义为异步函数，在路由配置中使用箭头函数包裹 import。   这样只有访问对应路由时才会加载对应的组件，减少初始包体积，提升页面加载速度。

> Vue Router的hash模式是带#号的，兼容性好；
>
> history模式不带#，但需要后端配置重定向。
>
> 状态管理我用过Vuex，它的核心是state、mutations、actions、getters，用来管理全局状态，比如用户登录信息。
>
> 路由懒加载的话，我用import()语法，把组件按路由分块打包，优化首屏加载速度。

---

### 3.HTML/CSS基础

- Flex布局的常用属性？怎么实现水平垂直居中？
  - Flex 布局常用属性包括：flex-direction（主轴方向）、justify-content（水平对齐方式）、align-items（垂直对齐方式）、flex-wrap（换行设置）和 gap（间距）。   实现水平垂直居中只需在父容器上设置 `display: flex`，并配合 `justify-content: center` 和 `align-items: center`。
- 说一下盒模型？标准盒模型和怪异盒模型的区别？
  - 盒模型是元素布局的基本结构，包括内容区、内边距（padding）、边框（border）和外边距（margin）。   标准盒模型中，width 和 height 只包含内容区；怪异盒模型（IE 盒模型）中，width 和 height 包含内容、内边距和边框，导致实际占用空间更大。

> Flex布局里，父元素设display:flex，justify-content:center实现垂直居中。
>
> 盒模型分为标准盒模型（width=content）和怪异盒模型（width=content+padding+border），用box-sizing:border-box可以设置为怪异盒模型，更方便做布局。

---

### 4.AJAX请求

- 说一下AJAX的原理？项目里怎么处理请求异常？
  - AJAX 是通过 JavaScript 的 XMLHttpRequest 或 fetch API 异步发送请求，不刷新页面就能与服务器通信，实现局部更新。   在项目中，通常用 axios 封装请求，通过拦截器统一处理网络异常，如超时、404、500 等错误，结合全局提示（如 Toast）向用户反馈问题，并记录日志便于排查。
- 说说axios的常用配置？拦截器怎么用？
  - axios 的常用配置包括：baseURL（基础路径）、timeout（超时时间）、headers（请求头）、params（查询参数）和 data（请求体）。   拦截器通过 axios.interceptors.request.use() 和 axios.interceptors.response.use() 实现，可在请求发送前添加 token、统一处理加载状态，或在响应后统一处理错误、解析数据。

> AJAX是用XMLHttpRequst对象和服务器异步通信，实现页面刷新更新数据。
>
> 项目里用axios发请求，配置baseURL和超时时间，用请求拦截器加token，响应拦截器统一处理错误码，比如401跳登录，500提示服务器异常。

---

## 加分项考点

**全栈项目经验**：

> 提前准备一个Java+Vue的项目，按项目背景 + 你负责的模块 + 技术实现 + 遇到的问题 + 解决方法的逻辑说：
>
> 比如我做过一个XX管理系统，后端用SpringBoot+MyBatisPlus写接口，前端用Vue+Element UI做页面，用axios调用接口，实现了增删改查和分页功能，还解决了跨域问题。

**Redis/MongoDB**：

> 我了解了Redis的基础用法，比如用String存验证码，Hash存用户信息，也知道它的单线程模型和缓存雪崩、穿透的解决思路。

**开发工具**：

- Git常用工具
  - commit、push、pull、branch、merge
- Maven
  - 依赖管理

**前端工程化**：

> 我了解Webpack的打包流程，知道它能把JS、CSS、图片等资源打包成静态文件，也用过Element UI组件库快速开发页面。

---

## 面试流程&话术

**自我介绍**：

> 面试官您好，我叫XXX，目前是大三计算机专业学生，主攻Java后端开发，同时也在学习Vue前端技术。
>
> 我熟悉SpringBoot、MyBatisPlus等后端框架，做过XX等项目，也了解MySQl、Redis的使用。
>
> 前端方面，我掌握Vue的核心语法和常用生态工具，能独立开发简单页面。
>
> 今天来面试贵公司的全栈开发岗位，希望能有机会加入团队，谢谢！

**反问环节**：

- 请问这个岗位在项目里具体负责什么模块？
- 团队的技术栈和开发流程是怎样的？
- 您对我这样的应届生，在技术上有什么建议吗？