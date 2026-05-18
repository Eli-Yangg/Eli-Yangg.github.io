---
title: 拖拽 API（Drag and Drop API）入门与列表排序实践
description: 从事件类型、draggable、DataTransfer 到列表拖拽排序，快速掌握 HTML5 Drag and Drop API，并了解移动端替代方案。
date: 2025-03-16
tags: ["html5", "javascript", "drag-and-drop"]
category: 技术
---

> 拖拽 API（Drag and Drop API）是 HTML5 提供的一组能力，可以让网页元素支持拖动与放置。开发者可以给元素增加拖拽行为，用户再通过鼠标把元素拖到指定区域。

## 事件类型

- `dragstart`：事件主体是被拖拽元素，在开始拖拽时触发。
- `drag`：事件主体是被拖拽元素，在拖拽过程中持续触发。
- `dragenter`：事件主体是目标元素，在被拖拽元素进入目标区域时触发。
- `dragover`：事件主体是目标元素，在被拖拽元素在目标区域内移动时触发。
- `dragleave`：事件主体是目标元素，在被拖拽元素离开目标区域时触发。
- `drop`：事件主体是目标元素，在目标元素接收被拖拽元素时触发。
- `dragend`：事件主体是被拖拽元素，在整个拖拽操作结束时触发。

## `draggable` 属性

HTML 元素可以通过设置 `draggable="true"` 来启用拖动，例如：

```html
<div draggable="true">可拖动的元素</div>
```

## `DataTransfer` 对象

拖拽相关事件里的 `event` 对象包含 `dataTransfer` 属性，用来存储和传递拖拽数据。常见写法是通过 `dataTransfer.setData()` 设置数据，再通过 `dataTransfer.getData()` 读取数据。

![DataTransfer 对象示意图](https://i-blog.csdnimg.cn/direct/32cdb60ed05f44feaa5a5c3683ceebee.png)

## 列表拖拽排序

### 演示

[列表拖拽排序演示视频](https://live.csdn.net/v/embed/469176)

[![列表拖拽排序演示封面](https://i-blog.csdnimg.cn/img_convert/9491c8860cf7246c667635257681b1c3.jpeg)](https://live.csdn.net/v/embed/469176)

### 完整代码

#### `index.css`

```css
.list {
  padding: 24px 5%;
}

.list .list-item {
  cursor: move;
  user-select: none;
  background-color: royalblue;
  padding: 16px 24px;
  border-radius: 5px;
  margin-bottom: 8px;
  color: white;
}

.list-item.moving {
  background-color: #ccc;
  color: transparent;
}
```

#### `index.html`

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>Document</title>
    <link rel="stylesheet" href="./index.css" />
  </head>
  <body>
    <div class="list">
      <div draggable="true" class="list-item">1</div>
      <div draggable="true" class="list-item">2</div>
      <div draggable="true" class="list-item">3</div>
      <div draggable="true" class="list-item">4</div>
      <div draggable="true" class="list-item">5</div>
    </div>

    <script src="./index.js"></script>
  </body>
</html>
```

#### `index.js`

```js
const list = document.querySelector(".list");
let source;

const handleDragStart = (event) => {
  source = event.target;
  event.dataTransfer.effectAllowed = "move";

  setTimeout(() => {
    event.target.classList.add("moving");
  }, 0);
};

const handleDragEnter = (event) => {
  event.preventDefault();

  if (!source) return;

  const target = event.target;
  if (target === list || target === source) return;

  const sourceIndex = Array.from(list.children).indexOf(source);
  const targetIndex = Array.from(list.children).indexOf(target);

  if (sourceIndex < targetIndex) {
    list.insertBefore(target, source);
    return;
  }

  list.insertBefore(source, target);
};

const handleDragEnd = (event) => {
  event.target.classList.remove("moving");
  source = null;
};

list.addEventListener("dragstart", handleDragStart);
list.addEventListener("dragenter", handleDragEnter);
list.addEventListener("dragend", handleDragEnd);
list.addEventListener("dragover", (event) => {
  // 不阻止默认行为的话，元素无法在目标区域完成放置。
  event.preventDefault();
});
```

## 移动端对 Drag API 的支持

支持较差，主要原因如下：

1. 原生支持有限
   - 移动端的触摸事件（`touchstart`、`touchmove`、`touchend`）和鼠标事件不同，很多浏览器不会触发 `dragstart`、`dragover`、`drop` 这类拖拽事件。
   - iOS Safari 和很多 Android 浏览器在移动端场景下对 `Drag API` 的支持并不理想，尤其是在 WebView 里更明显。

2. 触摸事件冲突
   - 触摸屏幕时，系统通常优先触发 `touch` 事件，而不是 `drag` 事件。
   - 比如在 iOS 上，`touchmove` 默认更偏向页面滚动，不会像桌面端那样自然触发拖拽流程。

3. 性能和交互体验
   - 在移动端，拖拽交互通常会被滑动、长按拖动等手势替代。
   - `draggable` 属性在桌面端可以正常生效，但在移动端一般不会按预期响应 `dragstart`。

## 解决方案

如果需要在移动端实现拖拽效果，更推荐下面两种方式：

1. 使用 `Touch Events` 代替 `Drag API`
   - 监听 `touchstart`、`touchmove`、`touchend`，手动计算元素位置并更新样式。

2. 使用第三方库
   - [SortableJS](https://github.com/SortableJS/Sortable)：同时支持 PC 和移动端。
   - [Interact.js](https://interactjs.io/)：提供更丰富的拖拽交互能力。

## 移动端拖拽示例

[移动端拖拽演示视频](https://live.csdn.net/v/embed/469198)

[![移动端拖拽演示封面](https://i-blog.csdnimg.cn/img_convert/0f000d88f13d29897c38c011b5874ec5.jpeg)](https://live.csdn.net/v/embed/469198)

```js
const item = document.getElementById("draggable");

let offsetX = 0;
let offsetY = 0;

item.addEventListener("touchstart", function (event) {
  const touch = event.touches[0];
  const rect = item.getBoundingClientRect();

  offsetX = touch.clientX - rect.left;
  offsetY = touch.clientY - rect.top;

  item.style.position = "absolute";
});

item.addEventListener("touchmove", function (event) {
  event.preventDefault();

  const touch = event.touches[0];
  const newLeft = touch.clientX - offsetX;
  const newTop = touch.clientY - offsetY;

  item.style.left = `${newLeft}px`;
  item.style.top = `${newTop}px`;
});
```

这样就能在移动端实现一个基础的拖拽效果，而不依赖桌面端的 `Drag API`。
