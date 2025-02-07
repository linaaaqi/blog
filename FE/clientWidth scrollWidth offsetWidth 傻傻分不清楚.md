---
title: clientWidth scrollWidth offsetWidth 傻傻分不清楚  
date: 2025-01-16T15:21:00  
lastMod: 2025-01-16T15:21:00  
tags: [Javascript]  
category: 笔记  
summary: clientWidth scrollWidth offsetWidth 傻傻分不清楚
---

## 一、Element.clientWidth

### 1. 定义
`clientWidth`属性返回元素的内部宽度，单位为像素。它涵盖了元素的内边距（padding），但不包含边框（border）、外边距（margin）以及滚动条（若存在）。

### 2. 计算公式
`clientWidth = 内容宽度 + 左侧内边距 + 右侧内边距`

### 3. 示例
假设有一个`<div>`元素，其 CSS 样式如下：
```css
div {
	width: 200px;
	padding: 20px;
	border: 10px solid black;
	margin: 15px;
}
```

```html
<div id="myDiv">示例文本</div>
```

使用 TypeScript 获取其`clientWidth`：
```typescript
const myDiv = document.getElementById('myDiv');
console.log(myDiv.clientWidth); 
// 输出：240 (200 + 20 + 20)
```

## 二、Element.scrollWidth

### 1. 定义
`scrollWidth`属性返回元素的完整宽度，包括因溢出而在屏幕上不可见的部分。它包含元素的内边距（padding），但不包括边框（border）和外边距（margin）。

### 2. 计算公式
`scrollWidth = 内容实际宽度（含溢出部分） + 左侧内边距 + 右侧内边距`

### 3. 示例
若上述`<div>`元素中的文本很长，导致出现水平滚动条：
```html
<div id="myDiv">这是一段很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长的文本</div>
```

使用 TypeScript 获取其`scrollWidth`：
```typescript
const myDiv = document.getElementById('myDiv');
console.log(myDiv.scrollWidth); 
// 输出：文本实际宽度 + 40 (20 + 20)
```

## 三、HTMLElement.offsetWidth

### 1. 定义
`offsetWidth`属性返回元素的布局宽度。它包括元素的边框（border）、内边距（padding）以及水平滚动条（若存在），但不包含外边距（margin）。

### 2. 计算公式
`offsetWidth = clientWidth + 左侧边框宽度 + 右侧边框宽度`

### 3. 示例
继续使用上述`<div>`元素，使用 TypeScript 获取其`offsetWidth`：
```typescript
const myDiv = document.getElementById('myDiv');
console.log(myDiv.offsetWidth); 
// 输出：260 (200 + 20 + 20 + 10 + 10)
```

## 四、其他类似属性

### 1. Element.clientHeight
与`clientWidth`类似，`clientHeight`返回元素的内部高度，包含内边距（padding），但不包括边框（border）、外边距（margin）和滚动条（若存在）。  
`clientHeight = 内容高度 + 顶部内边距 + 底部内边距`

### 2. Element.scrollHeight
与`scrollWidth`类似，`scrollHeight`返回元素的完整高度，包括因溢出而在屏幕上不可见的部分，包含内边距（padding），但不包括边框（border）和外边距（margin）。  
`scrollHeight = 内容实际高度（含溢出部分） + 顶部内边距 + 底部内边距`

### 3. HTMLElement.offsetHeight
与`offsetWidth`类似，`offsetHeight`返回元素的布局高度，包括元素的边框（border）、内边距（padding）和垂直滚动条（若存在），但不包含外边距（margin）。  
`offsetHeight = clientHeight + 顶部边框宽度 + 底部边框宽度`

理解这些属性之间的差异，有助于在前端开发中更精确地获取和操作 DOM 元素的尺寸信息，以满足诸如布局调整、响应式设计等各种开发需求。
