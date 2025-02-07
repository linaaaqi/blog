---
title: 通过Gitlab CI构建Docker镜像
date: 2021-01-01T00:00:00
lastMod: 2021-01-01T00:00:00
tags: [Docker, Gitlab CI, dind, Docker Image]
category: 笔记
summary: 本文介绍了如何通过Gitlab CI在Docker环境中构建Docker镜像。首先，解释了`dind`（Docker in Docker）的概念，然后展示了项目的文件结构，包括`.gitlab-ci.yml`和`Dockerfile`。接着，详细讲解了`.gitlab-ci.yml`文件的配置，特别是如何使用`docker:dind`服务来构建和推送Docker镜像。最后，提供了相关文档链接供进一步阅读。
---

## 前情提要
Gitlab CI 环境一般有：docker, shell。本文讨论在docker运行环境中如何再次构建Docker镜像。  
有点先有鸡还是先有蛋的意思，所以docker的解决方案是`dind`。

> `dind` 指的是 Docker in Docker，一种在docker容器内使用docker的方法

## 构建
首先看下文件结构
```shell
$ tree     
.
├── .gitlab-ci.yml
└── Dockerfile

0 directories, 3 files

```

### Dockerfile
不必多说，docker构建镜像必备文件

### .gitlab-ci.yml
gitlab ci 配置文件

直接看下配置文件

```dockerfile
# This file is a template, and might need editing before it works on your project.
build:
  tags:
    - docker-ci-runner
  # Official docker image.
  image: docker:latest
  services:
    - docker:dind
  before_script:
    - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD" $CI_REGISTRY
  script:
    - docker build --tag "$CI_REGISTRY_IMAGE:$CI_COMMIT_TAG" .
    - docker push "$CI_REGISTRY_IMAGE:$CI_COMMIT_TAG"
  only:
    # 仅运行在添加标签时
    - tags

```
前置知识需要了解 .gitlab-ci.yml 配置  
文档可以查看[这里](https://docs.gitlab.com/ee/ci/yaml/index.html)  
内容很简单，声明image、tags、script、only、services  
主要关注services内声明使用了docker:dind，借此可以完成docker容器 内构建docker 镜像。

```shell
docker build --tag "$CI_REGISTRY_IMAGE:$CI_COMMIT_TAG" .
```
此命令完成了构建镜像任务，且使用git标签名作为镜像标签。

## 推送
```shell
docker push "$CI_REGISTRY_IMAGE:$CI_COMMIT_TAG"
```
Gitlab CE版本也是支持docker镜像库的，上面的配置可以完成推送至镜像库。  
至此完成了镜像构建和推送。