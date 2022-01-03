## GitBook安装

### 1.GitBook介绍
- 官方给的定义：
> GitBook is a command line tool (and Node.js library) for building beautiful books using GitHub/Git and Markdown (or AsciiDoc).
- GitBook 是一个基于 Node.js 的命令行工具，可使用 Github/Git 和 Markdown 来制作精美的电子书

### 2.安装NodeJS、Gitbook-cli

#### 2.1.安装NodeJS
- Windows：下载安装即可：[下载地址](https://nodejs.org/en/download/releases/) `(推荐12以上版本)`
- Linux(CentOS7):  `curl -fsSL https://rpm.nodesource.com/setup_12.x | bash -` 
- Linux(Ubuntu):  `curl -sL https://deb.nodesource.com/setup_12.x | bash -`
- 安装完成后检验：

```
$ node -v
v12.22.8
$ npm -v
6.14.15

```  

- 配置国内`NPM`淘宝源：
- `npm config set registry https://registry.npm.taobao.org`
- `npm config get registry`


#### 2.2.安装Gitbook-cli
- `Gitbook-cli` 是安装和管理GitBook版本库的程序
- 安装： `npm install gitbook-cli -g`
- 安装完成后验证：

```
$ gitbook -V
CLI version: 2.3.2
GitBook version: 3.2.3
```
- 安装特定版本
    + 前提是已经安装了`Gitbook-cli`
    + 查看可用版本： `gitbook ls-remote`
    + 安装指定版本： `gitbook fetch 3.2.3`

#### 2.3.Gitbook常用命令
- 初始化`gitbook`: `gitbook init`
- 下载安装插件等： `gitbook install`
- `gitbook install`安装会比较慢, 推荐`npm install plugin-xxx`来安装
- 生成静态页面： `gitbook build`
- 本地运行： `gitbook serve`
- 列出本地可用版本： `gitbook ls`
- 列出远程可用版本： `gitbook ls-remote`
- 更新到最新版本： `gitbook update`
- 卸载指定版本： `gitbook uninstall 3.2.3`

#### 2.4.HelloWorld
- 在需要创建`book`的空目录中执行初始化命令： `gitbook init`

```bash
$ gitbook init
warn: no summary file in this book
info: create README.md
info: create SUMMARY.md
info: initialization is finished
```

- 然后在 `README.md` 追加简单的

```bash
### HelloWorld
```

- 接着运行： `gitbook serve`

```bash
$ gitbook serve
Live reload server started on port: 35729
Press CTRL+C to quit ...

info: 7 plugins are installed
info: loading plugin "livereload"... OK
info: loading plugin "highlight"... OK
info: loading plugin "search"... OK
info: loading plugin "lunr"... OK
info: loading plugin "sharing"... OK
info: loading plugin "fontsettings"... OK
info: loading plugin "theme-default"... OK
info: found 1 pages
info: found 0 asset files
info: >> generation finished with success in 0.4s !

Starting server ...
Serving book on http://localhost:4000
```

- 浏览器访问 `http://localhost:4000` 即可看到 `HelloWorld`