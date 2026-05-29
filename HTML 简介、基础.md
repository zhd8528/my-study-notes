# HTML 简介/基础

> 超文本标记语言（HyperText Markup Language）是一种用于创建网页的标准标记语言
>
> 可以用HTML建立自己的 WEB 站点，HTML 运行在浏览器上，由浏览器来解析

## HTML 实例

```ba
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>菜鸟教程(runoob.com)</title>
</head>
<body>
    <h1>我的第一个标题</h1>
    <p>我的第一个段落。</p>
</body>
</html>
```

### 效果：

<!DOCTYPE html> <html> <head> <meta charset="utf-8"> <title>菜鸟教程(runoob.com)</title> </head> <body>    <h1>我的第一个标题</h1>    <p>我的第一个段落。</p> </body> </html>

---

### 实例解析

![解析图片](https://zhd-blog.oss-cn-beijing.aliyuncs.com/02A7DD95-22B4-4FB9-B994-DDB5393F7F03.jpg)

- `<!DOCTYPE html>`声明为 HTML5 文档
- `<html>`元素是 HTML 页面的根元素
- `<head>`元素包含了文档的元（meta）数据，如`<meta charset="utf-8">`定义网页编码格式为 utf-8
- `<title>`元素描述了文档的标题
- `<body>`元素包含了可见的页面内容
- `<h1>`元素定义一个大标题
- `<p>`元素定义一个段落

---

## HTML 文档的后缀名

- `.html`
- `.htm`

---

## 什么是 HTML？

**HTML 是用来描述网页的一种语言**

- **HTML** 指的是超文本标记语言：**H**yper**T**ext **M**arkup **L**anguage
- **HTML** 不是一种编程语言，而是一种**标记**语言
- 标记语言是一套**标记标签**（markup tag）
- **HTML** 使用标记标签来**描述**网页
- **HTML** 文档包含了 HTML **标签**及**文本**内容
- **HTML** 文档也叫做 **web 页面**

---

## HTML 标签

**HTML 标记标签通常被称为 HTML 标签（HTML tag）**

- **HTML** 标签是由尖括号包围的关键词，比如`<html>`
- **HTML** 标签通常是成对出现的，比如 `<b>`和 `</b>`
- 标签对中的第一个标签是开始标签，第二个标签是结束标签
- 开始和结束标签也被称为开放标签和闭合标签

==<标签>==内容==</标签>==

---

## HTML 元素

> ”HTML 标签“和”HTML 元素“通常都是描述同样的意思
>
> 但是严格来讲，一个 HTML 元素包含了开始标签与结束标签

---

## Web 浏览器

> Web 浏览器（如谷歌浏览器，Internet Explorer，Firefox，Safari）是用于读取 HTML 文件，并将其作为网页显示
>
> 浏览器并不是直接显示的 HTML 标签，但可以使用标签来决定如何展现 HTML 页面的内容给用户

---

## HTML 网页结构

### 一个可视化的 HTML 页面结构，只有`<body>`区域（白色部分）才会在浏览器中显示

![截图](https://zhd-blog.oss-cn-beijing.aliyuncs.com/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202026-05-28%20214906.png)

---

## <!DOCTYPE> 声明

> `<!DOCTYPE>` 声明有助于浏览器中正确显示网页
>
> 网络上有很多不同的文件，如果能够正确声明 **HTML** 的版本，浏览器就能正确显示网页内容
>
> doctype 声明是不区分大小写的
>
> **通用声明**：HTML5 <!DOCTYPE html>

---

## 中文编码

> 目前在大部分浏览器中，直接输出中文会出现中文乱码的情况，这时候我们就需要在头部将字符声明为 UTF-8 或 GBK

---

## HTML 标题

> HTML 标题（Heading）是通过`<h1> - <h6>`标签来定义的

### 实例

```bash
<h1>这是一个标题</h1>
<h2>这是一个标题</h2>
<h3>这是一个标题</h3>
```

### 效果

<h1>这是一个标题</h1>
<h2>这是一个标题</h2>
<h3>这是一个标题</h3>

---

## HTML 段落

> HTML 段落是通过标签`<p>`来定义的

### 实例

```bash
<p>这是一个段落。</p>
<p>这是另外一个段落。</p>
```

### 效果

<p>这是一个段落。</p>
<p>这是另外一个段落。</p>

---

## HTML 链接

> HTML 链接是通过标签`<a>`来定义的

### 实例

```bA
<a href="https://www.runoob.com">这是一个链接</a>
```

### 效果

<a href="https://www.runoob.com">这是一个链接</a>

**提示**：在 **href** 属性中指定链接的地址

---

## HTML 图像

> HTML 图像是通过标签`<img>`来定义的

### 实例

```b
<img src="/images/logo.png" width="258" height="39" />
```

### 效果

<img src="C:\Users\Administrator\Desktop\resume\屏幕截图 2026-05-24 151815.png" width="100" height="100" />

**注意**：图像的名称和尺寸是以属性的形式提供的
