---
title: hexo部署vscode
date: 2025-07-22T00:03:00+08:00
tags:
- hexo
index_img: https://qjw-00.oss-cn-guangzhou.aliyuncs.com/%E4%B8%AA%E4%BA%BA%E4%BD%BF%E7%94%A8%E5%AD%98%E5%82%A8/OIP-C.webp
---

# hexo部署vscode
> 今天，好朋友想要基于hexo来搭建一个博客，但是在部署的过程中出现了问题，一时间我也不知道如何解决，所以我就把我部署的经验和过程写下来，方便之后进行查阅和修改。

## 1.准备
### 1.1前提
当前PC环境中有Node和Git。版本可以参考Hexo文档。
文章中出现的yourusername为Github用户名，your-repo为仓库名。

### 1.2使用的软件
- [Hexo](https://hexo.io/zh-cn/)
- [Git](https://git-scm.com/)
- [GitHub](https://github.com/)
- [VSCode](https://code.visualstudio.com/)
- [Markdown](https://markdown.com.cn/)

### 1.3注意事项
- 确保你全程都是管理员权限
- 确保你可以链接到GitHub
- 确保你的GitHub用户名、邮箱、GitHub Pages的域名已经设置好
- 上面的软件下载好

## 2.Hexo安装
### 2.1 安装Hexo
安装脚手架，初始化hexo，这会新建blog文件夹，进入后安装依赖。
打开vscode，在终端输入以下命令
``` bash
npm install -g hexo-cli
hexo init blog（这一句可以省略blog）
cd blog（这一句可以省略）
npm install
```
### 2.2 初始化仓库
可以选择利用VSCode等软件直接对项目开源到github仓库。
也可以手动去github创建一个空仓库，然后手动在命令行中推送。
我是直接点击vscode最上面的输入框，直接推送到新仓库自动创建了。
``` bash
git init
git remote add origin https://github.com/yourusername/your-repo.git
git add .
git commit -m "Initial commit"
git push -u origin main
```

### 2.3 创建token
在个人设置中新增一个Personal access tokens。至少要包含repo权限，然后记住token。
这个token是给Github Action用的，Github Action会把Hexo编译部署到gh-pages分支。
具体可以查看网上的教程，这里我直接用人话

点击github的头像，点击settings，找到开发者设置，点击个人令牌访问，点击令牌（经典），然后点击生成新令牌，选择经典就好。保存好你的token，时间自己设置。

获取token之后，返回hexo的仓库，点击setting，找到secrets，点击Secrets and variables，点击action，点击new repository secret，输入token的名字（这个和后面保持一致），输入token的值，然后点击add secret。

### 2.4 修改_config.yml
在_config.yml中修改deploy字段。指示Hexo在deploy时的推送地址。
```yaml
deploy:
  type: git
  repo: https://github.com/yourusername/your-repo.git
  branch: gh-pages
```
## 3 配置Github Action工作流(如果不需要工作流，可以略过)
在.github文件夹下新增workflows文件夹，然后新增deploy.yml文件，内容如下。

里面有个node-version要和你本地的node一致。

步骤大致意思就是使用ubuntu-latest作为基础环境，然后安装各种依赖，随后hexo generate生成博客网站静态文件夹，
把这个文件夹推送到同一仓库的gh-pages分支。

```yml
name: Deploy Hexo to GitHub Pages

on:
  push:
    branches:
      - main  # 当推送到 main 分支时触发

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2
        with:
          submodules: false  # 禁用子模块检查

      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '18'

      - name: Install Dependencies
        run: npm install

      - name: Install Hexo Git Deployer
        run: |
          npm install hexo-deployer-git --save
          npm install hexo-cli -g

      - name: Clean and Generate Static Files
        run: |
          hexo clean
          hexo generate

      - name: Configure Git
        run: |
          git config --global user.name 'github-actions[bot]'
          git config --global user.email 'github-actions[bot]@users.noreply.github.com'

      - name: Deploy to GitHub Pages
        env:
          GH_TOKEN: ${{ secrets.GH_TOKEN }}
        run: |
          cd public/
          git init
          git add -A
          git commit -m "Create by workflows"
          git remote add origin https://${{ secrets.GH_TOKEN }}@github.com/yourusername/your-repo.git
          git push origin HEAD:gh-pages -f
```
### 3.1 推送验证（如果不需要工作流，可以略过）
把刚才更新的所有文件都推送一遍，github就会触发工作流，然后去网站看工作流运转的如何。
等一切运转完毕，就会发现仓库多出一个gh-pages分支。

### 3.2 配置Github Pages
在仓库settings中配置page来源为gh-pages分支即可。等待网站部署完毕，就可以看了。网站链接可以在settings的**GitHub Pages**看到，也可以去action里看到。

## 4. 修改Hexo主题样式
在hexo中找到你需要的主题，然后按照主题修改即可。
我放一个链接，这里可以找到很多主题。
[Hexo主题](https://hexo.io/themes/)


## 5. 配置添加文章
### 5.1推荐用vscod插件

**vscode-hexo和Hexo Utils**

### 5.2 创建文章

你可以执行下列命令来创建一篇新文章或者新的页面。
```
$ hexo new [layout] <title>
```
您可以在命令中指定文章的布局（layout），默认为 post，可以通过修改 _config.yml 中的 default_layout 参数来指定默认布局。

文章添加编辑后，现在只需要推送到仓库，那么github不仅会保存你的Hexo个人博客源码，还会自动更新个人博客静态页面到gh-pages，由此触发github-page功能来更新你的个人博客网站。


## 6. 遇到的问题和解决方法
### 6.1. 网站没有样式问题
在网站打开F12，发现css等样式资源无法加载，仔细查看报错原因和请求地址，发现并不是当前仓库。

缺少仓库地址，所以把请求地址复制一份，并在后面添加上仓库名即可，这需要修改_config.yml中修改url字段。yourusername似乎为小写。

推送后等待工作流执行，查看结果。

```yml
url: https://yourusername.github.io/your-repo
root: /your-repo/
```
### 6.2. 图片不显示
在_config.yml中设置
```yml
post_asset_folder: true
```
意思是每个md博文会单独配套一个同名文件夹，用来存放图片。形如
```
source/_posts/
├── my-new-post.md
└── my-new-post/
    └── example.jpg
```
hexo提供三种语法

```
{% asset_path slug %}
{% asset_img slug [title] %}
{% asset_link slug [title] %}
```
那么在md中可以这样引用图片

```
{% asset_img example.jpg This is an example image %}
```
具体的可以看这个[Hexo文档](https://hexo.io/zh-cn/docs/index.html)

这样一来，部署的时候图片就不会不显示了，但是有个新的问题，
我在本地编辑md的时候无法预览图片怎么办。

建议用VSCode下载插件vscode-hexo和Hexo Utils，随后在左边栏目就可以看到新Hexo Utils的新菜单，只要你的VSCode当前打开的文件夹是hexo的根目录，那么插件就会自动识别到，当你对md文件使用“侧边预览”时，图片就正常显示了。
