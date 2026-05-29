# HTML 元素

> HTML 文档由一系列HTML元素（Element）构成，是网页结构的基础

## 什么是 HTML 元素？

> **HTML** 元素是从**开始标签（start tag）**到**结束标签（end tag）**之间的完整结构

| 开始标签                 | 元素内容       | 结束标签 |
| ------------------------ | -------------- | -------- |
| `<p>`                    | 这是一个段落   | `</b>`   |
| `<a href="default.htm">` | 这是一个链接   | `</a>`   |
| `<br>`                   | 换行（空元素） | 无       |

**说明**：

- 开始标签也称为 **起始标签（opening tag）**
- 结束标签也称为 **闭合标签（closing tag）**

---

## HTML 元素语法规则

- 元素以**开始标签**开始，以**结束标签**结束
- 元素内容位于开始标签和结束标签之间
- 部分元素为**空元素（empty element）**，没有内容
- 空元素通常在开始标签中自闭合（如`<br />`）
- 大多数元素可以包含**属性（Attribute）**

---

## HTML 元素的嵌套

> HTML 元素可以相互嵌套，形成树状结构
>
> - 一个元素可以包含其他元素
> - **HTML** 文档本质上是一个嵌套结构（**DOM树）**

---

## HTML 文档示例

```bash
<!DOCTYPE html>
<html>

  <body>
    <p>这是第一个段落。</p>
  </body>

</html>
```

> 该示例包含 3 个核心元素：html、body、p

### 示例解析

- `<p>`元素（段落）

```bash
<p>这是一个段落。</p>
```

> 定义一个段落，包含文本内容

- `<body>`元素（主体）

```bash
<body>
  <p>这是第一个段落。</p>
</body>
```

> 定义页面的主体内容，可以包含多个子元素

- `<html>`元素（根元素）

```bash
<html>

  <body>
    <p>这是第一个段落。</p>
  </body>

</html>
```

> 整个 HTML 文档的根节点，所有内容都必须包含在其中

---

## 关于结束标签

- 部分情况下，浏览器会自动补全缺失的结束标签

```bash
<p>这是一个段落
<p>这是另一个段落
```

> 虽然可以正常显示，但**不推荐依赖这种行为**，可能导致
>
> - 结构错乱
> - 样式异常
> - 兼容性问题

---

## HTML空元素

> 空元素是指没有内容的元素，例如
>
> - `<br>`（换行）
> - `<img>`（图片）
> - `<input>`（输入框）

**推荐写法**：

```bash
<br />
```

> 这种写法兼容 HTML、XHTML 和 XML，更规范

---

## 最佳实践：使用小写标签

> HTML 标签**不区分大小写**，但推荐
>
> - 统一使用**小写标签**
> - 符合 W3C 规范
> - 提升代码可读性与一致性

---

## HTML 属性

> 属性是 HTML 元素提供的附加信息
>
> 属性通常出现在 HTML 标签的开始标签中，用于定义元素的行为、样式、内容或其他特性
>
> 属性总是以`name = "value"`的形式写在标签内，**name** 是属性的名称，**value** 是属性的值

- HTML 元素可以设置**属性**
- 属性可以在元素中添加**附加信息**
- 属性一般描述于**开始标签**
- 属性总是以名称/值对的形式出现，**比如：name = ”value“**

---

## 属性实例

> HTML 链接由`<a>`标签定义
>
> 链接的地址在 **href 属性** 中指定

### 实例

```bash
<a href="http://www.runoob.com">这是一个链接</a>
```

### 效果

<a href="http://www.runoob.com">这是一个链接</a>

---

## HTML 属性常用引用属性值

> 属性值应该始终被包括在引号内
>
> 双引号是最常用的，不过使用单引号也没有问题

- 使用双引号

```bash
<a href="https://www.runoob.com">Link</a>
```

- 使用单引号

```bash
<a href='https://www.runoob.com'>Link</a>
```

**提示**：在某些个别的情况下，比如属性值本身就含有双引号，那么必须使用单引号

```bash
<a href="https://www.runoob.com?q='search'">Link</a>
```

---

## HTML 提示：使用小写属性

> 属性和属性值对大小写不敏感
>
> 不过，万维网联盟在其 HTML 4 推荐小写的属性/属性值
>
> 而新版本的 (X)HTML 要求使用小写属性

## 全局属性

> 全局属性是所有 HTML 元素都可以使用的属性
- `id`：为元素指定唯一的标识符

```bash
<div id="header">This is the header</div>
```

- `class`：为元素指定一个或多个类名，用于 **CSS** 或 **JavaScript** 选择

```bash
<p class="text highlight">This is a highlighted text.</p>
```

- `style`：用于直接在元素上应用 **CSS** 样式

```bash
<p style="color: blue; font-size: 14px;">This is a styled paragraph.</p>
```
<p style="color: blue; font-size: 14px;">This is a styled paragraph.</p>
- `title`：为元素提供额外的提示信息，通常在鼠标悬停时显示

```bash
<abbr title="HyperText Markup Language">HTML</abbr>
```

<abbr title="HyperText Markup Language">HTML</abbr>

- `data-*`：用于存储自定义数据，通常通过 **JavaScript** 访问

```bash
<div data-user-id="12345">User Info</div>
```

---

## 特定元素的属性

> 某些属性仅适用于特定的 **HTML** 元素

- `href`（用于<a>和<link>元素）：指定链接的目标 **URL**

```bash
<a href="https://www.example.com">Visit Example</a>
```

<a href="https://www.example.com">Visit Example</a>

- `src`（用于<img>，<script>，<ifrane>等元素）：指定外部资源的 **URL**

```bash
<img src="image.jpg" alt="An example image">
```

<img src="C:\Users\Administrator\Desktop\resume\屏幕截图 2026-05-24 151815.png" alt="An example image">

- `alt`（用于<img>元素）：为图像提供替代文本，当图像无法显示时展示

```bash
<img src="image.jpg" alt="An example image">
```

<img src="image.jpg" alt="An example image">

- `type`（用于<input>和<button>元素）：指定输入控件的类型

```bash
<input type="text" placeholder="Enter your name">
```

- `value`（用于<input>，<button>，<option>元素）：指定元素的初始值

```bash
<input type="text" value="Default Value">
```

- `disable`（用于表单元素）：禁用元素，使其不可交互

```bash
<input type="text" disabled>
```

- `checked`（用于<input>）：禁用元素，使其不可交互

```bash
<input type="text" disabled>
```

