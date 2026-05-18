---
title: 强大的CSS变量
description: 全面了解 CSS 变量（Custom Properties）的定义、作用域、回退值、JavaScript 操作方式，以及主题切换等常见应用场景。
date: 2025-03-15
tags: ["css", "custom-properties", "javascript"]
category: 技术
---

在 CSS 中，**变量（Custom Properties）** 允许你定义可重用的值，方便在整个样式表中使用和修改。CSS 变量的基本语法如下：

## 1. 定义 CSS 变量

CSS 变量通常在 `:root` 伪类中定义，以便它们可用于整个文档：

```css
:root {
  --main-color: #3498db;
  --secondary-color: #2ecc71;
  --font-size: 16px;
}
```

- 变量名称以 `--` 开头，比如 `--main-color`
- `:root` 选择器相当于 `html`，使变量全局可用

## 2. 使用 CSS 变量

在 CSS 规则中使用 `var()` 函数引用变量。

语法：`var(自定义属性, 回退值)`

```css
body {
  color: var(--main-color);
  font-size: var(--font-size);
}

button {
  background-color: var(--secondary-color);
  border: 2px solid var(--main-color);
}
```

回退值用法：

```css
/* 在 component 的样式中 */
.component .header {
  /* header-color 没有被设置，将使用回退值 blue */
  color: var(--header-color, blue);
}

.component .text {
  color: var(--text-color, black);
}

/* In the larger application's style */
.component {
  --text-color: #080;
}
```

使用自定义属性作为回退值：

```css
:root {
  --backup-bg-color: teal;
}

body {
  /* main-bg-color 没有被设置，将使用回退值 backup-bg-color。
     如果 backup-bg-color 没有被设置，将使用回退值 white。 */
  color: var(--main-bg-color, var(--backup-bg-color, white));
}
```

## 3. 变量的作用域

- 变量可以定义在 `:root` 作用域，全局可用
- 也可以在特定的元素中定义，仅该元素及其子元素可以使用

```css
.card {
  --card-bg: #f5f5f5;
  background-color: var(--card-bg);
}
```

## 4. 提供默认值

如果变量未定义，可以使用 `var()` 提供默认值：

```css
p {
  color: var(--text-color, black); /* 如果 --text-color 未定义，则使用 black */
}
```

## 5. 在 JavaScript 中操作 CSS 变量

CSS 变量可以通过 JavaScript 动态修改：

```js
document.documentElement.style.setProperty("--main-color", "red");
```

## 6. CSS 变量 vs 预处理器变量

| 特性            | CSS 变量 (`var()`)     | 预处理器变量 (`SASS/LESS`) |
| --------------- | ---------------------- | -------------------------- |
| 作用域          | 运行时可变             | 编译时确定                 |
| JavaScript 操作 | 可以修改               | 不能修改                   |
| 计算            | 可与 `calc()` 结合使用 | 预计算                     |

## 7. 使用场景

### 动态主题切换

[动态主题切换演示视频](https://live.csdn.net/v/embed/469013)

[![动态主题切换演示封面](https://i-blog.csdnimg.cn/img_convert/25adce6a5da4937a891a6f362add4477.jpeg)](https://live.csdn.net/v/embed/469013)

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Title</title>
    <style>
      :root {
        --bg-color: white;
        --text-color: black;
      }

      .dark-theme {
        --bg-color: black;
        --text-color: white;
      }

      body {
        background-color: var(--bg-color);
        color: var(--text-color);
      }
    </style>
  </head>
  <body>
    <button onclick="handleClick(event)">深色主题</button>

    <script>
      let isDeep = false;

      function handleClick(e) {
        document.body.classList.toggle("dark-theme");
        isDeep = !isDeep;
        e.target.innerHTML = isDeep ? "浅色主题" : "深色主题";
      }
    </script>
  </body>
</html>
```

### 动态 hover

[动态 hover 演示视频](https://live.csdn.net/v/embed/469007)

[![动态 hover 演示封面](https://i-blog.csdnimg.cn/img_convert/58cd8401baff5bc08e50089123ee5350.jpeg)](https://live.csdn.net/v/embed/469007)

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Title</title>
    <style>
      div {
        width: 100px;
        height: 100px;
        background-color: #ccc;
      }

      div:hover {
        background-color: var(--hover-bg);
      }
    </style>
  </head>
  <body>
    <div id="box" style="--hover-bg: none">hello</div>

    <select id="select">
      <option value="red">红</option>
      <option value="green">绿</option>
      <option value="blue">蓝</option>
    </select>

    <script>
      const select = document.getElementById("select");
      const box = document.getElementById("box");

      box.style.setProperty("--hover-bg", select.value);

      const handleChange = (e) => {
        const value = e.target.value;
        box.style.setProperty("--hover-bg", value);
      };

      select.addEventListener("change", handleChange);
    </script>
  </body>
</html>
```

CSS 变量非常适合 **主题切换、响应式设计、动态样式调整** 等场景。
