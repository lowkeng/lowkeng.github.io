---
title: 用Markdown写幻灯片——reveal.js
date: 2020-09-20 17:07:05
tags: [reveal.js,markdown,幻灯片，PPT,排版和演示]
catogories: 暂未分类
---

# 快速开始

revea.js是基于CSS和HTML的幻灯片写作工具，支持第三方插件来增强效果。

STEP1：搭建reveal.js使用环境

>使用reveal.js必须先安装`Node.js`和`Git`。

STEP2：下载reveal.js

>可以使用浏览器到官网下载
>也可以使用Git命令下载
>
>```bash
>git clone https://github.com/hakimel/reveal.js.git
>```

STEP3：查看示例

>打开`reveal.js`文件夹，在根目录找到`index.html`,然后使用VS Code打开。
>
>```html
><div class="reveal">
>		<div class="slides">
>			<section>Slide 1</section>
>			<section>Slide 2</section>
>		</div>
></div>
>```
>
>从这段代码可以看出，在HTML中，幻灯片标记的层次结构是：`.reveal > .slides > section`。关于`section`，我们可以理解为分页标签，每一张幻灯片都是使用`<section>标签包裹的。`

STEP4：创建第一个幻灯片

> 复制`index.heml`并重命名为`first.html`,修改核心代码，具体如下
>
> ```html
> <div class="reveal">
> 		<div class="slides">
> 			<section>如何使用reveal.js创建和演示幻灯片</section>
> 			<section>第一步：下载reveal.js项目构建创作环境</section>
>                     <section>第二步：熟悉语法规则后进行内容创作</section>
>                     <section>第三步：启动本地服务进行幻灯片演示</section>
>            </div>
> </div>
> ```
>
> 保存后双击`first.html`可在浏览器中打开幻灯片 

# 使用指南

前面搭建的是最基本的环境配置，进行一些基础的操作是没问题的，但有些功能需要从本地Web服务器运行，如演讲者备注、引用外部Markdown文件、修改后自动刷新等。为了能够进行完整的演示，还要对项目环境进行完整配置。     

## 搭建完整的项目环境

STEP1：在【reveal.js】文件夹下打开`git bash`
STEP2：安装`reveal.js`项目中依赖的模块，在终端运行如下命令

```bash
npm install
```

如果安装模块失败，请使用`cnpm install`来安装

STEP3：运行如下命令，启动Web服务

```
npm start
```

打开`http://localhost:8000/` ，显示`index.html`页面，如果想要打开`first.html`页面，则需要手动输入`http://localhost:8000/first.html` 。如果端口被占用，可以在运行时指定端口，命令如下：

```bash
npm start -- --port=7000
```

## 快速了解reveal.js  

本节将介绍一些使用reveal.js写幻灯片的最基础的知识，后文中会有更多详细的内容和使用案例

### 创建并编写幻灯片

在【reveal.js】项目中创建HTML文件，然后在HTML文件中编写幻灯篇。基本结构如下：

```html
<!doctype html>
<html>
    
	<head>
		<link rel="stylesheet" href="css/reveal.css">
   	    <!-- 在这里修改幻灯片的主题 -->
    	<link rel="stylesheet" href="css/theme/black.css">
	</head>
    
	<body>
    	<div class="reveal">
            <div class="slides">
                <!-- 在<section>标签中编写幻灯片的内容 -->
                <section>第1页</section>
                <section>第2页</section>
            </div>
        </div>
    	
        <script src="js/reveal.js"></script>
        <script>
            //在这里修改幻灯片的配置
            Reveal.initialize({});
        </script>
	</body>
</html>
```

1)  在`<head>`标签中设置幻灯片的主题

2）在<section>标签中编写幻灯片的内容

3）在`Reveal.initialize({});`中添加`reveal.js`的依赖和配置

### 演示幻灯片

简单的幻灯片可以通过双击HTML文件在浏览器中打开进行演示。但如果涉及演讲者备注或引用外部的Markdown文件，则需要使用Web服务器运行。本地运行命令为`npm start`，如果想要部署到服务器上则需要使用`Nginx` 

### 幻灯片的类型

reveal.js中的幻灯片分为水平幻灯片和垂直幻灯片两种类型，垂直幻灯片常常嵌套在水平幻灯片中使用。示例代码如下：

```html
<!-- 最外层是水平幻灯片 -->
<section>
	<!-- 嵌套的垂直幻灯片1 -->
    <section data-markdown>
    	<textarea data-template>
        	## 我是第1页
        </textarea>
    </section>
    <!-- 嵌套的垂直幻灯片2 -->
    <section data-markdown>
    	<textarea data-template>
        	## 我是第2页
        </textarea>
    </section>
</section>
```

### 使用Markdown编写幻灯片

reveal.js使用Markdown编写幻灯片有两种方式：

1）在HTML文件中直接使用Markdown编写

2）在HTML文件中引用外部的Markdown文件

如果在HTML文件中直接使用Markdown编写，需要给`<section>` 标签添加`data-markdown`属性，并且内容要使用`textarea data-template>`包裹，示例代码如下:

```html
<section data-markdown>
	<textarea data-template>
    ## 居中对齐
        
    |序号|姓名|年龄
    |:---:|:---:|:---:|
        |1|毕小凡|31|
        |2|张三|30|
        |3|李四|32|
        
    </textarea>
</section>
```

如果在HTML文件中引用外部的Markdown文件，则需要指定分页的匹配规则，示例代码如下

```html
<!-- 引用外部的Markdown文件 -->
<section data-markdown="外部文件.md">
	data-separator="^\n\n\n"
    data-separator-vertical="^\n\n"
    data-separator-notes="^Note:"
    data-charset="urf-8"
</section>
```

### 添加reveal.js的依赖和配置

在`Reveal.initialize({});`中添加`reveal.js`的依赖和配置，如开启历史记录、显示页面、设置全局转场效果等，示例代码如下：

```html
<script>
	Reveal.initialize({
        history: true                        //开启历史记录
        slideNumber: true                    //显示页码
        transition: 'convex'
        //配置依赖的库
        dependencies:[
        	{
       				 src:'plugin/markdown/marked.js'
    		},
            {
                     src:'plugin/markdown/markdown.js'
            },
    		{
                	 src:'plugin/notes/notes.js',
                	 async: true
            },
    		{
					 src:'plugin/highlight/highlight.js',
                	 async: true,
                	 callback: function(){
                         hljs.initHighlightingOnLoad();
                     }
            },
    		{src:'plugin/zoom-js/zoom.js',async: true},
                      
       ] 
    })
</script>
```

reveal.js不依赖任何第三方脚本，但有一些可选的库供用户选择，这些库按依赖顺序进行加载，如上述代码所示。

关于配置依赖库的语法解读如下：

1）src: 指定要加载脚本的路径

2）async：[可选]，如果脚本要在`reveal.js`启动后加载，则设置为`true`,默认为`false`

3）callback：[可选]，指定脚本加载后要执行的函数

### 示例：编写幻灯片的封面

- 为幻灯片写封面（HTML版）

  1）复制`index.html`,并重命名为`revealjs.html`,用VS Code打开

  2）先不管代码的其他部分，先找到`<div class="slides">`,在`<section>`标签中添加幻灯片的内容，具体如下：

  ```html
  <div class="reveal">
      <div class="slides">
          <!-- HTML实现效果 -->
          <section>
          <h1>大道至简</h1>
          <h3>使用 reveal.js快速创建精美的幻灯片</h3>
          <p>
          作者：<a href="https://lowkeng.github.io">罗坤</a>    
          </p>     
          </section>
      </div>
  </div>
  ```

  为了理解后续内容，先要明确几个HTML中的概念：

  1）元素：指的是从开始标签到结束标签之间的所有代码

  2）标签：由<>包裹的关键字，通常成对出现。

  3）属性：通常在元素的开始标签中`键/值`对的形式出现，如`<section data-state="customevent">`

- 为幻灯片写封面（Markdown版）

  示例代码如下

  ```html
  <div class="reveal">
      <div class="slides">
          <!-- Markdown实现效果 -->
          <section data-markdown>
          	<textarea data-template>
              	# 大道至简
                  ### 使用`reveal.js`快速创建精美的幻灯片
                  作者：[lowkeng](https://lowkeng.github.io)
              </textarea>
          </section>
      </div>
  </div>
  ```

  **小提示**：reveal.js通过`marked.js`解析Markdown，在使用Markdown之前请确保添加了以下依赖项

  ```html
  <script>
  	Reveal.initialize({
          dependencies:[
          	{
         				 src:'plugin/markdown/marked.js'
      		},
              {
                       src:'plugin/markdown/markdown.js'
              },                      
         ] 
      })
  </script>
  ```




## 常用格式

reveal.js使用`marked.js`来解析Markdown，并且支持`CommonMark` 和`GitHub Flavored Markdow` 。不过有些效果还是需要使用HTML或引入其他资源来实现，例如任务列表、视频、字体颜色、下划线和emoji等。

### 标题

```html
<!-- 标题（Markdown) -->
<section data-markdown>
	<textarea data-template>
    	# 一级标题
        ## 二级标题
        ### 三级标题
        #### 四级标题
        普通文本
        
        <small>更小的文本</small>       
    </textarea>
</section>
```

**小提示**：

1）四五六级标题区别不大

2）在Markdown中使用HTML标签以实现更丰富的显示效果，如改变字体颜色、设置图片显示大小和位置等

### 引用

行内引用和区块引用的代码，由于Markdown不支持行内引用标记，因此行内引用使用<q>标签实现

```HTML
<!-- 引用(Markdown) -->
<section data-markdown>
	<textarea data-template>
    毕小凡说:<q>Markdown真好用。</q>
    
    > 区块引用使用 > 标记
    > >引用嵌套
    </textarea>
</section>
```



  ### 列表

列表示例代码如下

```HTML
<!-- 无序列表(Markdown) -->
<section data-markdown>
	<textarea data-template>
    	待办事项：
        - 跑步
        - 喝酒
        - 睡觉
    </textarea>
</section>

<!-- 有序列表(Markdown) -->
<section data-markdown>
	<textarea data-template>
    	待办事项：
        1. 跑步
        1. 喝酒
        1. 睡觉
    </textarea>
</section>

<!-- 任务列表(Markdown) -->
<section data-markdown>
	<textarea data-template>
    	## 任务列表
        <input type="checkbox" style="zoom:200%;" checked/>跑步
        <input type="checkbox" style="zoom:200%;" />喝酒
        <input type="checkbox" style="zoom:200%;" />睡觉
    </textarea>
</section>
```

由于reveal.js中Markdown不支持任务列表，因此需要使用HTML来实现，在本例中设置了复选框的大小。

###  文本格式

Markdown中下划线需要通过`<u>`标签来实现，e.g.  `<u>下划线</u>`，另外改变文字颜色需要使用`<font>`标签和`color`属性来实现，e.g. `<font color="#fa0">改变颜色</font>`

### Emoji表情

在reveal.js中无法直接使用`Emoji`,使用时需要在head中引用一个CSS文件，如下所示

```html
<link href="https://afeld.github.io/emoji-css/emoji.css" rel="stylesheet">
```

然后在Markdown中这样使用

```html
<!-- Emoji(markdown) -->
<section data-markdown>
	<textarea data-template>
    	## Emoji
        <i class="em em-construction_worker"></i>
        <i class="em em-dog"></i>
        <i class="em em-dancers"></i>
    </textarea>
</section>
```

Emoji的选择很简单，打开`https://afeld.github.io/emoji-css/`,点击某个表情图标就可以复制对应的代码了。

### 图片

如果需要设置图片大小，需要使用HTML标签来实现，示例代码如下：

```html
<!-- 在Markdown中使用HTML标签设置图片大小 -->
<section data-markdown>
	<textarea data-template>
    	## 设置图片大小
        <img width="200" height="250" data-src="imgs/zhuobielin.jpg" alt="卓别林">
    </textarea>
</section>
```

### 视频和音频

插入视频和音频的代码如下

```HTML
<!-- 插入视频(Markdown) -->
<section data-markdown>
	<textarea data-template>
    	## 视频
        <video width="400" height="350" controls="controls">
        	<source src="./imgs/video.mov" type="video/mp4">
        </video>
    </textarea>
</section>

<!-- 插入音频(Markdown) -->
<section data-markdown>
	<textarea data-template>
    	## 音频
        <audio src="音频地址" controls="controls"></audio>
    </textarea>
</section>
```

使用`controls`属性可以显示播放控件。

## 幻灯片的嵌套、链接和注释

### 幻灯片嵌套

```html
<!-- 最外层是水平幻灯片 -->
<section>
	<!-- 嵌套的垂直幻灯片1 -->
    <section data-markdown>
    	<textarea data-template>
        	## 垂直幻灯片1
        	按<font color="#fa0">↓</font>翻到下一页垂直幻灯片
        </textarea>
    </section>
    <!-- 嵌套的垂直幻灯片2 -->
    <section data-markdown>
    	<textarea data-template>
        	## 垂直幻灯片2
            按<font color="#fa0">↑</font>翻到下一页垂直幻灯片
        </textarea>
    </section>
</section>
```

### 链接幻灯片

当我们进行幻灯片演示时，如果想要回到之前讲过的某一页或提前查看后面的某一页，就需要用到幻灯片链接功能了。

本质上，使用reveal.js编写的幻灯片就是一个网页，因此需要在幻灯片之间跳转，只需要添加链接就可以了。

```html
<section data-markdown>
	<textarea data-template>
    	去[第三页](#/3)
        
        去[第2页第2节](#/2/2)
    </textarea>
</section>
```

### 演讲者注释

在演讲者注释界面上，可以查看当前幻灯片的注释，也可以预览下一页幻灯片的内容。由于演讲者注释是嵌到幻灯片里面的，因此要在对应的幻灯片页面里编写注释内容。如果用Markdown编写，则注释内容默认以`Note+:`开头，示例代码如下：

```html
<!-- 演讲者注释(Markdown) -->
<section data-markdown>
	<textarea data-template>
    	## 按<font color="#fa0">**S**</font>键可以打开演讲者注释
        <!-- 演讲者注释 -->
        Note:
        在演讲者注释界面上，可以查看当前幻灯片的注释，也可以预览下一页幻灯片的内容。
    </textarea>
</section>
```

## 幻灯片演示

### 背景

幻灯片可以以不同颜色作为背景，也可以使用图片或视频作为背景。

#### a) 颜色背景

示例代码如下

```HTML
<!-- 背景颜色 -->
<section data-background="#F34D4E" data-markdown>
	<textarea data-template>
    	## 指定背景颜色
        
        ```html
        &lt;section data-background="#F34D4E"&gt;
    </textarea> 
</section>
```

**注意**：再插入代码中如果有特殊符号`<`和`>`，则需要使用`&lt;`和`&gt;`代替，否则他们会被浏览器认为是标签而导致渲染错误。

#### b) 图片背景

将图片设为背景，需要在`<section>`标签中使用`data-background`属性进行设置，示例代码如下

```HTML
<!-- 图片背景 -->
<section data-background="./imgs/galaxy.png" data-markdown>
	<textarea data-template>
    	## 指定背景图片
        
        ```html
        &lt;section data-background="path/image.png"&gt;
    </textarea>
</section>
```

在默认情况下，图片会被拉伸填满整个页面，这有可能会导致图片失真，为了避免这种情况，可以使用`data-background-size`属性设置图片的大小，也可以使用`data-background-repeat="repeat"`平铺图片，示例代码如下：

```html
 <section data-background="./imgs/star.jpeg" data-markdown data-background-repeat="repeat" data-background-size="100px">
	<textarea data-template>
     	<!-- 通过CSS添加背景颜色 -->
        <div style="background-color:rgba(0,0,0,0.603); color:#fff; padding:15px;">
         平铺图片
        </div>
        ```html
        <!-- 平铺图片背景图片，并指定图片的大小 -->
        &lt;section data-backround="./imgs/star.jpeg" data-markdown data-background-repeat="repeat" data-background-size="100px"&gt;
     </textarea>
</section>
```

#### c) 网页背景

将网页设为幻灯片的背景，需要在`<section>`标签中使用`data-background--iframe`属性进行设置，示例代码如下：

```html
<section data-background-iframe="https://www.baidu.com" data-background-interactive data-markdown>
	<textarea data-template>
    ## 网页作为幻灯片的背景
    </textarea>
</section>
```

### 主题

reveal.js提供了11种主题。

| 序号 |    主题     |       CSS文件地址       |
| :--: | :---------: | :---------------------: |
|  1   | black(默认) |   css/theme/black.css   |
|  2   |    white    |   css/theme/white.css   |
|  3   |   league    |  css/theme/league.css   |
|  4   |     sky     |    css/theme/sky.css    |
|  5   |    beige    |   css/theme/beige.css   |
|  6   |   simple    |  css/theme/simple.css   |
|  7   |    serif    |   css/theme/serif.css   |
|  8   |    blood    |   css/theme/blood.css   |
|  9   |    night    |   css/theme/night.css   |
|  10  |    moon     |   css/theme/moon.css    |
|  11  |  solarized  | css/theme/solarized.css |

替换主题的方法：

在head中找到引用主题的样式文件，

```html
<link rel="stylesheet" href="dist/theme/black.css" id="theme">
```

默认主题是black，使用其它主题只要替换`black.css` 

### 开启历史记录

由于每次保存修改后，Web服务器都会自动重启，页面也会随之刷新，这就导致刷新后的页面总是会跳转到首页。假如我们编辑的是第10页的内容，保存后浏览器会自动跳转到首页，但如果我们想要看修改后的效果，就需要再翻转到第10页才能看到，这样极不方便。

那么如何在保存修改后只在当前页面刷新而不跳转到首页呢？答案就是开启幻灯片的历史记录功能，让它记住我们当前所在的页面。

开启方法就是在`revealjs.html`代码底部找到`Reveal.initialize({});`，添加`history:true`j即可开启历史记录，示例代码如下

```html
Reveal.initialize({
	history:true,
	dependencies:[{
		......
	}]
})
```

### 显示页码

如果想要在幻灯片上显示页码，需要在`Reveal.initialize({});`中进行配置

```html
slideNumber:true,
```

### 显示进度条

进度条默认是显示的(默认`progress:true` ，如果不想显示进度条，需要在`Reveal.initialize({});`中配置

```html
progress: false,
```

## 演示效果

### 分段演示

在演示幻灯片时，把一页幻灯片中的内容分段显示出来

```html
<!-- 分段演示(Markdown) -->
<section data-markdown>
    <textarea data-template>
        ## 什么是分段演示？
        <span class="fragment" >分段演示</span>
        <span class="fragment">就是</span>
        逐步 <!--.element:class:“fragment”-->
        显示内容  <!--.element:class:“fragment”-->
        它有很多动画效果 <!--.element:class:“fragment”-->
    </textarea>
</section>

<!-- 分段演示动画(Markdown) -->
<section data-markdown>
    <textarea data-template>
        ## 什么是分段演示？
        <p class="fragment fade-down" >分段演示</p>
        <p class="fragment fade-up">就是</p>
        逐步 <!--.element:class:“fragment”-->
        显示内容  <!--.element:class:“fragment”-->
        它有很多动画效果 <!--.element:class:“fragment”-->
    </textarea>
</section>
```

### 转场动画

设置单个幻灯片的转场动画，需要给`<section>`标签添加`data-transition`属性，属性值就是转场动画的效果，示例如下：

```html
<section data-transition="zoom">
	<h2>放大</h2>
    这种过渡动画效果怎么样？
</section>
```

reveal.js提供的转场动画有如下几种

| 转场动画可选值 | 转场效果 |
| :------------: | :------: |
|     convex     | 凸进凸出 |
|    concave     | 凹进凹出 |
|      zoom      |   缩放   |
|     slide      | 移入移出 |
|      fade      | 淡入淡出 |

设置全局转场动画效果，需要在`Reveal.initialize({});`中添加如下代码

```html
transition: 'convex',
```

页面设置的优先级高于全局设置，因此即使设置了全局转场效果，如果还想对某页幻灯片进行其他设置也是可以的。

### 背景过渡动画

可以通过在`<section>`标签中添加`data-background-transition`属性为每一页幻灯片设置不同的背景过渡动画，动画效果的属性值选项与上文的转场动画一样。如果设置了背景过渡动画，内容的过渡就默认为滑入效果。

### 自动演示

自动演示只需要设置自动演示的间隔时间就可以了，示例

```html
//每3s自动翻页
Reveal.configure({
	autoSlide: 3000
})
```

可对单个幻灯片和片段单独设置自动演示时间

```html
<section data-autoslide="3000">
    <p class="frament" data-autoslide="8000">
        本页第一个片段 <!--.element:class:“fragment”--> 8s后自动播放本页第二个片段
    </p>
    <p class="fragment" data-autoslide="6000"> 
        本页第二个片段 <!--.element:class:“fragment”--> 6s后自动播放本页第三个片段
    </p>
    <p class="fragment"> 
        本页第三个片段 
    </p>
</section>
```

### 循环演示

在`Reveal.initialize({});`中添加

```HTML
loop: true,
```

### 局部放大

需要在在`Reveal.initialize({});`中添加依赖项，具体如下

```html
dependencies:[{src:'plugin/zoom-js/zoom.js',async: true},]
```

### 休眠

如果在演示时需要休息一会儿，只需要按`B`键或者`.`键就可以让幻灯片休眠或恢复。

### 全屏

按`F`键可以进入全屏状态，按`Esc`键可以退出全屏

### 快捷键

下一张幻灯片：`N`键或空格键

上一张幻灯片：`P`键

演讲者注释页面：`S`键

## 引用外部的Markdown文件

在HTML代码中使用Markdown来编写幻灯片，不好的地方是HTML与Markdown交叉使用，如果不熟悉HTML，就会觉得比较乱，而且一旦格式化代码会导致Markdown编写的内容全乱掉，且失去语法高亮和语法检查的效果。

不过，reveal.js允许我们引用外部的Markdown文件。这样可以让HTML文件和Markdown文件分离，在HTML文件中专注于幻灯片的配置，而在Markdown文件中专注于内容的编写，语法检查和语法高亮也可以使用起来。另外，我们可以引用多个Markdown文件，内容分类也会很方便。

外部的Markdown文件是在`<section>`标签中指定的，我们需要在HTML中添加`<section>`标签，然后在`<section>`标签的属性中指定加载和解析Markdown文件的规则。

### 配置解析规则

在`<section>`标签中添加属性

```html
<section data-markdown="外部文件.md"
	data-separator="^\n\n\n"
    data-separator-vertical="^\n\n"
    data-separator-notes="^Note:"
    data-charset="urf-8">
</section>
```

配置说明

|         作用         |              属性               |             说明             |
| :------------------: | :-----------------------------: | :--------------------------: |
|   Markdown文件路径   |   data-markdown="外部文件.md"   |     外部Markdown文件路径     |
|  水平幻灯片分页规则  |    data-separator="^\n\n\n"     |  在3次换行之后是水平幻灯片   |
|  垂直幻灯片分页规则  | data-separator-vertical="^\n\n" |  在2次换行之后是垂直幻灯片   |
|  演讲者注释匹配规则  |  data-separator-notes="^Note:"  | 以Note开头的内容为演讲者注释 |
| 加载文件是的编码格式 |      data-charset="urf-8"       |       编码格式为UTF-8        |

接着上面的例子，我们编写外部文件.md

```markdown
# 第1页

Markdown文件水平分页匹配规则
​```HTML
data-separator="^\n\n\n"
​```
在3次换行符之后是1页水平幻灯片



# 第2页
Markdown文件垂直分页匹配规则
​```HTML
data-separator-vertical="^\n\n"
​```
在2次换行符之后是1页垂直幻灯片


# 第3页
Markdown演讲者注释匹配规则
​```html
data-separator-notes="^Note:"
​```
以Note:开头的内容为演讲者注释
Note:
#我是演讲者注释
按`S`键可以看到我
```

### 另一种分页规则

```html
<section data-markdown="外部文件.md"
	data-separator="^\n---\n$"
    data-separator-vertical="^\n--\n$"
    data-separator-notes="^Note:"
    data-charset="urf-8">
</section>
```

这里定义的水平幻灯片的分页规则是：`空行+---+空行`；垂直幻灯片的分页规则是：`空行+--+空行`