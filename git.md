windows 系统git全部设置
===
+ 安装git
+ 安装SourceTree
+ 安装JAVA
+ 安装配置gitblit
+ 安装github

### 安装git
git下载：http://msysgit.github.io/，安装后初始化设置：
~~~
$ git config --global user.name "Alexzeng"
$ git config --global user.email “Alexzeng@msn.com”

~~~

### 安装SourceTree

### 安装JAVA
下载Java,下载地址：http://www.java.com/zh_CN/

配置Java环境变量.
       右键”计算机” => ”属性” => ”高级系统设置” => ”高级” => “环境变量” => “系统变量”。
1. 新建：
变量名：JAVA_HOME
变量值：D:\Program Files (x86)\Java\jdk1.6.0_21【具体要根据你自己的安装路径，我的是安装在D盘的】
2.  新建：
变量名：CLASSPATH
变量值：.;%JAVA_HOME%/lib/dt.jar;%JAVA_HOME%/lib/tools.jar

3. 添加：找到PATH变量，选择编辑。把%JAVA_HOME%/bin;%JAVA_HOME%/jre/bin添加到”变量值”的结尾处。

### 安装配置gitblit

1. 到http://gitblit.com/ 下载Gitblit
2. 解压缩下载的压缩包即可，无需安装。如d:\gitblit
3. 找到Git目录下的data文件下的gitblit.properties文件，“记事本”打开。
4. 找到git.repositoriesFolder(资料库路径)，赋值为第2步创建好的文件目录(d:\gitblit)。
5. 找到server.httpPort，设定http协议的端口号(如：10101)
6. 找到server.httpBindInterface，设定服务器的IP地址。这里就设定你的服务器IP。（如果本机设置就使用127.0.0.1）
7. 找到server.httpsBindInterface，设定为localhost
8. 保存，关闭文件。找到bitblit目录中的gitblit.cmd文件，双击运行。
9. 设置以Windows Service方式启动Gitblit.1. 在Gitblit目录下，找到installService.cmd文件，用记事本打开。
10. 修改 ARCH
32位系统：SET ARCH=x86
64位系统：SET ARCH=amd64
添加 CD 为程序目录
SET CD=D:\Git\Gitblit-1.6.0 (你的实际目录)
  修改StartParams里的启动参数，给空就可以了。
11. 双击Gitblit目录下的installService.cmd文件(以管理员身份运行)。
