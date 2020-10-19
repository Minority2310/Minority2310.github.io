---
title: 使用Hexo框架在GitHub上部署博客，发布文章
date: 2020-01-18 13:12:00
categories: 
- 学习
tags: 
- hexo
- blog
---

# 准备阶段

## 环境

### GitHub

GitHub网页版和桌面版均可使用

### Hexo

安装需要Node.js和Git环境

这里，我们要区分清楚git与GitHub。git是一个版本控制的工具，而GitHub有点类似于远程仓库，用于存放用git管理的各种项目。

下面提供相关的官方版本地址，安装教程去网上搜一下就有很多而且基本上都是默认一直下一步。

- Node 官方版本安装：https://nodejs.org/en/
- Git官方版本安装：https://git-scm.com/download/win
- GitHub桌面版安装：https://desktop.github.com/

### Git配置

当安装完Git应该做的第一件事情就是设置用户名称和邮件地址。这样做很重要，因为每一个Git的提交都会使用这些信息，并且它会写入你的每一次提交中不可更改

GitBash：

```bash
$ git config --global user.name "username"
$ git config --global user.email "username@example.com"
```

对于user.email，因为在GitHub的commits信息上是可见的，所以如果你不想让人知道你的email，可以Keeping your email address private:

1. 在GitHub右上方点击你的头像，选择”Settings”；
2. 在右边的”Personal settings”侧边栏选择”Emails”；
3. 选择”Keep my email address private”。

这样，你就可以使用如下格式的email进行配置：

```bash
$ git config --global user.email "username@users.noreply.github.com"
```

### Github 配置

#### 创建仓库 new repository

**在自己的GitHub账号下创建一个新的仓库，仓库名必须为**username.github.io**（username 是你的账号名)。**

在这里要知道，GitHub Pages有两种类型：User/Organization Pages 和 Project Pages，而我所使用的是User Pages。

简单来说，User Pages 与 Project Pages的区别是：

1. User Pages 是用来展示用户的，而 Project Pages 是用来展示项目的。
2. 用于存放 User Pages 的仓库必须使用username.github.io的命名规则，而 Project Pages 则没有特殊的要求。
3. User Pages 将使用仓库的 master 分支，而 Project Pages 将使用 gh-pages 分支。
4. User Pages 通过 http(s)://username.github.io 进行访问，而 Projects Pages通过 http(s)://username.github.io/projectname 进行访问。

> 注意：
>
> 勾选下图中的选项后可以直接创建一个新的远程分支。如果未勾选，需要在远程仓库创建一个README.md文件后才能创建一个新的远程分支。

![](https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/01.jpg)

### 创建分支

创建新代码仓库时，默认是 master 分支，但是这里需要2个分支，master分支存储HTML静态网页代码(访问博客展示的html页面)，source分支存储本地上传的博客网站源码。

在GitHub上创建一个新的远程分支source，将source设置为默认分支。

![](https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/02.jpg)

### clone仓库

在本地创建一个My Blog目录

在此目录下clone远程仓库(username.github.io)，clone完成后的目录名即为远程仓库名

```bash
$ git clone git@github.com:xxx/xxx.git
```

![](https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/03.jpg)



### 配置SSH key

为什么要配置这个呢？因为你提交代码肯定要拥有你的github权限才可以，但是直接使用用户名和密码太不安全了，所以我们使用SSH key来解决本地和服务器的连接问题。

1. 检查电脑是否已经有SSH KEYS。
   `$ ls -al ~/.ssh`
   默认情况下，public keys的文件名是以下的格式之一：id_dsa.pub、id_ecdsa.pub、id_ed25519.pub、id_rsa.pub。因此，如果列出的文件有public和private钥匙对（例如id_ras.pub和id_rsa），证明已存在SSH keys。如果提示：No such file or directory 说明你是第一次使用git。
2. 如果没有SSH KEY，则生成新的SSH KEY。
   ```ssh-keygen -t rsa -C "your_email@example.com"```
   然后连续3次回车，最终会生成一个文件在用户目录下，打开用户目录，找到```.ssh\id_rsa.pub```文件，记事本打开并复制里面的内容，打开你的github主页，进入个人设置 -> SSH and GPG keys -> New SSH key：

![](https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/04.jpg)

将刚复制的内容粘贴到key那里，title随便填，保存。

3. 测试是否成功
   ```$ ssh -T git@github.com```

如果提示```Are you sure you want to continue connecting (yes/no)?```，输入yes，然后会看到：

> Hi xxx! You've successfully authenticated, but GitHub does not provide shell access.

看到这个信息说明SSH已配置成功！

此时你还需要配置：

```bash
$ git config --global user.name "liuxianan" 	#你的github用户名，非昵称
$ git config --global user.email  "xxx@qq.com"	#填写你的github注册邮箱
```



# 部署阶段

## Hexo博客创建

> 注意：
>
> 1. 很多命令既可以用Windows的cmd来完成，也可以使用git bash来完成，但是部分命令会有一些问题，为避免不必要的问题，建议全部使用git bash来执行。
>
> 2. hexo不同版本差别比较大，网上很多文章的配置信息都是基于2.x的，所以注意不要被误导。
> 3. hexo有2种config.yml文件，一种是根目录下的全局的config.yml，称为站点配置文件；一种是各个theme下的，称为主题配置文件。在配置文件中修改时，冒号后面必须有一个空格，否则可能会出问题。

### 步骤

```bash
# npm换为淘宝服务器的cnpm
npm config set registry https://registry.npm.taobao.org
# 然后安装cnpm
npm install -g cnpm --registry=https://registry.npm.taobao.org
```



在clone的远程仓库(username.github.io)目录下打开Git Bash

```bash
cnpm install hexo-cli -g        # hexo安装
hexo init <博客名>              # 初始化博客并命名
cd <博客名>                     
cnpm install                    # 安装依赖
hexo generate(g)               # 生成静态页面(markdowm文件->html文件)  
hexo server(s)                 # 网站本地预览
```


## Matery主题

你要是愿意用自带的 langscape 主题，可忽略此步骤。


### hexo-theme-matery主题设定

- 选择风格 Scheme
- 设置 界面语言
- 设置 菜单
- 设置 侧边栏
- 设置 头像
- 设置 作者昵称
- 设置 站点描述

> 这是一个采用`Material Design`和响应式设计的 Hexo 博客主题。



###  特性

- 简单漂亮，文章内容美观易读
- [Material Design](https://material.io/) 设计
- 响应式设计，博客在桌面端、平板、手机等设备上均能很好的展现
- 首页轮播文章及每天动态切换 `Banner` 图片
- 瀑布流式的博客文章列表（文章无特色图片时会有 `24` 张漂亮的图片代替）
- 时间轴式的归档页
- **词云**的标签页和**雷达图**的分类页
- 丰富的关于我页面（包括关于我、文章统计图、我的项目、我的技能、相册等）
- 可自定义的数据的友情链接页面
- 支持文章置顶和文章打赏
- 支持 `MathJax`
- `TOC` 目录
- 可设置复制文章内容时追加版权信息
- 可设置阅读文章时做密码验证
- [Gitalk](https://gitalk.github.io/)、[Gitment](https://imsun.github.io/gitment/)、[Valine](https://valine.js.org/) 和 [Disqus](https://disqus.com/) 评论模块（推荐使用 `Gitalk`）
- 集成了[不蒜子统计](http://busuanzi.ibruce.info/)、谷歌分析（`Google Analytics`）和文章字数统计等功能
- 支持在首页的音乐播放和视频播放功能



###  下载

点击 [这里](https://codeload.github.com/blinkfox/hexo-theme-matery/zip/master) 下载 `master` 分支的最新稳定版的代码，解压缩后，将 `hexo-theme-matery` 的文件夹复制到你 Hexo 的 `themes` 文件夹中即可。

当然你也可以在你的 `themes` 文件夹下使用 `git clone` 命令来下载:

```bash
git clone https://github.com/blinkfox/hexo-theme-matery.git
```



###  配置
---
#### 切换主题

修改 Hexo 根目录下的 `_config.yml` 的 `theme` 的值：`theme: hexo-theme-matery`

> `_config.yml` 文件的其它修改建议:
>
> - 请修改` _config.yml`的 `url` 的值为你的网站主 `URL`（如：`http://xxx.github.io`）。
> - 建议修改两个 `per_page` 的分页条数值为 `6` 的倍数，如：`12`、`18` 等，这样文章列表在各个屏幕下都能较好的显示。
> - 如果你是中文用户，则建议修改 `language` 的值为 `zh-CN`。



#### 新建分类 categories 页

`categories` 页是用来展示所有分类的页面，如果在你的博客 `source` 目录下还没有 `categories/index.md` 文件，那么你就需要新建一个，命令如下：

```bash
hexo new page "categories"
```

编辑你刚刚新建的页面文件 `/source/categories/index.md`，至少需要以下内容：

```yaml
---
title: categories
date: 2018-09-30 17:25:30
type: "categories"
layout: "categories"
---
```



#### 新建标签 tags 页

`tags` 页是用来展示所有标签的页面，如果在你的博客 `source` 目录下还没有 `tags/index.md` 文件，那么你就需要新建一个，命令如下：

```bash
hexo new page "tags"
```

编辑你刚刚新建的页面文件 `/source/tags/index.md`，至少需要以下内容：

```yaml
---
title: tags
date: 2018-09-30 18:23:38
type: "tags"
layout: "tags"
---
```



#### 新建关于我 about 页

`about` 页是用来展示**关于我和我的博客**信息的页面，如果在你的博客 `source` 目录下还没有 `about/index.md` 文件，那么你就需要新建一个，命令如下：

```bash
hexo new page "about"
```

编辑你刚刚新建的页面文件 `/source/about/index.md`，至少需要以下内容：

```yaml
---
title: about
date: 2018-09-30 17:25:30
type: "about"
layout: "about"
---
```



#### 新建友情连接 friends 页（可选的）

`friends` 页是用来展示**友情连接**信息的页面，如果在你的博客 `source` 目录下还没有 `friends/index.md` 文件，那么你就需要新建一个，命令如下：

```bash
hexo new page "friends"
```

编辑你刚刚新建的页面文件 `/source/friends/index.md`，至少需要以下内容：

```yaml
---
title: friends
date: 2018-12-12 21:25:30
type: "friends"
layout: "friends"
---
```

同时，在你的博客 `source` 目录下新建 `_data` 目录，在 `_data` 目录中新建 `friends.json` 文件，文件内容如下所示：

```json
[{
    "avatar": "http://image.luokangyuan.com/1_qq_27922023.jpg",
    "name": "码酱",
    "introduction": "我不是大佬，只是在追寻大佬的脚步",
    "url": "http://luokangyuan.com/",
    "title": "前去学习"
}, {
    "avatar": "http://image.luokangyuan.com/4027734.jpeg",
    "name": "闪烁之狐",
    "introduction": "编程界大佬，技术牛，人还特别好，不懂的都可以请教大佬",
    "url": "https://blinkfox.github.io/",
    "title": "前去学习"
}, {
    "avatar": "http://image.luokangyuan.com/avatar.jpg",
    "name": "ja_rome",
    "introduction": "平凡的脚步也可以走出伟大的行程",
    "url": "ttps://me.csdn.net/jlh912008548",
    "title": "前去学习"
}]
```



#### 代码高亮

由于 Hexo 自带的代码高亮主题显示不好看，所以主题中使用到了 [hexo-prism-plugin](https://github.com/ele828/hexo-prism-plugin) 的 Hexo 插件来做代码高亮，安装命令如下：

```bash
npm i -S hexo-prism-plugin
```

然后，修改 Hexo 根目录下 `_config.yml` 文件中 `highlight.enable` 的值为 `false`，并新增 `prism` 插件相关的配置，主要配置如下：

```yaml
highlight:
  enable: false

prism_plugin:
  mode: 'preprocess'    # realtime/preprocess
  theme: 'tomorrow'
  line_number: false    # default false
  custom_css:
```



#### 搜索

本主题中还使用到了 [hexo-generator-search](https://github.com/wzpan/hexo-generator-search) 的 Hexo 插件来做内容搜索，安装命令如下：

```bash
npm install hexo-generator-search --save
```

在 Hexo 根目录下的 `_config.yml` 文件中，新增以下的配置项：

```yaml
search:
  path: search.xml
  field: post
```



#### 中文链接转拼音（可选的）

如果你的文章名称是中文的，那么 Hexo 默认生成的永久链接也会有中文，这样不利于 `SEO`，且 `gitment` 评论对中文链接也不支持。我们可以用 [hexo-permalink-pinyin](https://github.com/viko16/hexo-permalink-pinyin) Hexo 插件使在生成文章时生成中文拼音的永久链接。

安装命令如下：

```bash
npm i hexo-permalink-pinyin --save
```

在 Hexo 根目录下的 `_config.yml` 文件中，新增以下的配置项：

```yml
permalink_pinyin:
  enable: true
  separator: '-' # default: '-'
```

> **注**：除了此插件外，[hexo-abbrlink](https://github.com/rozbo/hexo-abbrlink) 插件也可以生成非中文的链接。



#### 文章字数统计插件（可选的）

如果你想要在文章中显示文章字数、阅读时长信息，可以安装 [hexo-wordcount](https://github.com/willin/hexo-wordcount)插件。

安装命令如下：

```bash
npm i --save hexo-wordcount
```

然后只需在本主题下的 `_config.yml` 文件中，激活以下配置项即可：

```yaml
wordCount:
  enable: false # 将这个值设置为 true 即可.
  postWordCount: true
  min2read: true
  totalCount: true
```



#### 添加 RSS 订阅支持（可选的）

本主题中还使用到了 [hexo-generator-feed](https://github.com/hexojs/hexo-generator-feed) 的 Hexo 插件来做 `RSS`，安装命令如下：

```bash
npm install hexo-generator-feed --save
```

在 Hexo 根目录下的 `_config.yml` 文件中，新增以下的配置项：

```yaml
feed:
  type: atom
  path: atom.xml
  limit: 20
  hub:
  content:
  content_limit: 140
  content_limit_delim: ' '
  order_by: -date
```

执行 `hexo clean && hexo g` 重新生成博客文件，然后在 `public` 文件夹中即可看到 `atom.xml` 文件，说明你已经安装成功了。



#### 修改页脚

页脚信息可能需要做定制化修改，而且它不便于做成配置信息，所以可能需要你自己去再修改和加工。修改的地方在主题文件的 `/layout/_partial/footer.ejs` 文件中，包括站点、使用的主题、访问量等。



#### 修改社交链接

在主题的 `_config.yml` 文件中，默认支持 `QQ`、`GitHub` 和邮箱的配置，你可以在主题文件的 `/layout/_partial/social-link.ejs` 文件中，新增、修改你需要的社交链接地址，增加链接可参考如下代码：

```html
<a href="https://github.com/blinkfox" class="tooltipped" target="_blank" data-tooltip="访问我的GitHub" data-position="top" data-delay="50">
    <i class="fa fa-github"></i>
</a>
```

其中，社交图标（如：`fa-github`）你可以在 [Font Awesome](https://fontawesome.com/icons) 中搜索找到。以下是常用社交图标的标识，供你参考：

- Facebook: `fa-facebook`
- Twitter: `fa-twitter`
- Google-plus: `fa-google-plus`
- Linkedin: `fa-linkedin`
- Tumblr: `fa-tumblr`
- Medium: `fa-medium`
- Slack: `fa-slack`
- 新浪微博: `fa-weibo`
- 微信: `fa-wechat`
- QQ: `fa-qq`

> **注意**: 本主题中使用的 `Font Awesome` 版本为 `4.7.0`。



#### 修改打赏的二维码图片

在主题文件的 `source/medias/reward` 文件中，你可以替换成你的的微信和支付宝的打赏二维码图片。



#### 音乐播放器（可选的）

要支持音乐播放，就必须开启音乐的播放配置和音乐数据的文件。

首先，在你的博客 `source` 目录下的 `_data` 目录（没有的话就新建一个）中新建 `musics.json` 文件，文件内容如下所示：

```json
[{
    "name": "五月雨变奏电音",
    "artist": "AnimeVibe",
    "url": "http://xxx.com/music1.mp3",
    "cover": "http://xxx.com/music-cover1.png"
}, {
    "name": "Take me hand",
    "artist": "DAISHI DANCE,Cecile Corbel",
    "url": "/medias/music/music2.mp3",
    "cover": "/medias/music/cover2.png"
}, {
    "name": "Shape of You",
    "artist": "J.Fla",
    "url": "http://xxx.com/music3.mp3",
    "cover": "http://xxx.com/music-cover3.png"
}]
```

> **注**：以上 JSON 中的属性：`name`、`artist`、`url`、`cover` 分别表示音乐的名称、作者、音乐文件地址、音乐封面。

然后，在主题的 `_config.yml` 配置文件中激活配置即可：

```yaml
# 是否在首页显示音乐.
music:
  enable: true
  showTitle: false
  title: 听听音乐
  fixed: false # 是否开启吸底模式
  autoplay: false # 是否自动播放
  theme: '#42b983'
  loop: 'all' # 音频循环播放, 可选值: 'all', 'one', 'none'
  order: 'list' # 音频循环顺序, 可选值: 'list', 'random'
  preload: 'auto' # 预加载，可选值: 'none', 'metadata', 'auto'
  volume: 0.7 # 默认音量，请注意播放器会记忆用户设置，用户手动设置音量后默认音量即失效
  listFolded: false # 列表默认折叠
  listMaxHeight: # 列表最大高度
```



### 自定义修改

在本主题的 `_config.yml` 中可以修改部分自定义信息，有以下几个部分：

- 菜单
- 我的梦想
- 首页的音乐播放器和视频播放器配置
- 是否显示推荐文章名称和按钮配置
- `favicon` 和 `Logo`(博客标题栏的图标和博客的图标)
- 个人信息
- TOC 目录
  - 默认从h2开始显示目录
- 文章打赏信息
- 复制文章内容时追加版权信息
- MathJax
- 文章字数统计、阅读时长
- 点击页面的’爱心’效果
- 我的项目
- 我的技能
- 我的相册
- `Gitalk`、`Gitment`、`Valine` 和 `disqus` 评论配置
- [不蒜子统计](http://busuanzi.ibruce.info/)和谷歌分析（`Google Analytics`）
- 默认特色图的集合。当文章没有设置特色图时，本主题会根据文章标题的 `hashcode` 值取余，来选择展示对应的特色图

如果本主题中的诸多功能和主题色彩你不满意，可以在主题中自定义修改，很多更自由的功能和细节点的修改难以在主题的 `_config.yml` 中完成，需要修改源代码才来完成。以下列出了可能对你有用的地方：



#### 修改主题颜色

在主题文件的 `/source/css/matery.css` 文件中，搜索 `.bg-color` 来修改背景颜色：

```css
/* 整体背景颜色，包括导航、移动端的导航、页尾、标签页等的背景颜色. */
.bg-color {
    background-image: linear-gradient(to right, #4cbf30 0%, #0f9d58 100%);
}

@-webkit-keyframes rainbow {
   /* 动态切换背景颜色. */
}

@keyframes rainbow {
    /* 动态切换背景颜色. */
}
```



#### 修改 banner 图和文章特色图

你可以直接在 `/source/medias/banner` 文件夹中更换你喜欢的 `banner` 图片，主题代码中是每天动态切换一张，只需 `7` 张即可。如果你会 `JavaScript` 代码，可以修改成你自己喜欢切换逻辑，如：随机切换等，`banner` 切换的代码位置在 `/layout/_partial/bg-cover-content.ejs` 文件的 `<script></script>` 代码中：

```javascript
$('.bg-cover').css('background-image', 'url(/medias/banner/' + new Date().getDay() + '.jpg)');
```

在 `/source/medias/featureimages` 文件夹中默认有 24 张特色图片，你可以再增加或者减少，并需要在 `_config.yml` 做同步修改。



#### 增加在文章页显示作者名的功能

原来只会在文章卡片上显示作者名，在`hexo-theme-matery/layout/_partial/post-detail.ejs`第37行左右，**增加**下列代码使得文章页可以显示：

```jsx
<div class="text-color" align=center style="font-size:large">
    <% if (page.author && page.author.length > 0) { %>
        <i class="far fa-square"></i> <%- page.author %>
    <% } else { %>
        <i class="far fa-square"></i> <%- config.author %>
    <% } %>
</div>
```

## 发布源码到GitHub

**将上面Hexo新建的项目(<博客名>)里的所有文件复制到 username.github.io目录里(复制后博客目录可以直接删除)** 

1. 首先，SSH key肯定要配置好。
2. 其次，配置站点```_config.yml```中有关deploy的部分。
默认生成的```_config.yml```：

```swift
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type:
```

修改后的`_config.yml`：

```swift
deploy:
  type: git
  #对应仓库的SSH地址（可以在GitHub对应的仓库中复制）
  repo: git@github.com:xxx/xxx.git 
  #master分支用于发布生成的html页面
  branch: master 
```

为了能够使Hexo部署到GitHub上，需要安装一个插件：
`$ npm install hexo-deployer-git --save`

提交源码到source分支：

```bash
git add * 					# 将所有修改添加到暂存区
git commit -m "提交备注"	
git push origin <代码分支>
```

用`hexo deploy`命令发布生成后的HTML代码到 master 分支上。
执行指令即可完成部署(博客目录下)：

``` hexo g -d```

之后，可以通过在浏览器键入：username.github.io进行浏览



## Github 绑定域名

### 配置域名解析设置

创建一个A记录和一个CNAME记录

![](https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/05.jpg)

在仓库的Setting中填写个人域名

![](https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/06.jpg)

**完成后即可通过访问个人域名访问博客**





# 写作阶段

## 创建文章

```bash
hexo new <post> "文章名称"			# 文章默认类型为post，可不写
```



## 文章基本设置

### Front-matter 

Front-matter 是文件最上方以 `---` 分隔的YAML或JSON块，用于指定个别文件的变量，会有一些预先定义的参数

#### 示例

```yaml
---
title: typora-vue-theme主题介绍
date: 2019-09-17 09:25:00
author: xx
img: /source/images/xxx.jpg
top: true
cover: true
coverImg: /images/1.jpg
password: 8d969eef6ecad3c29a3a629280e686cf0c3f5d5a86aff3ca12020c923adc6c92
toc: false
mathjax: false
summary: 这是你自定义的文章摘要内容，如果这个属性有值，文章卡片摘要就显示这段文字，否则程序会自动截取文章的部分内容作为摘要
categories: 
- Markdown
tags: 
- Typora
- Markdown
---
```



### 添加预定义参数到默认文章类型中

> 注：`tags`和`categories`后面不用加任何值，如果加了新建文章会出错

`scaffolds/draft.md`草稿类型

```bash
---
title: {{ title }}
tags: 
---
```

`scaffolds/post.md`默认类型

```bash
---
title: {{ title }}
date: {{ date }}
tags: 
categories: 
---
```



### 图床

使用图床保存图片，保证图片在互联网上的正常访问

#### 开通阿里云对象存储OSS服务

进入阿里云官网搜索对象存储OSS，点击折扣套餐

![](https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/oss.png)

一年也就9块钱

在控制台点击新建Bucket，按图中选项选择

<img src="https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/Snipaste_2019-10-02_20-07-29.jpg"  />

#### 使用PicGo实现快速上传图片

PicGo是GitHub的一个开源项目

[PicGo](https://github.com/Molunerfinn/PicGo)仓库

windows版[下载](https://github.com/Molunerfinn/PicGo/releases)红色箭头指向的文件

![](https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/Snipaste_2019-10-02_20-58-40.jpg)

安装完成后打开软件到图床设置界面

![](https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/Snipaste_2019-10-02_20-37-36.jpg)

* 设定KeyId和设定KeySecret

点击进入新建Access Key，得到KeyId和KeySecret

![](https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/Snipaste_2019-10-02_20-32-19.jpg)

* 设定存储空间名：创建Bucket时的空间名
* 确认存储区域：到OSS控制台的访问域名中查找，图中为oss-cn-beijing

![](https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/Snipaste_2019-10-02_21-08-36.jpg)

* 存储路径是存储图片的位置，要求以/结尾
* 自定义域名可以不写

将图片拖拽到图片中的虚线框中即可上传。上传完成后会以下方选定的链接格式复制到剪贴板中，直接粘贴到文档中即可

![](https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/ScreenClip [2].png)

## 写作

### 常用Markdown语法(Typora)：

#### 标题

```markdown
//后有空格
# 一级标题		
## 二级标题
```

#### 列表

```markdown
1. 有序列表
* 无序列表
```

#### 引用

```markdown
> 引用的文字
>
> ........
```

#### 分割线、删除线

```markdown
--- 分割线
~~删除线~~
```

#### 粗体、斜体

```markdown
**粗体**
*斜体*
```

#### 图片与链接

`[]`表示图片名或链接名，`()`表示图片地址或链接地址

```markdown
![]()		图片		
[]()	    链接
```

#### 文字背景填充

用于个别突出某些文字

```markdown
`文字`		反引号
```

#### 代码块

```markdown
​```<语言名>   3个反引号
代码
​```
```

#### 上下标

```markdown
文字<sup>上标</sup>
文字<sub>下标</sub>
```

#### 内联HTML

```html
<span style='color:red'>This is red</span>		改变字体颜色
<kbd>Ctrl</kbd>+<kbd>F9</kbd>		键盘输入格式
```



# 管理阶段

## 日常修改

在本地修改、创建文章后，按进行下面的命令进行操作

```bash
git add . 
git commit -m ""
git push origin <分支名称>
hexo g -d
```



## 本地资料丢失

  当重装电脑之后，或者想在其他电脑上修改博客，可以使用下列步骤：

1. clone项目：github桌面端
     或 使用 `git clone git@github.com:xx/xx.github.io.git`拷贝仓库(默认分支为source)
2. 在本地新拷贝的 woaiwojia321314.github.io 文件夹下通过 Git bash 依次执行下列指令：

```bash
npm install hexo
npm install
npm install hexo-deployer-git
```



## 保留文件

* `CNAME`、`README.md`、`favicon.ico`等文件放在`source`目录下
* `CNAME`内容为个人域名(该文件没有后缀)
* `README.md`文件名需要添加在站点配置文件`_config.yml`的`skip_render`选项中，防止被渲染成html文件
* 项目根目录：`gitignore`文件内容：

```swift
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
```



# 附录

## hexo命令

### 常见命令：

```bash
hexo new "postName" 		# 新建文章
hexo new page "pageName" 	# 新建页面
hexo generate 				# 生成静态页面至public目录
hexo server 				# 开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy 				# 部署到GitHub
hexo help  					# 查看帮助
hexo version  				# 查看Hexo的版本
```

### 缩写：

```bash
hexo n == hexo new
hexo g == hexo generate
hexo s == hexo server
hexo d == hexo deploy
```

### 组合命令：

```bash
hexo s -g 		# 生成并本地预览
hexo d -g 		# 生成并上传
```



