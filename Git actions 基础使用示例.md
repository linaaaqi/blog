---
title: GitHub Actions 及 GitHub Pages 简单搭配
date: 2019-09-28T00:00:00
lastMod: 2019-09-28T00:00:00
tags: [GitHub Actions, CI CD]
category: 笔记
summary: GitHub Actions 及 GitHub Pages 简单搭配
---

## 优势
可以在一个仓库或者多个仓库里保存源码及构建后的静态文件产物
全程自动化部署，无需人工干预
GitHub 代码托管，保证代码安全无虞

## GitHub Actions 是什么？
GitHub Actions 是由 GitHub 推出的包含 CI、CD、构建、测试及部署的自动化工作流

官方介绍为：

GitHub Actions makes it easy to automate all your software workflows, now with world-class CI/CD. Build, test, and deploy your code right from GitHub. Make code reviews, branch management, and issue triaging work the way you want.

## GitHub Pages 是什么？
GitHub Pages 是由 GitHub 推出的一项静态页面托管服务，可用以展示项目、个人、组织等相关页面。

具体 Github Pages 此处不做过多赘述，请自行查阅资料

## 简单使用
借用 Actions 的强大可自定义性质，我们可以使用 GitHub Actions 实现对 GitHub Pages 的自动化部署。

### 创建Git仓库并安装博客程序
首先创建一个用来保存静态页面的仓库，这里我们使用的静态页面生成器是 Hexo 博客框架

⚠️注意：代码仓库里保存的是博客程序的源码，无需执行 `hexo g` 上传 `public` 文件夹

### 创建 GitHub Actions 工作流文件

在目录 `./github/workflows` 下创建 YAML 文件，文件名自取，如 `deploy.yml`

内容填充为
```yaml
name: Deploy to GitHub Pages
on:
  push:
    branches:
      - master # 推送此分支触发部署流程

jobs:
  deploy:
    name: Deploy to GitHub Pages
    runs-on: ubuntu-latest
    steps:
      # 检出代码
      - uses: actions/checkout@master

      # 设置 Nodejs
      - uses: actions/setup-node@v2
        with:
          node-version: '14'
      - run: npm i -g hexo-cli
      - run: npm install
      - run: cd themes/obsidian && npm install
      - run: hexo g

      # 部署静态页面
      - name: Deploy
        uses: s0/git-publish-subdir-action@develop
        env:
          REPO: self
          BRANCH: gh-pages # Github Pages 部署分支
          FOLDER: public/
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

```

### 提交代码至 GitHub 仓库
GitHub 仓库会自动识别工作流文件，并且在你每次提交新的代码到 master 分支时开始执行一个 action
至此，GitHub Actions 与 GitHub Pages 简单搭配使用已结束。