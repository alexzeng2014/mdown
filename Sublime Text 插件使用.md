Sublime Text 插件使用
=====================

![Sublime Text](https://camo.githubusercontent.com/53cd6a9bd9c58e19c4256213e88dd65cbbcc756b/68747470733a2f2f7261772e6769746875622e636f6d2f646f616269742f7375626c696d652d746578742d69636f6e2f6d61737465722f5375626c696d65546578745f323536783235367833322e706e67)

* [简单描述](#jdms)
* [markdown-preview](#mp)
* [Emmet](#Emmet)
* [Sublime-HTMLPrettify](#Sublime-HTMLPrettify)
* [PlainTasks](#PlainTasks)
* [colorpicker](#colorpicker)
* [YUI Compressor](#YUI-Compressor)
* [sublime-text-2-clipboard-history](#sublime-text-2-clipboard-history)


<h2 id="jdms">简单描述</h2>

**Sublime Text**作为一个强大的代码编辑器，我除了用它来当前端的代码IDE外，我还很喜欢用它来做MarkDown文档的编辑器。它唯一的缺点就是不能同步显示MD文档，每次要看它总要输出到浏览器才能看最终的效果。好在一般都是写完几个段落之后我才看一次，所以不是很碍事。

MarkDown的好处就是代码少，排版强大，可以很简单的做连同目录在内的文档，我比较喜欢用它来写文章、记笔记等。

>Markdown 是一种轻量级标记语言，创始人为约翰·格鲁伯（John Gruber）。它允许人们“使用易读易写的纯文本格式编写文档，然后转换成有效的 XHTML（或者 HTML）文档”。这种语言吸收了很多在电子邮件中已有的纯文本标记的特性。

以下就是我为了Sublime Text做一个备忘的笔记。

<h2 id="mp">markdown-preview</h2>

既然我是那么的喜欢Markdown，那么就最先来介绍这个插件吧，这个插件的地址在[点击这里](https://github.com/revolunet/sublimetext-markdown-preview)，从这个GIT我们可以看到一些简单的介绍，包括排版、插入代码、图像甚至Emoji 表情 :+1:

这个插件使用非常简单，几乎可以不对它做任何设置！
* 新建：<kbd>Ctrl</kbd>+<kbd>N</kbd> 新建文档后，<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd>输入`SMD`,设置为MarkDown文档。
* 浏览:<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+`MPP`，在浏览器浏览MarkDown文档。

<kbd>Ctrl</kbd>+<kbd>B</kbd>可以生成HTML文档

如果你对它生成的CSS有不满意，你还可以修改其CSS文件，在[Markdown GIT页面](https://github.com/riku/Markdown-Syntax-CN)里面找到`markdown.css`文件，复制好以后进行修改保存在本地硬盘，在`Settings-User`里面写入`{ "css": "d:md.css"}`

<h2 id="Emmet">Emmet</h2>

>Emmet 可以快速的编写 HTML、CSS 以及实现其他的功能。它根据当前文件的解析模式来判断要使用 HTML 语法还是 CSS 语法来解析。

**Emmet**这个插件要说的话就太长太长了,就放一个简单代码页面在这里就好了。
[Emmet GIT](https://github.com/sergeche/emmet-sublime)
[更多语法](http://emmet.io/)


<h2 id="Sublime-HTMLPrettify">Sublime-HTMLPrettify</h2>
**Sublime-HTMLPrettify** HTML,CSS,JS格式标准化插件，可以生成非常漂亮的代码。需要none.js支持。
>一款集成了格式化（美化）html、css、js三种文件类型的插件，即便html,js写在PHP文件之内。插件依赖于nodejs，因此需要事先安装nodejs，然后才可以正常运行。插件安装完成后，快捷键ctrl+shift+H完成当前文件的美化操作。
[Sublime-HTMLPrettify GIT](https://github.com/victorporof/Sublime-HTMLPrettify)

<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>h</kbd> 自动生成

<h2 id="PlainTasks">PlainTasks</h2>
**PlainTasks**是我很喜欢的一个插件，ST3有很多插件可以等同于一个软件使用的，PlainTasks就可以算一个。PlainTasks是一个列表任务管理插件，可以让你一边在ST3工作，一边用ST计划任务。

[PlainTasks GIT](https://github.com/aziz/PlainTasks)

<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd> 输入 `Tasks: New document` 新建文件
<kbd>Ctrl</kbd>+<kbd>Enter</kbd>新建任务
<kbd>Ctrl</kbd>+<kbd>D</kbd>完成任务

其它参阅GIT文档。

<h2 id="colorpicker">colorpicker</h2>
**colorpicker**调色器，一般在编辑CSS文档的时候就需要。
[colorpicker GIT](https://github.com/weslly/ColorPicker)
<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>C</kbd> 打开

<h2 id="YUI-Compressor">YUI Compressor</h2>

[YUI Compressor GIT](https://github.com/leon/YUI-Compressor)

>压缩JS和CSS文件，按<kbd>F7</kbd>键后，若压缩当前文件（demo.js），则压缩后的文件（demo.min.js）保存在该文件的同级目录，需要安装java的JDK。

<h2 id="sublime-text-2-clipboard-history">sublime-text-2-clipboard-history</h2>
这个软件在sublime text 3就找不到安装，需要自己去[sublime-text-2-clipboard-history GIT](https://github.com/kemayo/sublime-text-2-clipboard-history)下载安装，<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>V</kbd>可调出clipboard历史记录面板，多次复制粘贴非常好用~~

>有了这个插件，便可方便使用sublime text 3里的粘贴板历史记录内容，快捷键<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>V</kbd>可调出该历史记录面板，按方向键选择想要粘贴的历史记录。不过这是sublime text 2下的插件，<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>D</kbd>清除粘贴板历史记录好像不能生效，不过重启sublime也可清除粘贴板的历史记录。
