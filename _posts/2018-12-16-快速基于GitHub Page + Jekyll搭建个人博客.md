---
layout:          post
title:           快速基于GitHub Page + Jekyll搭建个人博客
subtitle:        利用现成可拉取的模版及可自定义个性化风格
date:            2018-12-16
author:          rookflying
head-img:        img/post-bg-ios9-web.jpg
catalog:         true
tags:
    - Jekyll
    - github
---

# 1. 环境搭建

## 1.1 Mac or Linux

Mac需先安装Xcode。

安装rvm，rvm是个ruby版本管理器，用来装各种版本的ruby的。

```
curl -L https://get.rvm.io | bash -s stable
```

安装好后会提示 source ~/.bash_profile 将rvm添加到环境变量中。也可以关闭，重新打开一个terminal，新开的terminal会更新环境变量。

接着使用　rvm use 来使用具体版本的ruby。

```
rvm use 2.4.1
```

如果发现没有可用的指定版本的ruby，会提示你安装，按着提示命令敲就好。安装完之后会自动切换到该版本的ruby环境。

接着使用gem安装jekyll，gem是ruby的包管理器，类似于py的pip？

```
gem install jekyll
```

## 1.2 Windows

传送门：[Windows安装jekyll](https://blog.csdn.net/qiujuer/article/details/44620019)

# 2. 拉取模版本地测试


[repo地址](https://github.com/rookflying/rookflying.github.io)

先fork到自己的仓库，然后克隆到本地。克隆可以走https协议，也可以走ssh协议。以后push的时候，https协议每次都需要输入用户名和密码，ssh协议则需要在服务端配置公钥，不用每次输入口令。ssh协议速度快些。

该模版用到了两个插件，因此克隆到本地之后，需要在本地环境安装这两个插件：

```
gem install jekyll-paginate
gem install jekyll-sitemap
```

安装完之后就可以在本地测试了：

```
jekyll serve
```

默认端口是4000，因此现在可以访问http://127.0.0.1:4000查看效果了。本地测试好像并不支持https。

# 3. 在github上部署

github支持自动利用jekyll生成站点，因此部署非常简单。。只需要改一下repo的名字。。

在github上将刚刚fork的repo名字给改了，改成xxx.github.io形式，xxx是github用户名。接着就可以访问xxx.github.io了（可能会有几分钟的缓冲时间）。

github是按照master分支来生成站点的，因此确保更新的内容merge到master，或者直接push到master，才能看到更新的内容。

# 4. 全局配置文件_config.yml

这个站点的配置是通过这个全局配置文件来的，这个配置文件实际上就是定义一堆全局变量，站点里的其他文件可以访问到这些全局变量来渲染出不同的静态网页。因此实际上并不需要关心站点的项目布局，只需要关注这个配置文件就好了。例如下面的一些个性化内容：

```
# Site settings
title: rookflying's blog
SEOTitle: rookflying's blog
header-img: img/home.jpg
email: cpeng2424@163.com
description: "Somebody has to win"
keyword: "rookflying"
url: "https://rookflying.com"          # your host, for absolute URL
baseurl: ""      # for example, '/blog' if your blog hosted on 'host/blog'
github_repo: "https://github.com/rookflying/rookflying.github.io.git" # you code repository
```

可以看到配置文件的格式就是简单的 变量名：变量值。可以自行修改，本地测试查看修改结果，这上面的都是一些个性化的内容。

## 4.1 侧边栏

```
# Sidebar settings
sidebar: true                           # whether or not using Sidebar.
sidebar-about-description: "Somebody has to win"
sidebar-avatar: /img/avatar.jpeg      # use absolute URL, seeing it's used in both `/` and `/about/`



# SNS settings
RSS: false
github_username:        rookflying
codeforces_username:    DamJoker
```

sidebar:true 表示使用侧边栏，接着是一段文字和头像的设置。侧边栏下面还可以设置到其他平台的超链接，这里我只设置了github和codeforces的，可以自行添加，如添加知乎的：

```
zhihu_username:     xxxxx
```

接着在/_layouts/page.html里合适的地方添加如下代码（ctrl+f搜索codeforces__name的对应地方）：

```
{% if site.zhihu_username %}
          <li>
              <a target="_blank" href="https://www.zhihu.com/people/{{ site.zhihu_username }}">
                    <span class="fa-stack fa-lg">
                             <i class="fa fa-circle fa-stack-2x"></i>
                             <i class="fa  fa-stack-1x fa-inverse">知</i>
                    </span>
             </a>
          </li>
{% endif %}
```

页面下方也有超链接，也往_includes/footer.html里添加上面这段代码。

关于标签的配置如下：

```
# Featured Tags
featured-tags: true                     # 是否使用首页标签
featured-condition-size: 0              # 相同标签数量大于这个数，才会出现在首页
```

## 4.2 评论系统

评论用的是gitalk插件，代码已嵌入写好，只需要修改配置文件。

```
# Gitalk
gitalk:
  enable: true    #是否开启Gitalk评论
  clientID: xxxxxxxxxxxx                         #生成的clientID
  clientSecret: xxxxxxxxxxxxx   #生成的clientSecret
  repo: rookflying.github.io    #仓库名称
  owner: rookflying    #github用户名
  admin: rookflying
  distractionFreeMode: true #是否启用类似FB的阴影遮罩
```

gitalk需要一个Github Application,[点击注册](https://github.com/settings/applications/new)

![](https://github.com/rookflying/rookflying.github.io/blob/master/img/github_jekyll/register_github_application.png)

Homepage URL 和 Authorization callback URL 填的都是博客地址，即https://xxx.github.io。如果自己买了域名，并且解析到xxx.github.io，则也可以填域名。注册完会返回 clientId 和 clientSecret ，填入配置文件即可。

gitalk是基于 GitHub issue 开发的插件，因此需要github账户。因为是基于 GitHub issue 的，因此评论实际上就是在对应的 issue 提交 comment ，每篇文章对应一个 issue ，需要手动创建。

## 4.3 修改图标

将img下的favicon.ico替换掉即可。

# 5. 发表文章

发表的文章放在_post/目录下，文件格式是markdown，文件名格式统一为 YEAR-MONTH-DAY-NAME.md 。每个markdown文件头如下：

```
---
layout:          post
title:           快速基于GitHub Page + Jekyll搭建个人博客
subtitle:        利用现成可拉取的模版及可自定义个性化风格
date:            2018-12-16
author:          rookflying
head-img:        img/post-bg-ios9-web.jpg
catalog:         true
tags:
    - Jekyll
    - github
---
```

自行修改即可。把要发布的文章放入该目录，同步到远程master分支即可。

# 6. index.html/about.html/tags.html

index.html ， about.html ， tags.html 分别对应 HOME 主页， ABOUT 页和 TAGS 页。 ABOUT 页和 TAGS 页的背景图和文字（ description ）需要在 about.html 和 tags.html 的文件头里配置。 HOME 页的背景图在全局配置文件里配置，文字在 index.html 的文件头里配置。
