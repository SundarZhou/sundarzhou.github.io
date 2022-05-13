---
layout: post
title: "Github Page自动部署报错"
date: 2022-05-14 00:28:18 +0800
categories: jsDelivr GithubPages
---

突然发现每次部署的时候都会卡在 build-and-deploy 报错误：
![](/images/blog/build-and-deploy-error.png)

各种Google无关，但是看报错信息发现是这个脚本造成的 `.github/workflows/ci.yml`
看了下作者的源码发现需要配置github完成 jsDelivr的JSON 资源加速

步骤如下：

1. 在 GitHub 新建一个 Personal access Token：

    Settings --> Developer settings --> Personal access tokens --> Generate new token --> 填写 note，勾选 public_repo，生成之后复制 token 值备用。

2. 在博客源码仓库的 Settings --> Secrets --> Actions --> New repository secret，Name 填 `ACCESS_TOKEN`，Value 填第 1 步里复制的 token 值；

3. .github/workflows/ci.yml 的内容如下，不需要修改：

    ```yaml
    name: Build and Deploy

    on:
      push:
        branches: [ master ]

    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout
            uses: actions/checkout@v2.3.1
            with:
              persist-credentials: false

          - name: Set Ruby 2.7
            uses: actions/setup-ruby@v1
            with:
              ruby-version: 2.7

          - name: Install and Build
            run: |
              gem install bundler
              bundle install
              bundle exec jekyll build

          - name: Deploy
            uses: JamesIves/github-pages-deploy-action@3.6.2
            with:
              ACCESS_TOKEN: ${{ secrets.ACCESS_TOKEN }}
              BRANCH: built
              FOLDER: _site
              CLEAN: true
    ```

    大意就是在向 master 分支 push 代码时，自动执行 checkout、初始化 ruby 环境、安装 Jekyll 并编译博客源码的工作，最后将编译生成的 _site 目录里的内容推送到 built 分支。对 GitHub Actions 感兴趣的同学可以自行参考官方说明学习。

参考文章：
- [使用 jsDelivr 免费加速 GitHub Pages 博客的静态资源](https://mazhuang.org/2020/05/01/cdn-for-github-pages/)
