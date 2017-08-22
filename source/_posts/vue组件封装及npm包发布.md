---
layout: post
title: vue组件封装及npm包发布
date: 2017-08-21 09:36:39
tags:
    - vue组件封装
    - npm发布
---

# vue组件封装
vue2是基于web components标准的前端框架，它支持用户自定义组件，构建vue项目的过程中我们无处不在使用自定义组件，需要引入第三方组件库则需npm install,下面就简单介绍一下如何将自己自定义的组件按照vue制定的组件封装方式封装并发布到npm，这样便实现了自定义组件第三方化。
1、首先建一个自定义组件的文件夹，比如叫loading，里面有一个`index.js`，还有一个自定义组件`loading.vue`,在这个loading.vue里面就是这个组件的具体的内容，比如：
```
<template>
    <div>
        loading..............
    </div>
</template>

<script>
    export default {

    }
</script>

<style scoped>
    div{
        font-size:40px;
        color:#f60;
        text-align:center;
    }
</style>
```
2、在index.js中，规定使用这个组件的名字，以及使用方法，如：
```
import loadingComponent from './loading.vue'

const loading={
    install:function(Vue){
        Vue.component('Loading',loadingComponent)
    }  //'Loading'这就是后面可以使用的组件的名字，install是默认的一个方法
};

export default loading;
```
3、经过1、2两个步骤就已经完成了组件的封装，只要在index.js中规定了install方法，就可以像一些公共的插件一样使用Vue.use()来使用，如：：
```
import loading from './loading'

Vue.use(loading)
```
这是在入口文件中引入的方法，可以看到就像vue-resource一样，可以在项目中的任何地方使用自定义的组件了，比如在home.vue中使用
```
<template>
    <div>
        <Loading></Loading>
    </div>
</template>
```
# npm包发布
## 准备工具
- 安装nodeJS
- 注册一个github账户用于托管代码
- 注册一个npm账户
- 开发你的module，更新至github
- 发布module至npm
安装nodeJS已经github账户的使用在此不做介绍，需要将自己准备发布的代码托管到github上，然后你需要注册一个npm账户，接下来说明发布过程：
终端进入到项目文件夹，执行`npm init`命令，构建模块的描述文件，系统会提示你输入所需的信息，不想输入就直接Enter跳过。这里主要的几个配置如下:
- `name`就是你要发布的module名；
- `version`版本信息（每发布一次版本号都必须大于上一次发布的版本号）；
- `entry`入口文件
```
$ npm init

This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sane defaults.

See `npm help json` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg> --save` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
name: (node) easy_mongo
version: (0.0.0) 0.1.0
description: An easy mongodb client for node.js based on native mongodb driver.
entry point: (index.js) 
test command: test
git repository: https://github.com/Myqilixiang/zsh-koa-cli.git
keywords: koa2
author: zsh
license: (BSD-2-Clause) MIT
```
### npm注册
输入完用户名，密码，邮箱后没有错误信息就完成了。
```
$ npm adduser
Username: your name
Password: your password
Email: (this IS public) your email
```
查询或者登陆别的用户命令
```
$ npm whoami
$ npm login
```
### npm module 发布
module开发完毕后，剩下的就是发布啦，进入项目根目录，输入命令。
```
$ npm publish
```
这里有时候会遇到几个问题,问题1：
```
npm ERR! no_perms Private mode enable, only admin can publish this module:
```
这里注意的是因为国内网络问题，许多小伙伴把npm的镜像代理到淘宝或者别的地方了，这里要设置回原来的镜像。
```
npm config set registry=http://registry.npmjs.org
```
问题2：
```
npm ERR! you do not have permission to publish "your module name". Are you logged in as the correct user? 
```
提示没有权限，其实就是你的module名在npm上已经被占用啦，这时候你就去需要去npm搜索你的模块名称，如果搜索不到，就可以用，并且把package.json里的name修改过来，重新npm publish，看到如下信息就表示安装完成了，zsh-koa-cli就是我的模块名。
+ zsh-koa-cli@0.1.0
更新版本，发布
```
$ npm version 0.1.1
$ npm publish
```
### 版本号规范
npm社区版本号规则采用的是semver（语义化版本），主要规则版本格式：主版本号.次版本号.修订号，版本号递增规则如下：

- 主版本号：当你做了不兼容的 API 修改，
- 次版本号：当你做了向下兼容的功能性新增，
- 修订号：当你做了向下兼容的问题修正。
先行版本号及版本编译信息可以加到“主版本号.次版本号.修订号”的后面，作为延伸。
### 持续集成
目前npm上开源的项目实在是太多，从中找出靠谱的项目要花费一定的精力跟时间去验证，所以开发者都会对自己的开源项目持续更新，并且经过测试的项目更加值得信赖。对于刚上线并且github上star星数很少的项目，使用者都会怀疑，这个项目靠谱不？所以这时候你需要告诉他，老子靠谱，怎么做？持续集成。

目前Github已经整合了持续集成服务travis，我们只需要在项目中添加.travis.yml文件，在下一次push之后，travis就会定时执行npm test来测试你的项目，并且会在测试失败的时候通知到你，你也可以把项目当前的状态显示在README.md中，让人一目了然，比如React里的
![npm-version](/assets/img/npm-version.png)
`.travis.yml` 是一个YAML文件，具体的相关的配置见This，例子如下：
```
language: node_js
node_js:
  - "6"
  - "6.1"
  - "5.11"
services:
  - mongodb
  ```
  这个例子的是让travis在node.js的0.6.x，0.6.1，0.5.11三个版本下对项目进行测试，并且需要mongodb的服务。

  <font color=green size=35>End</font>
  至此你的一个module就开发完成了。

