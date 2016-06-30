学会Atom的基础
====
### 目录
+ 学会写mdown
+ 学会将Atom中的项目发送到github
+ 将代码显示到浏览器里面去
+ 如何修改快捷键
+ 如何本地化安装插件


mdown在Atom应用起来简直爽快，利用快捷键 **Ctrl+Shift+M** 可以同步浏览，而且界面巨爽！！！
Sublimetext的优点就是比Atom快很多。

插件安装：Settings-install，查找插件名称进行安装。

下载Atom插件：git-control；就是命令行的GUI版本,有些类似sourcetree,但是不如它强大,日用满足

但是操作起来还是蛮方便的，**ALT+CTRL+O** 打开git-control,点击commit，然后再push
有时，会出现莫名的错误！特别是使用中文文件名。

修改编辑器的快捷键：如果快捷发生冲突，可以在Settings-keybindings中查看是哪个插件导致错误，使用以下的语句在keymap.cson（File-keymap），使用unset!对造成冲突的快捷键停止使用。

~~~
 'atom-text-editor:not([mini])':
   'ctrl-shift-m': 'unset!'
   ~~~

下载emmet插件,快速html代码。
