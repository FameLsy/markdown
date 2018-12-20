---
title: hexo笔记
tags:
- hexo
- next
categories:
- blog
---
[hexo官网文档](https://hexo.io/zh-cn/docs/)
___
# 安装Hexo
## 安装前提
安装hexo前，还需还装**Node.js**和**Git***两个程序
## 安装 Git
[git官网](https://git-scm.com/)  
Linux (Ubuntu, Debian)
```Linux
sudo apt-get install git
```
Linux (Fedora, Red Hat, CentOS)
```Linux
sudo yum install gi
```
## 安装Node.js
[Node.js官网](https://nodejs.org/en/)  
安装**Node.js**,推荐使用**nvm**方式安装  
下载并安装**nvm**  
[nvm  github地址](https://github.com/creationix/nvm)  
CURL方式
```Linux
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash

```
Wget方式
```Linux
wget -qO- https://raw.github.com/creationix/nvm/v0.33.11/install.sh | sh
```
安装完成后,重新打开一个终端.
开始安装**Node.js**
```Linux
nvm install stable
```
## 安装**Hexo**
使用**nmp**命令进行安装
```Linux
npm install -g hexo-cli
```
# 建站
## 建立博客站

先初始化一个站，**folder**为自己填写的站名
```linux
hexo init <folder>
```
完成后，在用户目录下，多出了一个以**folder** 为名的文件夹  
进入该文件夹
```linux
cd <folder>
```
该文件夹下，有如下文件

|文件名|描述|
|--|--|
|_config.yml|存有网站的配置信息，大部分配置都在这里修改|
|package.json|应用程序的信息|
|scaffolds|模版文件夹。当新建文章时，Hexo 会根据 scaffold 来建立文件|
|source|资源文件夹是存放用户资源的地方。开头命名为 _ (下划线)的文件 / 文件夹和隐藏的文件将会被忽略。Markdown 和 HTML 文件会被解析并放到 public 文件夹，而其他文件会被拷贝过去|
|themes|主题 文件夹。Hexo 会根据主题来生成静态页面|

安装相关依赖
```npm
npm install
```
此时基本可以运行，首先生成静态文件
```hexo
hexo d
```
然后运行
```hexo 
hexo s
```
运行正确的话，系统会输出
```
 Hexo is running at http://localhost:4000
```
在自己的浏览器打开访问,就可以浏览到一个什么都没有的博客了。
# 配置
##  网站配置
打开配置文件,接下来我将以**blog**为设置的站文件夹
```liunx
sudo vim ~/blog/_config.yml
```
配置网站,关键字**Site**

|参数|描述|
|--|--|
|title|网站标题|
|subtitle|网站副标题|
|description|网站描述|
|author|您的名字|
|language|网站使用的语言|
|timezone|网站时区。Hexo 默认使用您电脑的时区。时区列表。比如说：America/New_York,Japan, 和 UTC 。|

其中，description主要用于SEO，告诉搜索引擎一个关于您站点的简单描述，通常建议在其中包含您网站的关键词。author参数用于主题显示文章的作者。

## 网址配置
关键字，**Url**

|参数|描述|默认值|
|--|--|--|
|url|网址||
|root|网站根目录||
|permalink|文章的永久链接格式|:year/:month/:day/:title/|
|permalink_defaults|永久链接中各部分的默认值| |

## 配置目录
关键字，**Directory**

|参数|描述|默认值
|--|--|--|
|source_dir|资源文件夹，这个文件夹用来存放内容。|source|
|public_dir|公共文件夹，这个文件夹用于存放生成的站点文件。|public
|tag_dir|标签文件夹|tags|
|archive_dir|归档文件夹|archives|
|category_dir|分类文件夹|categories|
|code_dir|Include code 文件夹|downloads/code|
|i18n_dir|国际化（i18n）文件夹|:lang|
|skip_render|跳过指定文件的渲染，您可使用 glob 表达式来匹配路径。| |

## 配置

未完待续
___
# 更新博客文件

```linux
# 进入博客文件文件夹
cd ~/blog/source/_posts
# 更新博客
hexo clean && hexo g && hexo d
```
# 挂载到Github
## **Github** 操作
## 本地**Hexo**操作

# 个性化**Hexo**

## 自定义CSS样式
创建一个.styl文件
```linux
# 内容就放css的内容即可
vim ~/blog/themes/next/source/css/_custom/mystyle.styl
```
打开custom.styl文件，并将刚刚创建的.styl文件导入
```linux
#输入@impolt "mystyle.styl"导入
sudo vim ~/blog/themes/next/source/css/_custom/custom.styl
```
## 自定义JS代码  
首先,如果根目录没有*scripts*目录，就创建一个。  
然后，创建js文件  
```linux
# 直接创建并打开
vim ~/blog/scripts/myjs.js
```
hexo会自动加载该目录下的js文件

## 修改代码块
首先,如果根目录没有*scripts*目录，就创建一个。  
然后，创建js文件  
```linux
# 直接创建并打开
vim ~/blog/scripts/codeblock.js
```
复制以下内容进去
```js
var attributes = [
  'autocomplete="off"',
  'autocorrect="off"',
  'autocapitalize="off"',
  'spellcheck="false"',
  'contenteditable="true"'
]

var attributesStr = attributes.join(' ')

hexo.extend.filter.register('after_post_render', function (data) {
  while (/<figure class="highlight ([a-zA-Z]+)">.*?<\/figure>/.test(data.content)) {
    data.content = data.content.replace(/<figure class="highlight ([a-zA-Z]+)">.*?<\/figure>/, function () {
      var language = RegExp.$1 || 'plain'
      var lastMatch = RegExp.lastMatch
      lastMatch = lastMatch.replace(/<figure class="highlight /, '<figure class="iseeu highlight /')
      return '<div class="highlight-wrap"' + attributesStr + 'data-rel="' + language.toUpperCase() + '">' + lastMatch + '</div>'
    })
  }
  return data
})
```
然后，创建一个.style文件
```linux
vim ~/blog/themes/next/source/css/_custom/mystyle.styl
```
加入内容
```css
.highlight {
        margin: 38px 0 0 0 !important;
}

.highlight-wrap[data-rel] {
  position: relative;
  overflow: hidden;
  border-radius: 5px;
  box-shadow: 0 10px 30px 0px rgba(0, 0, 0, 0.4);
  margin: 35px 0;
  ::-webkit-scrollbar {
    height: 10px;
  }

  ::-webkit-scrollbar-track {
    -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
    border-radius: 10px;
  }

  ::-webkit-scrollbar-thumb {
    border-radius: 10px;
    -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.5);
  }
  &::before {
    color: white;
    content: attr(data-rel);
    height: 38px;
    line-height: 38px;
    background: #21252b;
    color: #fff;
    font-size: 16px;
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    font-family: 'Source Sans Pro', sans-serif;
    font-weight: bold;
    padding: 0px 80px;
    text-indent: 15px;
    float: left;
  }
  &::after {
    content: ' ';
    position: absolute;
    -webkit-border-radius: 50%;
    border-radius: 50%;
    background: #fc625d;
    width: 12px;
    height: 12px;
    top: 0;
    left: 20px;
    margin-top: 13px;
    -webkit-box-shadow: 20px 0px #fdbc40, 40px 0px #35cd4b;
    box-shadow: 20px 0px #fdbc40, 40px 0px #35cd4b;
    z-index: 3;
  }
}
```
打开custom.styl文件，并将刚刚创建的.styl文件导入
```linux
#输入@impolt "mystyle.styl"导入
sudo vim ~/blog/themes/next/source/css/_custom/custom.styl
```