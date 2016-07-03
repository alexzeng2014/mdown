# 学会Atom的基础

## 目录

- Markdown简明语法
- 学会将Atom中的项目发送到github
- 如何修改快捷键
- 如何本地化安装插件
- 将代码显示到浏览器里面去
- 代码提示，自动提醒插件
- 任务列表：Toggle Markdown Task
- 颜色 补全路径插件
- Atom 备份插件
- 


## Atom 备份插件

sync-settings可以同步Atom的设置文件,自定义快捷键,用户风格,初始化脚本及代码片段,还支持已安装的插件同步。绝对神器！！

1. 进入设置中心找到该插件,进去setting
2. 打开自己的github创建一个personal access token – 然后复制生成的token序列,粘贴到插件的setting里面的(42ba555f6f2008e8063707c8d394105eef9c8140)
3. 再打开github的gist服务,创建一个gist–复制生成gistID（3f89fe827603e169e9e58ca73fe94902）,也粘贴到二步设置里面
4. 在文档编辑页面,按下全局命令搜索面板(Ctrl+shift+p) 搜索sync- ,会有可选项

  ```
  sync-settings:backup – 这条命令是备份当前的配置
  sync-settings:restore – 这条命令是回复配置,是直接覆盖的;
  sync-settings:view-backup – 这条是当你执行备份后到线上查询你的备份的,也就是到你的gist code里面
  sync-settings:check-backup – 这条是查询最后一次是否正常
  ```

## Toggle Markdown Task

安装Toggle Markdown Task，建立一个基于Markdown文档的Todo列表。

```
- [ ] 任务类别：

Clrd+d 打勾

keymaps 文件中：

'atom-workspace atom-text-editor:not(.mini)':
  'ctrl-d': 'toggle-markdown-task:toggle'
```

## Markdown简明语法

mdown在Atom应用起来简直爽快，利用快捷键 **Ctrl+Shift+M** 可以同步浏览，而且界面巨爽！！！

```
标题：
#h1级标题
##h2级标题
###h3级标题
####h4级标题
#####h5级标题
######h6级标题

分割线：三个以上的短线 即可作出分割线

----

超链接：[连接名称](网址 , 标题)
[我是链接名](http://www.izhangbo.cn, "我是标题")
[<i class="icon-refresh"></i> 点我刷新](/sonfilename/)

另一种超链接写法：[链接名][链接代号]
[here][3]
然后在别的地方定义 3 这个详细链接信息，
[3]: http://www.izhangbo.cn "聚牛团队"

直接展示链接的写法：<http://www.izhangbo.cn>

键盘键
<kbd>Ctrl+[</kbd> and <kbd>Ctrl+]</kbd>

code格式：反引号
Use the `printf()` function.

``There is a literal backtick (`) here.针对在代码区段内插入反引号的情况``

强调：
*斜体强调*
**粗体强调**

图片
![Alt text](http://www.izhangbo.cn/wp-content/themes/minty/img/logo.png "Optional title")

使用 icon 图标文字
<i class="icon-cog"></i>

段落：以一个空行开始，以一个空行结束，中间的就是一个段落。

表格：

Item     | Value
-------- | ---
Computer | $1600
Phone    | $12
Pipe     | $1

无序列表：使用 - 加一个空格（）

- 无需列表1
- 无序列表2
- 无序列表3

有序列表：使用 数字 加一个英文句点

1\. 有序列表
2\. 有序列表
3\. 有序列表
4\. 有序列表
5\. 有序列表

换行缩进形成代码区块

    这里先换行，然后缩进4个空格，之后的内容便可以原样显示了，适合用于显示代码内容。直到文本结束或最后一个存在缩进的行为止。    

块引用
>给引用的文本开始位置都加一个 '>'，
>便可组成一个块引用。在块引用中，可以结合
>其他markdown元素一块使用，比如列表。
>**强调**
也可以只在第一行加大于号，其他位置不加。

>- 块引用里使用列表，需要和上面的内容隔开一个空行
>- 记得加空格哦。
```

## 如何本地安装Atom插件

插件安装：Settings-install，查找插件名称进行安装。但是手动安装插件速度更快，不知是不是因为墙的关系。 运行CMD

```
C:\Users\alexzeng>cd .atom

C:\Users\alexzeng\.atom>cd packages

C:\Users\alexzeng\.atom\packages>git clone https://github.com/mark-hahn/markdown-scroll-sync.git
Cloning into 'markdown-scroll-sync'...
remote: Counting objects: 177, done.
Receiving objects:  86% (153, reused 0 (delta 0), pack-reused 177
Receiving objects: 100% (177/177), 38.02 KiB | 0 bytes/s, done.
Resolving deltas: 100% (79/79), done.
Checking connectivity... done.

C:\Users\alexzeng\.atom\packages>cd markdown-scroll-sync

C:\Users\alexzeng\.atom\packages\markdown-scroll-sync>npm install
sub-atom@1.1.0 node_modules\sub-atom
└── jquery@2.2.4
```

手动安装成功后，需要重启Atom才能成功！

也可以改镜像直接安装：

> apm install atom-beautify

如果这个安装速度太慢，还可以如下： 在terminal下运行如下命令，避开防火墙设置:

```
apm config set strict-ssl false
修改registry到淘宝npm镜像

apm config set registry https://registry.npm.taobao.org
如果需要删除该镜像设置，使用：apm config delete registry
```

## 上传到git服务器

下载Atom插件：git-control；就是命令行的GUI版本,有些类似sourcetree,但是不如它强大,日用满足

但是操作起来还是蛮方便的，**ALT+CTRL+O** 打开git-control,点击commit，然后再push 有时，会出现莫名的错误！特别是使用中文文件名。

## emmet

下载emmet插件,快速html代码。

**HTML**

```
html:5 或!：用于HTML5文档类型 —
html:xt：用于XHTML过渡文档类型 —
html:4s：用于HTML4严格文档类型 —
```

**id#|类.|属性[]|内容{},若是不带父元素,则默认为隐性生成(就近原则)**

```
  <!-简写格式我就放在注释里面啦啦!!-->
  <!--#test.test-->
  <div id="test" class="test">

  </div>

  <!-- p#test.test  -->
  <p id="test" class="test"></p>

  <!-- a[href="http://www.baidu.com"]{文本内容--我是百度} -->
  <a href="http://www.baidu.com">文本内容--我是百度</a>
```

**后代> | 兄弟+ | 上级^**

```
  <!-- div>ul>li 后代 -->
  <div>
    <ul>
      <li></li>
    </ul>
  </div>

  <!-- div>p+p  兄弟-->
  <div>
    <p></p>
    <p></p>
  </div>

  <!-- div>p>ul>li>^span+b  上级-->
  <div>
    <p>
      <ul>
        <li></li>
        <span></span>
        <b></b>
      </ul>
    </p>
  </div>
```

**分组()/乘法*/自增$/自减$@-/起序$@数字**

```
  <!-- div>ul>(li>a)*2 -->
  <div>
    <ul>
      <li><a href=""></a></li>
      <li><a href=""></a></li>
    </ul>
  </div>

  <!-- div>ul>(li>a{文本$$})*2 -->
  <!--$是匹配数字,一个代表1开始,两个01开始,依次001-->
  <div>
    <ul>
      <li><a href="">文本01</a></li>
      <li><a href="">文本02</a></li>
    </ul>
  </div>

  <!-- div>ul>(li>a{文本$@-})*3 -->
  <!-- @-代表启用自减,降序排列   -->
  <div>
    <ul>
      <li><a href="">文本3</a></li>
      <li><a href="">文本2</a></li>
      <li><a href="">文本1</a></li>
    </ul>
  </div>

  <!-- div>ul>(li>a{文本$@2})*5 -->
  <!-- $@number 代表几号开始出现数字  -->
  <div>
    <ul>
      <li><a href="">文本2</a></li>
      <li><a href="">文本3</a></li>
      <li><a href="">文本4</a></li>
      <li><a href="">文本5</a></li>
      <li><a href="">文本6</a></li>
    </ul>
  </div>
```

## 手动修改快捷键

修改编辑器的快捷键：如果快捷发生冲突，可以在Settings-keybindings中查看是哪个插件导致错误，使用以下的语句在keymap.cson（File-keymap），使用unset!对造成冲突的快捷键停止使用。

```
   'atom-text-editor:not([mini])':
     'ctrl-shift-m': 'unset!'
```

## 代码提示，自动提醒插件

js,jquery的自动提醒插件。
