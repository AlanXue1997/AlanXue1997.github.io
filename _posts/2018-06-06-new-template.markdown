---
layout: article
title: 更改博客模板
mathjax: true
tags: jekyll ruby 配环境
key: 2018-06-06-new-template
---

# 前言

之前这个博客使用的是一个极简的模板[The plain](https://heiswayi.nrird.com/the-plain/)的老版本，现在事实上已经稍微丰富了一点点了，在那个老版本里，除了展示最基础的markdown之外，几乎什么都不支持。于是想换一个使用方便，且功能较全的模板。

经过一段时间的搜索，觉得[TeXt Theme](https://tianqi.name/jekyll-TeXt-theme/)这个模板写的很好，而且应该是国人写的，配有[中文文档](https://tianqi.name/jekyll-TeXt-theme/docs/zh/quick-start)。


今天写这篇博文，主要是记录一下在修改到此模板的过程中遇到的各种问题，以及最终的解决方式。

<!--more-->

# 配环境

## 初次启动失败

### Could not find gem 'jemoji (~> 0.8) x64-mingw32'

首先，从GitHub上clone下来整个仓库后，按照文档，应该执行`bundle exec jekyll serve`命令来启动服务器。但我再尝试的时候发现这个会报“缺少jemoji”的错误，根据Stack Overflow上[一个问题](https://stackoverflow.com/questions/45301378/bundler-could-not-find-compatible-versions-for-gem-jemoji-when-bundle-exec-j)的答案，在你第一次执行`bundle exec jekyll serve`命令之前，应该先执行`bundle install`指令。

### bundle install 特别慢/没反应

在执行`bundle install`后，界面就一直没有反应，也不知道是否已经开始下载，搜索后发现这是因为该指令默认的下载地址是<https://rubygems.org/>（我的老版本实际是<http://rubygems.org/>，这个地址在墙外，因此速度非常非常慢。

一些较老的博客会给出将地址修改为<https://ruby.taobao.org/>的解决方法，但根据该网站的告示，这个网站已经不再维护，RubyGems 镜像的管理工作已经将交由[Ruby China](https://gems.ruby-china.org/)负责，相应的换sources的教程在[Ruby China的官网](https://gems.ruby-china.org/)上有介绍。

### gem update --system 特别慢/没反应

[Ruby China](https://gems.ruby-china.org/)建议先升级RubyGems版本，然而这一步需要翻墙，我尝试把Shadowsocks开到了全局模式，但是并没有什么反应，或者说我没有耐心等到他有反应，于是决定手动安装新版本。

记不清是从哪找到的网站，[Ruby Installer for Windows](https://rubyinstaller.org/downloads/)， 直接下载了最新版本，下载完运行，安装器不会检测已有的版本，但根据选项，它会自动配置环境变量，因此猜测安装完成后，环境变量就已经被换为新版本了。

安装完成后，在**git bash**中仍为老版本，打开cmd窗口，输入`ruby --version`，发现已经是新版本，推测有些改动重启后才能生效，于是重启电脑，再次在**git bash**中检测ruby版本，已经切换到了新版本。

### SSL证书错误

将[Ruby China](https://gems.ruby-china.org/)的教程进行完，执行`bundle install`，这次反应非常快，说明网络已经通了，但果然报了**SSL证书错误**。

首先，官网说的`~/.gemrc`地址是Linux下的，在Windows下，应该是`C:\Users\<你的用户名>`（中文系统中`Users`文件夹在资源管理器中显示为`用户`）。

在添加了`:ssl_verify_mode: 0`一行后，报错信息发生了改变，但仍然是**SSL证书错误**。

[这篇博客](https://blog.csdn.net/ailurus_fulgens/article/details/54287621)给出了比较全面的解决方法。我最终的解决方法是添加环境变量，有一点需要强调的是这个环境变量添加的是**文件**而不是文件夹或路径。

之后执行`bundle install`，运行成功。

执行`bundle exec jekyll serve`，运行成功，能够打开网页。

## 配置

这里的配置指的就是中文文档里面的配置那一小节。

### gitalk

在此强调，这里说的gitalk遇到的问题，是在配置[TeXt Theme](https://tianqi.name/jekyll-TeXt-theme/)模板的gitalk部分时遇到的问题，与真正的gitalk关系并不大。

#### 结论

感觉文档中这里具体怎么写说的并不是很清楚，下面先放上一些结论，后从源代码的角度说一下有些结论的原因。

1. 注册GitHub Application时，Homepage URL填博客地址即可，Authorization callback URL可以不填，缺省会被自动填为和Homepage URL一样。
2. `_config.yml`中gitalk部分不需要加任何的引号或括号
3. 仓库名、用户名参考我下面的例子
4. `admin`是一个list，填法参考我下面的例子
4. 必须在文章的YAML头信息中添加`key`字段，该文章才能有评论，key字段不能重复

#### 例子

`_config.yml`中：

```
## Gitalk
# please refer to https://github.com/gitalk/gitalk for more info.
gitalk:
  clientID: 61***************b7 # GitHub Application Client ID
  clientSecret: 54************************************64 # GitHub Application Client Secret
  repository: AlanXue1997.github.io # GitHub repo
  owner: AlanXue1997 # GitHub repo owner
  admin: # Github repo owner and collaborators, only these guys can initialize github issues, IT IS A LIST.
    - AlanXue1997
```

本文章YAML头信息：

```
---
layout: article
title: 更改博客模板
mathjax: true
tags: jekyll ruby 配环境
key: 2018-06-06-new-template
---
```

#### 源代码分析

首先，看一下`/_includes/comment.html`

```rhtml
{% raw %}
{%- if jekyll.environment != "development" -%}

  {%- if site.disqus.shortname -%}
    {%- include comment-providers/disqus.html -%}
  {%- elsif site.gitalk.clientID and site.gitalk.clientSecret and site.gitalk.repository and site.gitalk.owner and site.gitalk.admin -%}
    {%- include comment-providers/gitalk.html -%}
  {%- endif -%}

{%- endif -%}
{% endraw %}
```

从第一行就可以看出，在开发环境中，comment里面的代码是不会被执行的，所以本地看不见gitalk是很正常的。

gitalk相关的html文件存储在`/_includes/comment-providers/gitalk.html`中，内容如下：

```rhtml
{% raw %}
{%- if page.key and
  site.gitalk.clientID and
	site.gitalk.clientSecret and
	site.gitalk.repository and
	site.gitalk.owner and
	site.gitalk.admin -%}

	{%- include snippets/get-sources.html -%}
	{%- assign _sources = __return -%}
	<div id="js-gitalk-container"></div>
	{%- assign _admin = '' -%}
	{%- for adminId in site.gitalk.admin -%}
		{%- assign _admin = _admin | append: ", '" | append: adminId | append: "'" -%}
	{%- endfor -%}
	{%- assign last = _admin | size | minus: 1 -%}
	{%- assign _admin = _admin | slice: 2, last -%}
	<script>
		window.Lazyload.css('{{ _sources.gitalk.css }}');
		window.Lazyload.js('{{ _sources.gitalk.js }}', function() {
			var gitalk = new Gitalk({
				clientID: '{{ site.gitalk.clientID }}',
				clientSecret: '{{ site.gitalk.clientSecret }}',
				repo: '{{ site.gitalk.repository }}',
				owner: '{{ site.gitalk.owner }}',
				admin: [{{ _admin }}],
				id: '{{ page.key }}'
			});
			gitalk.render('js-gitalk-container');
		});
	</script>

{%- endif -%}
{% endraw %}
```

首先，`<script>`中，代码中已经加了单引号，因此在写配置文件`_config.yml`的时候并不需要加引号。其次，根据第一行，如果该文章没有key字段（`page.key`），则此代码不会被执行，而 `page.key`被赋值给了`gitalk.id`（倒数第五行），用来唯一标识评论的id，需要保证每篇文章的评论id不同，因此需要为每个文章的YAML头信息添加不同的key字段。

# 写本文遇到的问题

## ruby template不能被正确高亮

这个问题本来就是写markdown时遇到的问题，想想现在要在markdown中把问题说清楚。。。难度太大了，如果有人遇到**ruby template不能被正确高亮**或**django template不能被正确高亮**，直接参考这个issue“[Highlight does not work with django templates #281](https://github.com/jekyll/jekyll/issues/281)”吧。