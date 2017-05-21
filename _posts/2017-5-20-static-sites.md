---
layout: post
title: 利用静态网站生成器制作静态博客网站
description: "working with static sites"
tags: Recent
image:
  feature: http://upload-images.jianshu.io/upload_images/1562320-67a21279dcfa5c18.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240
---

![http://www.stroustrup.com/](http://upload-images.jianshu.io/upload_images/1562320-05f0caeda5ecd451.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![https://stallman.org/](http://upload-images.jianshu.io/upload_images/1562320-bc87583881b1bc4f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

不知道大家有没有发现，长者们的网站都是相似的(比如上面这俩大佬[Bjarne Stroustrup](http://www.stroustrup.com/),[Richard Stallman](https://stallman.org/))，而粉丝们([比如我](https://feihao.me/))的网站却各有各的不同

其实在互联网早期，所有网站差不多长这样,他们都是静态的，因为当时压根就没有动态网站

不过我们今天要讲的不是像长者们一样的那种静态网站,而是通过静态网站生成器(SSG,Static Site Generator)生成的静态网站

近年来，随着浏览器发展,CDN成为主流,构建工具的广泛应用以及入们对高性能的不断追求,作为传统动态网站基础架构的替代方案，现代静态网站生成器日渐盛行。许多导致静态网站失败的限制已不复存在。现在，每周都会有新的静态网站生成器发布。

下面我将带大家一起了解静态网站生成器的一些知识并利用静态生成器搭建一个最基础的静态网站
### 什么是静态网站？

![静态网站](http://upload-images.jianshu.io/upload_images/1562320-1a1d04ca87a3fdbd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们在学习网站开发时写的第一个页面可能是这样的：
```html
<html>
<body>
<h1>Hello World</h1>
</body>
</html>
```
将上面的内容保存成index.html,然后扔到Apache服务器上,就可以通过互联网顺利访问了
上面的index.html就叫一个静态网页,由一堆静态网页构成的网站就叫静态网站

>在网站设计中，纯粹HTML（标准通用标记语言下的一个应用）格式的网页通常被称为“静态网页”，静态网页是标准的HTML文件，它的文件扩展名是.htm、.html，可以包含文本、图像、声音、FLASH动画、客户端脚本和ActiveX控件及JAVA小程序等。静态网页是网站建设的基础，早期的网站一般都是由静态网页制作的。静态网页是相对于动态网页而言，是指没有后台数据库、不含程序和不可交互的网页。静态网页相对更新起来比较麻烦，适用于一般更新较少的展示型网站。容易误解的是静态页面都是htm这类页面，实际上静态也不是完全静态，他也可以出现各种动态的效果，如GIF格式的动画、FLASH、滚动字幕等。

### 什么是动态网站?
![动态网站](http://upload-images.jianshu.io/upload_images/1562320-acc92c3d76a67576.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>动态网站并不是指具有动画功能的网站，而是指网站内容可根据不同情况动态变更的网站，一般情况下动态网站通过数据库进行架构。 动态网站除了要设计网页外，还要通过数据库和编程序来使网站具有更多自动的和高级的功能。动态网站体现在网页一般是以asp，jsp，php，aspx等结束，而静态网页一般是HTML（标准通用标记语言的子集）结尾，动态网站服务器空间配置要比静态的网页要求高，费用也相应的高，不过动态网页利于网站内容的更新，适合企业建站。动态是相对于静态网站而言。

动态网站的内容都是由服务器动态生成的,如WordPress等

### 静态网站的优点
* **访问速度快**
即使是最为优化的动态网站，其性能也无法同静态网站相比。并且，对于动态网站而言，缓存失效非常难以恢复，尤其是需要充分利用CDN的分布式缓存。静态网站所有内容都储存在html里面，不需要后台服务器对内容进行渲染，避免了查询数据库等操作，而且可以充分利用缓存和CDN
* **非常安全**
动态网站容易遭受蠕虫攻击。据保守估计，超过70%的WordPress部署容易因为已知漏洞遭受攻击（超过23%的Web网站以WordPress为基础构建）。网站安全两大威胁SQL注入和XSS（cross-site scritpting）攻击，静态站点都可以很好的避免
* **易于部署**
没有后端要求，想部署在哪儿就部署在哪儿
* **利于版本控制**
静态网站是由静态文件组成，所以非常容易使用Git等工具进行版本控制

### 哪些网站可以是静态的


* **博客**
这也是静态网站最主要的使用方式，与用户的交流仅限与评论，而利用Disqus可以满足该需求
* **文档**
仅次于博客的使用方式，因为文档需要方便用户快速查看，并且能够进行版本控制文档,而文档一般又不需要经常更新，所以静态网站非常合适,实际上,很多开源项目的文档都是利用Jekyll做的
* **网页海报**
这类网站通常非常简单，很适合做成静态网站

一般来说内容需要经常改变的以及需要和用户进行高频度的交互的网站都不适合做成静态网站

### 什么是静态网站生成器
![ssg](http://upload-images.jianshu.io/upload_images/1562320-0480ce5f43852300.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
简单来说,静态网站生成器就是一个由轻量的标记语言以及模版语言和元数据以及CSS预处理器加上可以编译成JavaScript的语言构成的用来生成静态HTML,CSS和JS文件的程序

目前网上有上百种生成器，不过它们都大同小异：
* **都使用一种或多种模版语言**
如Liquid，Handlebars，Jade等，这是静态网站生成器最关键的部分，它允许你为你的网站构建一套主题或者说布局，在构建的时候将动态的内容插入进去
* **都使用一种或多种轻量化的标记语言**
如Markdown，AsciiDoc，reStructuredText等,尽管大多数SSG都支持直接使用HTML，但是利用标记语言可以更快更容易的使用一些流行的文本编辑器进行内容编辑,比如我一般使用Typora进行MarkDown写作
* **都在命令行下运行**
大多数静态网站生成器都主要是一个命令行工具
* **都包含一个本地开发服务器**
允许你在正式发布之前在本地开发和测试。通常这些工具会检测你的文件的改变然后在你编辑的过程中就重新编译
* **都可扩展**
大多数静态网站生成器都允许你添加插件
* **都支持文件类型(file-based)的数据格式**
如YAML，TOML，JSON，它们允许你结构化任何类型的数据而不管他的展示形式,而标记语言如Markdown等一般用来保存长期的内容

### 静态网站生成器的选择

![image.png](http://upload-images.jianshu.io/upload_images/1562320-b15a94cf7c1971af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

那么面对五花八门的静态网站生成器我们到底该选哪个好呢?

真理往往掌握在大多数人手中,在GitHub搜索静态网站生成器然后按star排序

决定就是你了,jekyll

### jekyll安装与运行

![https://jekyllrb.com/](http://upload-images.jianshu.io/upload_images/1562320-204e7862c287bf0a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[jekyll](https://jekyllrb.com/)是一个专注于创建博客的静态网站生成器，他原生支持GitHub Pages，也就相当于为我们提供了免费的托管服务

下面我们按照官网的步骤并创建项目（我用的Mac，所以已经装好Ruby和RubyGems了）
```bash
gem install jekyll bundler
jekyll new my-awesome-site
cd my-awesome-site
```

这是创建好的文件目录结构
![directory](http://upload-images.jianshu.io/upload_images/1562320-6523d0e3b8c0f0c6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中各文件的用处如下:
* _config.yml: 这是jekyll的配置文件,在里面可以更改你网站的信息,如标题等
* _post: 你创建的新博客文件将会存放在这里
* about.md和index.md: 默认的关于页面和主页，除了主页你都可以删掉，当然你也可以添加新页面进来

而其中的Gemfile文件是ruby的包管理工具用的,类似cocoapods的podfile,对Ruby我不太熟悉,但是我们可以打开看看

![image.png](http://upload-images.jianshu.io/upload_images/1562320-e65fc263c8cee4f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到除了jekyll我们还安装了minima,这是jekyll的默认主题,要查看它的位置我们可以运行以下命令

```bash
bundle show minima
```

返回目录如下
```bash
/Library/Ruby/Gems/2.0.0/gems/minima-2.1.1
```
打开该目录我们可以看到minima主题的内容
![image.png](http://upload-images.jianshu.io/upload_images/1562320-42bda703ad080d96.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中主要文件夹有3个,其他不用管
* _includes: 将会包含到你的模版中的内容，比如多个页面都会用到到版权声明
* _sass: 用来编译生成css文件
* _layout：网页的布局文件

好了,我们现在开始运行jekyll
```bash
 jekyll serve
```
然后在浏览器打开localhost:4000

![image.png](http://upload-images.jianshu.io/upload_images/1562320-df92f73cef9ad143.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这是jekyll的默认UI，当然也可以将其更换为自己喜欢的主题

### 新建文章

要想新建一篇文章只需要把我们写好的文章Markdown文件添加到_post文件夹就行了

文章文件命名格式为：年-月-日-标题.后缀名

如:2017-5-20-hello-world.md

每个文件开头都必须有如下的YAML格式的说明

```html
---
layout: post
title:  "Welcome to Jekyll!"
date:   2017-05-21 09:02:30 -0700
categories: jekyll update
---
```

YAML储存的是简单的键值对数据，layout告诉jekyll使用何种模版来渲染该文件，title则是该文章的标题,data为文章发布,文件头中的日期优先级大于文件名的，你也可以移除文件头中的日期使用文件名中的日期，category则是你要发布的文件属于哪个分类

好了，现在我们来新建一个名为2017-5-20-hello-world.md的文件

内容如下：
```html
---
layout: post
title:  "Hello World"
date:   2017-05-21 09:02:30 -0700
categories: jekyll update
---

Hello World!
```


保存该文件到_post文件夹，然后我们刷新浏览器，就可以看到新文章了(并不需要重新运行jekyll,因为jekyll一旦检测到文档改变就会重新编译)

![image.png](http://upload-images.jianshu.io/upload_images/1562320-cb1b6f428d752071.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

打开Hello World就能看到该文章的详细页面如下
![image.png](http://upload-images.jianshu.io/upload_images/1562320-67a21279dcfa5c18.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Liquid模版语言
jekyll使用了一种叫[Liquid](https://shopify.github.io/liquid/)的模版语言,类似于express.js中使用的jade,关于Liquid的内容这里只做简单介绍

打开minima主题文件夹中_layout文件夹里的home.html
```html
---
layout: default
---
```html
<div class="home">

  <h1 class="page-heading">Posts</h1>

  {{ content }}

  <ul class="post-list">
    {% for post in site.posts %}
      <li>
        {% assign date_format = site.minima.date_format | default: "%b %-d, %Y" %}
        <span class="post-meta">{{ post.date | date: date_format }}</span>
    
        <h2>
          <a class="post-link" href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
        </h2>
      </li>
    {% endfor %}
  </ul>

  <p class="rss-subscribe">subscribe <a href="{{ "/feed.xml" | relative_url }}">via RSS</a></p>

</div>
```
Liquid使用** "{% %}" **作为循环，条件等逻辑语句的标记，而** "{{ }}" ** 作为变量替换的标记，**"|"**则为管道操作符,用来进行格式化操作

Liquid里面的变量来自于jekyll，可以参考[jekyll变量文档](https://jekyllrb.com/docs/variables/)

### 主题布局

下面我们看看jekyll的布局文件，我们所发布的每篇文章文件头部都需要有对布局的定义,如下面头文件中的layout所指定的post,而这个post着来自我们的主题minima文件夹里面,打开minima文件夹,在里面的_layout文件夹里我们将会看到一个post.html的文件,这就是我们post布局,文件头里的布局指定不能带后缀名，比如post.html就只能指定为layout: post而不是layout: post.html

```html
---
layout: post
title:  "Hello World"
date:   2017-05-21 09:02:30 -0700
categories: jekyll update
---

Hello World!
```
如果你不指定layout将是这样的结果
![image.png](http://upload-images.jianshu.io/upload_images/1562320-986139cd709b6b34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


打开minima主题文件夹中_layout文件夹里的post.html
```html
---
layout: default
---
<article class="post" itemscope itemtype="http://schema.org/BlogPosting">

  <header class="post-header">
    <h1 class="post-title" itemprop="name headline">{{ page.title | escape }}</h1>
    <p class="post-meta">
      <time datetime="{{ page.date | date_to_xmlschema }}" itemprop="datePublished">
        {% assign date_format = site.minima.date_format | default: "%b %-d, %Y" %}
        {{ page.date | date: date_format }}
      </time>
      {% if page.author %}
        • <span itemprop="author" itemscope itemtype="http://schema.org/Person"><span itemprop="name">{{ page.author }}</span></span>
      {% endif %}</p>
  </header>

  <div class="post-content" itemprop="articleBody">
    {{ content }}
  </div>

  {% if site.disqus.shortname %}
    {% include disqus_comments.html %}
  {% endif %}
</article>
```
这就是我们之前发布的文章布局post的定义文件了,该文件也有个头信息

```html
---
layout: default
---
```

可以看到,布局文件也可以有布局,我们打开default.html布局文件看看
default.html：
```html
<!DOCTYPE html>
<html lang="{{ page.lang | default: site.lang | default: "en" }}">

  {% include head.html %}

  <body>

    {% include header.html %}

    <main class="page-content" aria-label="Content">
      <div class="wrapper">
        {{ content }}
      </div>
    </main>
    
    {% include footer.html %}

  </body>

</html>
```
post.html文件中的所有内容都将被插入到下面content的位置
```html
 <div class="wrapper">
        {{ content }}
 </div>
```
注意default.html里面的include
```html
{% include head.html %}
```
利用include我们可以直接将其他文件的内容引用进来

### 其他页面
我们的网站当然不可能全部由文章构成，也会有一些其他页面,比如关于页面等，这些页面除了不能放在_post文件夹里面以外,它们和文章页面没什么不同

把它们放到jekyll根目录就行了,它们将会被自动解析到liquid标签供你使用

下面我们在根目录创建一个新文件就叫count.md
```markdown
---
layout: default
title: Count
---

I have {{site.posts.size}} posts
```
这里我们用到了jekylly的变量来显示文章数量

刷新过后就可以在网页右上角看到,点击进入Count页面就能显示文章数目
![count page](http://upload-images.jianshu.io/upload_images/1562320-d141d5d8cb5198e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 接下来
如果默认主题符合你的需求的话其实到此你的博客网站就已经构建完成了,接下来只需要发布到你的服务器就行了,如果没有服务器可以利用[GitHub Pages](https://pages.github.com/)进行发布

更多主题可以到[GitHub](https://github.com/search?o=desc&q=jekyll+theme&s=stars&type=Repositories&utf8=%E2%9C%93)进行搜索