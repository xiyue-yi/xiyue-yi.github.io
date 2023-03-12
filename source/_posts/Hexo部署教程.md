---
title: Hexo部署踩坑教程 & Hexo如何仅通过修改_posts更新网页
date: 2023-03-11 23:02:49
index_img: /img/白鹤梁.jpeg
tags: 
- Hexo
- GitHub-pages
---



最近接触Hexo搭建Github Pages的内容，在部署的过程中遇到了一些坑点，记录并分享出来。

{% note success %}
**【划重点】主要坑点：拷贝theme仓库之后产生的子模块冲突问题、源代码和静态文件存储不同分支的冲突问题。** 
{% endnote %}

<!-- more -->

### 远程仓库创建

在GitHub上新建名为 ***username*\.github.io** 的Repo，`username` 是你在 GitHub 上的用户名。



### Hexo安装

Hexo的安装需要基于Node.js和Git程序，具体的安装过程见Hexo官网的详细说明：

[安装|Hexo](https://hexo.io/zh-cn/docs/)

```shell
$ npm install -g hexo-cli
```



### 本地仓库初始化

1. 参考[Hexo官网教程](https://hexo.io/zh-cn/docs/setup)，新建文件夹<folder>作为项目仓库：

```shell
$ hexo init <folder>
$ cd <folder>
$ npm install
```

执行`npm install`指令是默认安装所有`package.json`文件中列出的依赖包。

新建完成后，指定文件夹的目录如下

```
.
├── _config.yml
├── package.json
├── scaffolds
├── source
| ├── _drafts
| └── _posts
└── themes
```

* `_config.yml`中对网站信息进行配置
* `package.json`定义静态文件生成需要的基础依赖包
* `source/_posts/`路径下存放所有Markdown格式的blog文件
* `themes/`中存放使用的Hexo主题



2. 然后，将指定文件夹初始化为Git Repo：

```shell
$ git init
Initialized empty Git repository in .git/
```

3. 建立一个本地仓库的新分支，方便后面的代码同步，命名为**hexo**，**注意这个项目的远程仓库将会包含两个分支，「hexo」这个分支的建立是为了存储项目源代码，后面生成的静态网页文件会存储到另一个分支「gh-pages」中**。

```shell
$ git checkout -b hexo
```



### 主题配置

使用现成的Hexo主题，需要将对应的项目拷贝到文件夹`themes/`中，这里很重要的一点是，**尽量不要采用直接的git clone方式**，因为这样相当于「博客源码的Git Repo包含了另一个主题源码的Git Repo」，涉及到子模块(Submodule)的问题，后续会出现一些commit冲突（这些冲突并非无法解决，也有相应的解决方式，具体可以google，只是相比之下会麻烦很多）。更优的方式是直接使用Git Submodules来引用主题的Repo，这里以一个Hexo主题[Frame](https://frame.zhangyongqi.com/about-cn/)为例：

```shell
$ git submodule add https://github.com/zoeingwingkei/frame.git themes/frame
```

这样，就在`themes/frame`中添加了Frame主题，现在查看`.gitmodules`文件，可以看见主题Repo现在已经作为一个submodule添加到了项目中：

```
[submodule "themes/frame"]
path = themes/frame
url = https://github.com/zoeingwingkei/frame.git
```

接下来，还需要在`_config.yml`文件中进行主题配置，修改其中的`theme`字段，将项目主题换成`Frame`：

```
theme: frame
```

这样主题配置就完成了，此时执行`hexo generate`，会发现Hexo能够在`public/`目录下生成相应的静态网页。执行`hexo server`，能够在本地对生成的网页进行预览。`Frame`主题的更多网页格式配置方式可以参考[Frame主页](https://frame.zhangyongqi.com/about-cn/)，这里仅对部署过程进行记录。

配置完主题之后，可以先提交一次代码：

```shell
$ git add .
$ git commit -m "Add Frame theme by submodule"
```



### 网站部署

至此已经实现了包含主题的静态网页在本地的生成，接下来的操作是把静态网页部署到Git Repo中，这里有两种方式，一种是**「一键部署」**，也是大部分人会采用的常见方式，操作步骤简单，但是每次都需要先生成静态页面再部署到GitHub上；另一种是通过**「Git Actions」**的操作部署到GitHub Pages，这种方式既可以将源码传入远程仓库进行备份和维护，更新博客时也只需要将新建的blog文件push到Git Repo中，类似使用`Jekyll`的方式。

#### 一键部署

一键部署的方法可以参考[官网说明](https://hexo.io/zh-cn/docs/one-command-deployment)：

1. 安装[hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)

```shell
$ npm install hexo-deployer-git --save
```

2. 在`_config.yml`中添加以下配置：

```yaml
deploy:
	type: git
	repo: https://github.com/<username>/<project>
	# example, https://github.com/hexojs/hexojs.github.io
	branch: gh-pages
```

其中，`repo`是所要上传的GitHub Repo名，即 ***username*\.github.io**，`branch`是远程Repo中的对应分支，这里命名为`gh-pages`；即部署生成的静态文件到远程仓库的**gh-pages**分支。

3. 执行`hexo clean && hexo deply`，进行项目部署。作业部署完成后，产生的页面会放在**gh-pages**分支。
4. 在Repo中前往 **Settings** > **Pages** > **Source**，并将branch改为`gh-pages`。
5. 等待上传完成，浏览`<GitHub 用户名>.github.io`检查网站是否正常运作。



#### Git Actions部署

Git Actions通过定义一系列[Workflow触发器](https://docs.github.com/en/actions/using-workflows)执行操作，详细介绍参见[GitHub Actions 官方指南](https://docs.github.com/en/actions)。

<details>
<summary><b>Workflow配置举例</b></summary>
```yaml
name: Greet Everyone
# This workflow is triggered on pushes to the repository.
on: [push]
jobs:
build:
# Job name is Greeting
name: Greeting
# This job runs on Linux
runs-on: ubuntu-latest
steps:
# This step uses GitHub's hello-world-javascript-action: https://github.com/actions/hello-world-javascript-action
- name: Hello world
uses: actions/hello-world-javascript-action@v1
with:
who-to-greet: 'Mona the Octocat'
id: hello
# This step prints an output (time) from the previous step's action.
- name: Echo the greeting's time
run: echo 'The time was ${{ steps.hello.outputs.time }}.'
```
</details>



以上面这个Workflow配置为例，`name`字段定义`Action`的名称，会显示在Repo的Actions界面；`on`字段定义触发条件，包括Pull Request / Push / 手动触发等；`jobs`定义Workflow的具体执行操作，包括`build / steps`字段。

`build`中`name`定义`jobs`的名称；`step`是一个数组，执行`build`的过程中，会按先后顺序触发`step`中的任务，每一项任务的定义可以采用`uses`方式定义，调用GitHub预置或第三方提供的函数来进行环境配置、下载源码等操作；也可以使用`run`定义的Shell脚本。

Hexo官网中提供了使用Git Actions进行部署的[教程](https://hexo.io/zh-cn/docs/github-pages)，其中提供了Workflow的构建代码，修改了其中一部分内容总结如下：

1. 使用`node --version`指令检查本地的Node.js版本并记录。
2. 在本地仓库中建立`.github/workflows/pages.yml`，并填入以下内容，相比官网教程有所修改，相关说明见代码中的注释部分：

```yaml
# .github/workflows/pages.yml
name: Pages

on:
	push:
		branches:
			- hexo # default branch 修改为使用的分支名

jobs:
	pages:
		runs-on: ubuntu-latest
		permissions:
			contents: write
		steps:
			# 将源码clone到运行环境中
			- uses: actions/checkout@v2
				with:
					submodules: 'true' # 这里因为使用了子模块，设置为true
			# 配置Node.js环境
			- name: Use Node.js 16.x
				uses: actions/setup-node@v2
				with:
					node-version: "16" # 这里需要修改为对应的Node.js版本
			- name: Cache NPM dependencies
				uses: actions/cache@v2
				with:
					path: node_modules
					key: ${{ runner.OS }}-npm-cache
					restore-keys: |
						${{ runner.OS }}-npm-cache
			# 安装依赖环境
			- name: Install Dependencies
				run: npm install
			- name: Build
				run: npm run build
			- name: Deploy
				uses: peaceiris/actions-gh-pages@v3
				with:
					github_token: ${{ secrets.GITHUB_TOKEN }}
					publish_dir: ./public
```

3. 修改完成workflow文件之后，提交一下代码：

```shell
$ git add .
$ git commit -m "Add Workflow File"
```

4. 使用之前创建的 ***username*\.github.io** 远程仓库，`username` 是 GitHub 上的用户名。将本地仓库与远程仓库建立连接：

```shell
$ git remote add origin https://github.com/<username>/<username>.github.io
```

5. 将Hexo文件夹中的文件push到远程Repo的对应分支，之前建立了本地的**hexo**分支，这里对应的远程分支也为**hexo**。

* 将本地**hexo**分支push到Github的远程**hexo**分支：

```shell
$ git push origin hexo
```

* 默认情况下`public/`不会被上传（也不该被上传），确保`.gitignore`文件中包含一行`public/`，整体文件夹结构应该与[范例仓库](https://github.com/hexojs/hexo-starter)大致相似。

6. 作业部署完成后，产生的页面会放在仓库的`gh-pages`分支。

7. 在Repo中前往 **Settings** > **Pages** > **Source**，并将branch改为`gh-pages`。（注意这里，如果选错分支会导致无法build GH Pages）

8. 前往*username*.github.io查看网站。



后续如果需要添加新的博文，通过`hexo new <blogname>`创建文件并编辑内容、提交变更后，直接push到远程仓库即能够自动生成静态文件，完成部署，自动实现变更效果。



### 参考资料

[Hexo官方教程](https://hexo.io/zh-cn/docs/)

[轮子再造 | 使用 GitHub Actions 自动部署 Hexo 博客 - 上篇](https://oreo.life/blog/2021-09-01-deploy-hexo-with-github-actions-1/)

[【Hexo博客搭建】将其部署到GitHub Pages（二）：如何初始化并部署？](https://developer.aliyun.com/article/789233)

[hexo 博客的神坑及本质原因](https://liguanghe.github.io/2017/11/06/blogRebuilt/)