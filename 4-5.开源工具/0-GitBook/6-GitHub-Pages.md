## GitHub-Pages

### 1.GitHub-Pages 是什么
- `GitHub-Pages`是一个免费的静态站点托管服务
- 特点如下:
  + 操作简单、完全免费
  + 支持静态脚本
  + 自定义域名
  + 功能多，玩法丰富

- 搭配 `GitHub` 的 `CICD`服务: `Actions` 可以实现更加灵活的功能


### 2.如何使用GitHub-Pages
  - 参考[官方文档](https://pages.github.com/)很容易搭建
  - 简述下步骤:
    + 1.创建一个公开仓库，仓库名必须为: `username.github.io`, `username` 为你的`GitHub`用户名
    + 2.`clone`该项目并简单添加一个`index.html`,内容随便
    + 3.推送至远程仓库
    + 4.访问`username.github.io`即可访问到自己 添加的`index.html`的内容
  

### 3.自定义域名
- 1.在 `username.github.io`仓库中的`settings`、`Pages`可以设置`自定义域名`
- 2.前往自己的域名服务商那里添加一个域名的`CNAME`记录
- 3.等待生效后验证
- 4.因为众所周知的原因，`GitHub`在国内访问比较慢
- 5.因此可以使用`CDN`技术将`username.github.io`作为`回源地址`加快国内访问速度

### 4.静态博客框架
- 借助`静态博客框架`可以让我们专注于写作
- 常用的静态博客框架:
  + [Jekyll](https://jekyllrb.com/), [github-pages-site-with-jekyll](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll)
  + [Hexo](https://hexo.io/)
  + [Hugo](https://gohugo.io/)
  + [GitBook](https://www.gitbook.com/)

- 可以根据自己的喜好进行选择

### 5.gh-pages分支
- `普通用户`和`组织`的站点的`发布源`是默认的`master`分支，而普通项目的`发布源`是`gh-pages`分支
- `发布源`可以在仓库中的`settings`、`Pages`页面设置
- `静态博客框架`需要配置环境来预览页面，最后需要`build`出`静态资源文件`
- 将`静态资源文件`推送至`username.github.io`仓库，我们才可以通过互联网访问我们的站点
- 这一过程过于复杂和繁琐，那有没有什么方法可以简化这个过程呢
- 可以利用 `GitHub` 的 `CICD`服务: `Actions`，自动将 `master`分支的代码 `build` 后推送至 `gh-pages`
- 这样`master`分支就是`源代码`， `gh-pages` 分支就是`编译`后的`静态文件`
