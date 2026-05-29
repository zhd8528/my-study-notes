# 基于Deepseek模拟小厂面经

## 项目简述

### 1.项目名称是什么？用了哪些主要技术栈？

> 我：个人博客网站，技术栈是SpringBoot，SpringSecurity，MyBatisPlus，Redis，MySQL
>
> 面试官：好的，你的项目个人博客网站，技术栈很扎实，这说明你已经做了认证授权、缓存、ORM等核心模块

### 2.解决了什么问题？你在这个项目里负责了哪些部分？

> 我：解决的问题是写文章和管理评论，偏后端，前端用了Vue
>
> 面试官：收到，你的定位很清晰：偏后端，前端用Vue配合。写文章+管理评论是博客核心功能，配合SpringSecurity做登录/权限控制，Redis做缓存，很符合公司对实习生的预期。

---

## 数据库设计

### 3.主要有哪些表？表之间怎么关联的？你建表时考虑了哪些索引？有没有遇到过事务问题？怎么处理的？

>我：主要是用户表，评论表，文章表，表之间用一个联合表表示关联关系，关联表放的是表的主键；
>
>建立过联合索引，即文章id、父评论id，创建时间这3个字段；
>
>事务方面遇到不符合一致性的问题，删除文章时没有删除评论，在方法上加@Transactional就可以了。
>
>面试官：你的回答很实在，能看出你确实动手做过。我来帮你优化一下表述。
>
>你的回答优点：
>
>✅️表结构清晰（用户、文章、评论）
>
>✅️用了联合索引（文章id+父评论id+创建时间），这个组合对查询文章下的评论树很有用
>
>✅️发现删除文章时评论残留问题，并用@Transactional解决，这是真实踩坑经历
>
>建议❗️：
>
>1. 关联表问题：其实评论表里放一个article_id外键就够了，不需要额外一张关联表。实际设计时评论表中有article_id字段，表述为联合表不太准确，本质是多对一的关联
>2. 索引命名：在评论创建了复合索引idx_article_parent_time，能加速按文章查评论的、并且按父评论分组、按时间排序的查询

#### 追问1：你的联合索引、三个字段的顺序为什么是文章id、父评论id、创建时间？

> 因为查询条件通常先限定article_id=？，然后可能筛选parent_comment_id is null（查顶级评论），最后按创建时间排序。
>
> 索引从左到右匹配，这样能最大化利用

#### 追问2：加了@Transactional就一定能删除关联评论吗？

> 需要在删除文章的方法里手动调用评论Mapper的删除方法，事务保证要么都成功要么都回滚。

---

## 后端接口实战

### 4.请你选一个最有把握的接口，接口请求路径和请求方式？参数怎么传？Controller代码长什么样？Service层做了什么？数据库操作用MyBatisPlus，大概的Mapper写法？

> 我：关于删除文章接口，请求方式是DELETE，请求路径是/article/{id}，参数就是从前端接收一个long类型的文章id，并且加上@PathVariable，Controller代码的方法上面加上@RestController，需要用@Autowired注入Service提供的方法，把文章id传进去，具体的业务逻辑实现是在Service实现类代码中，我们可以直接用MyBatisPlus提供的方法删除文章，同时也要删除该文章id下面的所有评论，要加上@Transactional，最后返回统一结果，即200，操作成功。
>
> 面试官：你回答得很清晰，逻辑完整。
>
> 你做得好的点：
>
> ✅️明确@DeleteMapping+@PathVariable
>
> ✅️注入Service，调用具体方法
>
> ✅️提及删除文章同时删除评论，并用@Transactional
>
> ✅️返回统一结果（200成功）
>
> ❗️可微调的地方：
>
> 1. 路径风格：更常用/articles/{id}（资源名称复数）
> 2. @RestController作用：类加上@RestController代表每个方法返回JSON
> 3. 删除评论的实现：最好说出具体如何删除——比如commentMapper.delete(new LambdaQuerryWrapper<Comment>().eq(Comment :: getArticleId, articleId))，或自定义方法commentMapper.deleteByArticleId(articleId)
> 4. 事务边界：@Transactional加在Service方法上更常见，在Service实现类的方法上加了@Transactional，保证删除文章和删除评论在同一事务中

### 总结

> 删除文章接口：DELETE /articles/{id}，Controller接收@PathVariable Long id，调用Service。
>
> Service方法上使用@Transactional，先调用articleMapper.deleteById(id)删除文章，再调用commentMapper.delete(new LambdaQueryWrapper<Comment>().eq(Comment :: getArticleId, id))删除关联评论，最后返回统一成功响应

---

## 前端联调

## 5.你在哪个页面调用了这个删除接口？用的什么方法？点击删除按钮后，前端代码大致怎么写？如果请求失败，你怎么处理？

> 我：管理后台调用了这个接口，用的是axios，前端不太熟悉，大概就是点击删除按钮后发起请求，将选中的文章id传给后端，得到成功响应后会刷新列表并提示删除成功；
>
> 请求失败，如果是4开头的状态码，说明是在前端遇到问题，5开头就是后端，可以在浏览器打开检查工具或者查看后端控制台的报错信息，在这个接口的相关代码打上断点调试，一步步确定问题的根源。
>
> 面试官：你的回答很实在，尤其是排查问题的思路，这一点非常加分，说明你具备独立解决问题的能力，比只会写代码更重要。
>
> 回答的亮点：
>
> ✅️承认前端不熟，但有具体联调经验（删文章、刷列表，弹提示）
>
> ✅️展示了全链路排查能力（4xx/5xx判断+浏览器工具+后端断点）
>
> 优化版回答：
>
> 我在管理后台的文章列表页，用axios调用了删除接口。
>
> 具体流程：点击删除按钮时，拿到当前行的文章id，通过axios.delete('/articles/' + id)发请求。
>
> 成功后（then里）关闭弹窗、刷新列表、提示“删除成功”；
>
> 失败时（catch里）根据HTTP状态码判断：4xx一般是前端参数或权限问题，5xx是后端异常，我会打开浏览器开发者工具看请求详情，再去后端IDE打断点跟踪。
>
> 虽然我前段代码写得不多，但能独立完成整个删除联调，并且遇到过403没权限的问题，最后在axios请求头里加了token解决的。

---

## 难点与解决

### 6.你说过项目里有事务一致性问题。除此之外，还有没有其他你花了超过半小时才解决的bug？

> 我：跨域问题，其实也跟SpringSecurity有关，前端页面访问失败，原因是Security配置接口访问权限不对用户开放，比如登录接口，没有登录就没有权限，还有其他的接口，博客应该支持游客访问的，只有部分功能比如评论、上传个人资料需要登录权限。
>
> 面试官：明白了，你遇到的这个难点其实两个问题交织在一起：跨域（CROS）+SpringSecurity权限配置不合理。
>
> 这在真实项目中非常常见，你能把两者联系起来很好。

### 总结

> 问题现象：前端（Vue项目，运行在http://localhost:8080）调用后端接口（SpringBoot运行在http://localhost:9090）时，浏览器控制台报跨域错误，同时登录接口返回403，但明明用户名密码正确
>
> 排查过程：
>
> 1. 先在后端Controller上加@CrossOrigin，发现还是报错
> 2. 打开浏览器开发者工具 -> Network，发现OPTIONS预检请求返回403，而不是预期的200
> 3. 意识到是SpringSecurity拦截了OPTIONS请求（默认所有请求都需要认证）
> 4. 另外检查Security配置发现：登录接口 /login居然也要求“已登录”，这不合理；而且评论接口/comments要求必须登录，但博客文章页面应该是游客可读评论。
>
> 最终解决方案：
>
> - 在SpringSecurity配置类中：
>   - 调用http.cors()启用CORS支持（配合全局CorsConfiguration）
>   - 使用requestMatchers().permitAll()放行：/login /register /articles/***（文章列表详情） /comments的GET请求等
>   - 只对需要登录的接口如POST /comments /user/profile做.authenticated()
>   - 关键一步：requestMatchers(PathRequest.toStaticResources().atCommonLocations()).permitAll()并显式放行OPTIONS请求：.requestMatchers(CorsUtils :: isPreFlightRequest).permitAll()

### 结果

> 跨域预检通过，游客可以正常查看文章列表，登录后才能发表评论。前端也不需要额外修改。
>
> 我遇到过跨域和SpringSecurity权限混在一起的问题。
>
> 登录接口没放行，OPTIONS预检也被拦截。
>
> 排查发现Security默认拦截所有请求，后来我配置http.cors()，并用requestMatchers().permitAll()放行了登录、文章列表、GET评论，只对发表评论和个人资料左认证开放，同时放行了OPTIONS预检。
>
> 最后前后端联调通了。

---

## 部署与工具

### 7.项目是怎么运行的？用没用Git？说一下常用的几个命令？

> 我：生产环境是直接在IDE运行main方法，后来部署到服务器上就算打了jar包，用过git，平时用来上传自己的学习笔记，git add . ，git commit -m "笔记"，git push -u origin main。
>
> 面试官：完美，你的回到真实、清晰，尤其是打jar包部署这一点，很多实习生没做过。