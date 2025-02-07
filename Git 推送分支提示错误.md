---
title: Git 推送分支提示错误  
date: 2024-12-27T16:53:00  
lastMod: 2024-12-27T16:53:00  
tags: [Git]  
category: 笔记  
summary: Git 推送分支时，若遇到 HTTP 500 Internal Server Error 错误，可能是单个文件大小超过 Git 缓冲区大小。解决方案包括设置全局或当前仓库的 buffer size，或删除过大的文件。
---

错误信息：
`error: RPC failed; HTTP 500 curl 22 The requested URL returned error: 500 Internal Server Error`

原因分析：
the largest individual file size is over the git buffer size

解决方案：

1.  设置全局 `buffer size`
```bash
git config --global http.postBuffer 157286400
```

2.  设置当前 repo `buffer size`
```bash
git config http.postBuffer 157286400
```

3.  删除过大的文件，如图片、视频等