---
title: Git 推送分支提示错误  
date: 2024-12-27T16:53:00  
lastMod: 2024-12-27T16:53:00  
tags: [Git]  
category: 笔记  
summary: Git 推送分支提示错误
---

错误信息：
`error: RPC failed; HTTP 500 curl 22 The requested URL returned error: 500 Internal Server Error`

原因分析：
the largest individual file size is over the git buffer size

解决方案：

1.  设置全局 `buffer size`
```ssh
git config --global http.postBuffer 157286400
```

2.  设置当前 repo `buffer size`
```ssh
git config http.postBuffer 157286400
```

3.  删除过大的文件，如图片、视频等