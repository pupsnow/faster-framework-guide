# Node

Faster-Admin-Web开发环境依赖于 Node.js，如您已经安装了 Node.js，则可以忽略此文档。

## 介绍
Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。 
Node.js 使用了一个事件驱动、非阻塞式 I/O 的模型，使其轻量又高效。 
Node.js 的包管理器 npm，是全球最大的开源库生态系统。
Faster-Admin-Web同样使用NPM作为包管理。

## 安装 Node.js 环境

### macOS 用户
我们建议您使用 *nvm 来管理 Node.js 的安装。
打开终端，执行如下命令

```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
```

完成后重启终端，执行 nvm install --lts 来安装最新 LTS 版本的 Node.js

### Windows 用户
访问 Node.js 的[官网](https://nodejs.org/),下载对应平台且标记为 LTS 版本的安装包，并执行安装，安装成功后在终端执行：
验证安装的 Node.js 版本
在终端中执行如下命令 (Windows 下可以是 Git Bash 或其它终端模拟器)

```
node -v
npm -v
```

终端打印出 Node.js 和 npm 的版本，则表示安装成功。

## npm与cnpm

因为npm安装插件是从国外服务器下载，受网络影响大，可能出现异常，所以我们乐于分享的淘宝团队干了这事。来自官网：“这是一个完整 npmjs.org 镜像，你可以用此代替官方版本(只读)，同步频率目前为 10分钟 一次以保证尽量与官方服务同步。”

您可以访问[淘宝NPM镜像官方](http://npm.taobao.org/)进行安装使用。

