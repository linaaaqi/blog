---
title: The web's Clipboard API
createdAt: 2024-12-27T16:53:00
---
source: [https://alexharri.com/blog/clipboard](https://alexharri.com/blog/clipboard)

**一、复制文本到剪贴板**

1. 简单的复制文本示例
```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF - 8">
    <title>Clipboard API - 复制文本</title>
</head>

<body>
    <button onclick="copyText()">复制文本</button>

    <script>
        async function copyText() {
            try {
                await navigator.clipboard.writeText('这是要复制到剪贴板的文本');
                console.log('已成功复制文本到剪贴板');
            } catch (err) {
                console.error('复制文本到剪贴板失败: ', err);
            }
        }
    </script>
</body>

</html>
```
在这个示例中，当用户点击按钮时，`copyText`函数被调用。这个函数使用`navigator.clipboard.writeText`方法将指定的文本（这里是'这是要复制到剪贴板的文本'）复制到剪贴板。如果操作成功，会在控制台输出成功信息；如果失败，则会输出错误信息。

2. 从输入框获取文本并复制
```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF - 8">
    <title>Clipboard API - 从输入框复制文本</title>
</head>

<body>
    <input type="text" id="inputText" value="这里是输入框中的初始文本">
    <button onclick="copyInputText()">复制输入框中的文本</button>

    <script>
        async function copyInputText() {
            const input = document.getElementById('inputText');
            const text = input.value;
            try {
                await navigator.clipboard.writeText(text);
                console.log('已成功复制输入框中的文本到剪贴板');
            } catch (err) {
                console.error('复制输入框中的文本到剪贴板失败: ', err);
            }
        }
    </script>
</body>

</html>
```
这个示例中，有一个输入框和一个按钮。当用户点击按钮时，`copyInputText`函数会获取输入框中的文本内容，然后使用`navigator.clipboard.writeText`将其复制到剪贴板。

**二、从剪贴板读取文本**

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF - 8">
    <title>Clipboard API - 读取剪贴板文本</title>
</head>

<body>
    <button onclick="readClipboardText()">读取剪贴板文本</button>

    <script>
        async function readClipboardText() {
            try {
                const clipboardText = await navigator.clipboard.readText();
                console.log('从剪贴板读取的文本: ', clipboardText);
            } catch (err) {
                console.error('读取剪贴板文本失败: ', err);
            }
        }
    </script>
</body>

</html>
```
在这个示例中，当用户点击按钮时，`readClipboardText`函数被调用。这个函数使用`navigator.clipboard.readText`方法从剪贴板读取文本内容。如果读取成功，会在控制台输出读取到的文本；如果失败，则会输出错误信息。