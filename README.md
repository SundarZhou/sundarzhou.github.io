## Fork from https://github.com/mzlogin/mzlogin.github.io


## Fork 指南

Fork 本项目之后，还需要做一些事情才能让你的页面「正确」跑起来。

1. 正确设置项目名称与分支。

   按照 GitHub Pages 的规定，名称为 `username.github.io` 的项目的 master 分支，或者其它名称的项目的 gh-pages 分支可以自动生成 GitHub Pages 页面。

2. 修改域名。

   如果你需要绑定自己的域名，那么修改 CNAME 文件的内容，并参考 [配置 GitHub Pages 站点的自定义域](https://docs.github.com/cn/pages/configuring-a-custom-domain-for-your-github-pages-site) 做好配置；如果不需要绑定自己的域名，那么删掉 CNAME 文件。

3. 修改配置。

   网站的配置基本都集中在 \_config.yml 文件中，将其中与个人信息相关的部分替换成你自己的，比如网站的 url、title、subtitle 和第三方评论模块的配置等。

   **评论模块：** 目前支持 disqus、gitment 和 gitalk，选用其中一种就可以了，推荐使用 gitalk。它们各自的配置指南链接在 \_config.yml 文件的 Comments 一节里都贴出来了。

   **注意：** 如果使用 disqus，因为 disqus 处理用户名与域名白名单的策略存在缺陷，请一定将 disqus.username 修改成你自己的，否则请将该字段留空。我对该缺陷的记录见 [Issues#2][3]。

4. 删除我的文章与图片。

   如下文件夹中除了 template.md 文件外，都可以全部删除，然后添加你自己的内容。

   * \_posts 文件夹中是我已发布的博客文章。
   * \_drafts 文件夹中是我尚未发布的博客文章。
   * \_wiki 文件夹中是我已发布的 wiki 页面。
   * images 文件夹中是我的文章和页面里使用的图片。

5. 修改「关于」页面。

   pages/about.md 文件内容对应网站的「关于」页面，里面的内容多为个人相关，将它们替换成你自己的信息，包括 \_data 目录下的 skills.yml 和 social.yml 文件里的数据。

   skills.yml 和 social.yml 里内容的含义可以参考：[_data 目录下的 yml 文件内容含义](https://mazhuang.org/2020/05/03/blog-template-qna/#_data-%E7%9B%AE%E5%BD%95%E4%B8%8B%E7%9A%84-yml-%E6%96%87%E4%BB%B6%E5%86%85%E5%AE%B9%E5%90%AB%E4%B9%89)。

6. [配置 jsDelivr 免费加速 GitHub Pages 博客的静态资源](https://mazhuang.org/2020/10/07/cdn-for-github-pages-2/), 这一步没做虽然好像看着没什么影响，但是会报 build-and-deploy error
