> 作者：Sunirse  
> 时间：2022-05-20 17:20

### 从零开始安装Alpine

-   下载AlpineWSL发行包：

Why Alpine?

我同时使用[AlmaLinux](https://link.zhihu.com/?target=https%3A//github.com/AlmaLinux/wsl-images)和[CentOS 9-stream](https://link.zhihu.com/?target=https%3A//github.com/mishamosher/CentOS-WSL)等同样非常优秀的WSL发行包，但此时，我的首要需求是使用[Crystal Language](https://link.zhihu.com/?target=https%3A//crystal-lang.org/install/on_alpine_linux/)并能够使用其静态编译应用程序的功能。基于此需求，必须选用[基于muslc标准库构建的Linux](https://link.zhihu.com/?target=https%3A//wiki.musl-libc.org/)发行版，其中包括[Alpine Linux](https://link.zhihu.com/?target=https%3A//github.com/yuk7/AlpineWSL)、[Void Linux](https://link.zhihu.com/?target=https%3A//github.com/am11/VoidMuslWSL)以及[Kiss Linux](https://link.zhihu.com/?target=https%3A//github.com/mmatongo/KISSWSL)，我们可以很轻松获得它们的WSL发行包。

-   配置WSL环境
-   使用管理员身份打开PoweShell，按下Win+X，点击“Windows PowerShell (管理员)”。
-   请注意，不要点击“Windows PowerShell”，一定要点击带有(管理员)后缀的，这样才能用管理员身份启动。
-   开启WSL服务，将以下命令复制粘贴到控制台，然后按回车运行

```
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

-   开启虚拟机特性

```
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

-   重启电脑：请一定要重启，否则无法继续下面的操作。
-   更新WSL内核，下载[64位的Linux内核升级包](https://link.zhihu.com/?target=https%3A//wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)，双击安装下载好的安装包。
-   将WSL2设为默认启动版本，打开控制台，运行以下命令。

```
wsl --set-default-version 2
```

-   安装WSL发行包：将下载好的Alpine.zip压缩包中的所有文件解压缩到同一目录，右键以管理员身份运行Alpine.exe来解压缩rootfs.tar.gz并注册到WSL，至此AlpineWSL安装完成。
-   Alpine.exe是用于注册WSL的实例名称，如果重命名Alpine.exe，则可以使用其他名称进行注册或进行多次安装。
-   命令行启动配置：
-   使用-l命令，可以列出已安装的WSL发行版。

-   附加-v命令，可以同时显示所有分发的详细信息。

-   附加-o命令，可以列出能够使用wsl --install -d进行一键装载的WSL发行版。

```
wsl -l -o wsl --install -d kali-linux
```

-   如果在安装AlpineWSL前安装了其他WSL发行版，那么可以使用-s命令将Alpine设置为默认启动的发行版。

-   Alpine为当前注册WSL的实例名称。
-   使用--status命令，可以显示WSL子系统的状态。

-   使用--update命令，可以将WSL2的内核更新到最新版本。

-   使用--rollback命令，可以将WSL2的内核还原到先前版本。

-   使用-d命令，可以指定启动已安装的任意发行版。

-   使用--cd命令，可以将指定目录设置为当前工作目录。
-   如果使用了~，则将使用Linux用户的主页路径。如果路径以/字符开头，将被解释为绝对Linux路径。否则，该值一定是绝对Windows路径。

-   相当于在WSL的shell中执行cd $home命令。
-   运行Linux二进制文件的参数：
-   如果未提供命令行，在命令行中输入wsl将启动默认Shell。

-   使用-e命令，可以在不使用默认Shell的情况下执行指定的命令。

-   也可以在wsl后面输入Linux命令，默认将命令按原样传入WSL的默认Shell中执行。

![](https://pic3.zhimg.com/v2-423512d9cd899367f9e8e7f50bd28236_b.jpg)

AlpineWSL

在打开的bash中输入命令$SHELL可以查看当前Linux系统的默认Shell。Linux的默认信息存储在/etc/passwd  
文件中，可以通过修改该文件并重启WSL的方法进行修改。

-   用于管理WSL发行包的参数：
-   使用--export命令，可以将已安装的WSL发行版导出到.tar压缩文件。

```
wsl --export Alpine B://Alpine.tar
```

-   使用--import命令，可以将指定的.tar  
    压缩文件作为WSL发行包导入。

```
wsl --import Apline C://Windows//System32 B://Alpine.tar
```

-   使用--unregister命令，可以注销WSL发行版并删除其根文件系统。

### Alpine镜像源配置

-   Alpine默认的源地址在/etc/apk/repositories文件中，一般情况下，将[http://dl-cdn.alpinelinux.org/](https://link.zhihu.com/?target=http%3A//dl-cdn.alpinelinux.org/)替换为[http://mirrors.ustc.edu.cn/](https://link.zhihu.com/?target=http%3A//mirrors.ustc.edu.cn/)即可。
-   可以使用如下命令：

```
# 中科大
sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories
# 清华大学
sed -i 's/dl-cdn.alpinelinux.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apk/repositories
# 上海交大
sed -i 's/dl-cdn.alpinelinux.org/mirrors.sjtug.sjtu.edu.cn/g' /etc/apk/repositories
# 阿里云
sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories
```

-   也可以直接编辑/etc/apk/repositories文件。以下是v3.16版本的参考配置：  
    
-   中科大

```
https://mirrors.ustc.edu.cn/alpine/v3.16/main
https://mirrors.ustc.edu.cn/alpine/v3.16/community
```

-   兰州大学

```
https://mirror.lzu.edu.cn/alpine/v3.16/main
https://mirror.lzu.edu.cn/alpine/v3.16/community
```

-   北京外国语大学

```
https://mirrors.bfsu.edu.cn/alpine/v3.16/main
https://mirrors.bfsu.edu.cn/alpine/v3.16/community
```

-   中国互联网信息中心

```
https://mirrors.cnnic.cn/alpine/v3.16/main
https://mirrors.cnnic.cn/alpine/v3.16/community
```

-   清华大学

```
https://mirrors.tuna.tsinghua.edu.cn/alpine/v3.16/main
https://mirrors.tuna.tsinghua.edu.cn/alpine/v3.16/community
```

-   上海交大

```
https://mirrors.sjtug.sjtu.edu.cn/alpine/v3.16/main
https://mirrors.sjtug.sjtu.edu.cn/alpine/v3.16/community
```

-   北京交通大学

```
https://mirror.bjtu.edu.cn/alpine/v3.16/main
https://mirror.bjtu.edu.cn/alpine/v3.16/community
```

-   阿里云

```
https://mirrors.aliyun.com/alpine/v3.16/main
https://mirrors.aliyun.com/alpine/v3.16/community
```

-   腾讯云

```
https://mirrors.cloud.tencent.com/alpine/v3.16/main
https://mirrors.cloud.tencent.com/alpine/v3.16/community
```

-   大连东软信息学院

```
https://mirrors.neusoft.edu.cn/alpine/v3.16/main 
https://mirrors.neusoft.edu.cn/alpine/v3.16/community
```

-   或者可以使用latest-stable指向最新的稳定版本：

```
https://mirrors.ustc.edu.cn/alpine/latest-stable/main
https://mirrors.ustc.edu.cn/alpine/latest-stable/community
```

-   更改完/etc/apk/repositories文件后，运行apk update更新索引以生效。

### apk包管理工具

### apk常用命令

-   apk update 更新

-   apk search 查找

```
apk search                      #查找所有可用软件包
apk search -v                   #查找所有可用软件包及其描述内容
apk search -v curl              #通过软件包名称查找软件包
apk search -v -d 'docker'       #通过描述文件查找特定的软件包
```

-   apk add 安装

```
apk add openssh                  #安装一个软件
apk add git crystal ruby curl    #安装多个软件
apk add --no-cache -U apache2    #不使用本地镜像源缓存，相当于先执行update，再执行add
```

-   apk info 查看已安装

```
apk info                          #列出所有已安装的软件包
apk info -a zlib                  #显示完整的软件包信息
apk info --who-owns /sbin/lbu     #显示指定文件属于的包
```

-   apk upgrade 升级

```
apk upgrade                        #升级所有软件
apk upgrade openssh                #升级指定软件
apk upgrade openssh openntp vim    #升级多个软件
apk add --upgrade busybox          #指定升级部分软件包
```

-   apk del 卸载

### github镜像代理加速

-   常用加速站点
-   [https://fastgit.org/](https://link.zhihu.com/?target=https%3A//fastgit.org/)
-   [https://cnpmjs.org/](https://link.zhihu.com/?target=https%3A//cnpmjs.org/)
-   [https://gitclone.com/](https://link.zhihu.com/?target=https%3A//gitclone.com/)
-   [https://mirror.ghproxy.com/](https://link.zhihu.com/?target=https%3A//mirror.ghproxy.com/)  
    
-   当前用户全局git加速

```
git config --global url."https://gitclone.com/".insteadOf https://
```

-   上行命令会将镜像代理的前缀写入~/.gitconfig文件中  
    
-   多用户全局git加速

```
git config --system url."https://gitclone.com/".insteadOf https://
```

-   上行命令会将前缀写入/etc/gitconfig文件中，当前系统所有用户都可以使用镜像加速。

### 在WSL中运行代码

> Crystal语言截止至当前尚未发布可以运行在Windows系统上面的安装包，通过WSL可以很方便地在Win11系统中写Crystal代码。在集成了musl C标准库构建的Linux WSL中，可以使用Crystal静态编译.cr文件，构建可跨设备执行的应用程序。

```
crystal build main.cr --release --link-flags -static
```

### Sublime Text

Crystal编译系统

```
{ //Crystal.sublime-build
    "cmd": ["crystal", "run", "--no-color", "$file"],
    "file_regex": "^Error in (?<filename>.+\\.cr):(?<line_number>[0-9]*):?(?<column_number>[0-9]*): (?<message>.+)$",
    "selector": "source.crystal",
    // "quiet": true,
    "windows": {
        "cmd": ["bash", "-c", "crystal $file_name --no-color"]
    },
    "working_dir": "${file_path}",
    "variants":
    [
        {
            "name": "Run in cmd",
            "windows": {
                // "cmd" : ["start", "bash", "-c", "crystal $file_name &&echo -e \"\\npress 'enter' to continue ...\" &&read"],
                "shell_cmd": "start cmd /c \"wsl crystal $file_name &echo. &pause\""
            },
            "quiet": true,
            "shell": true
        },
        {
            "name": "Build --static",
            "windows": {
                "cmd" : ["wsl", "crystal", "build", "$file_name", "--release", "--link-flags", "-static"]
            },
            "shell": true
        },
        {
            "name": "Run only",
            "windows": {
                "cmd" : ["bash", "-c", "./$file_base_name"]
            },
            "shell": true
        }
    ]
}
```

Ruby和mRuby编译系统

```
{ //Ruby.sublime-build
    "cmd": "bash -c \"ruby ${file_name}\"",
    "file_regex": "^(...*?):([0-9]*):?([0-9]*)",
    "selector": "source.ruby",
    "working_dir": "${file_path}",
    "shell": true,
    "variants":
    [
        {
            "name" : "Run in cmd",
            "quiet": true,
            "cmd" : ["start", "bash", "-c", "ruby ${file_name} &&echo -e \"\\nPress 'Enter' to continue ...\" &&read"],
            // "cmd" : "start cmd /C \"wsl ruby ${file_name} &&echo. &&pause\""
        },
        {
            "name" : "mRuby",
            "cmd" : "bash -c \"mruby ${file_name}\"",
        },
        {
            "name" : "mRuby in cmd",
            "quiet": true,
            "cmd" : ["start", "bash", "-c", "mruby ${file_name} &&echo -e \"\\nPress 'Enter' to continue ...\" &&read"],
            // "cmd" : "start cmd /C \"wsl ruby ${file_name} &&echo. &&pause\""
        }
    ]
}
```

使用类似的方法，也可以借助WSL在安装在Windows系统中的Sublime Text中创建安装在WSL上的其他语言如Go、Java、Python、C/C++等的编译系统。

### Textadept

和Sublime Text一样，基于Scintilla开发的Textadept是一个快速、专业、高度可定制的文本编辑器，使用可扩展语言 lua 进行对框架进行功能性增强，支持多国语言，同时拥有许多可供选择的扩展，其中包括：[Markdown](https://link.zhihu.com/?target=https%3A//github.com/rgieseke/ta-markdown)、[Multiedit](https://link.zhihu.com/?target=https%3A//github.com/Pulgovisk/Multiedit)、[Golang](https://link.zhihu.com/?target=https%3A//github.com/rgieseke/textadept-go)、[format](https://link.zhihu.com/?target=https%3A//github.com/orbitalquark/textadept-format)以及整合在[官方模组](https://link.zhihu.com/?target=https%3A//orbitalquark.github.io/textadept/)中的许多优质扩展。 以下是我的配置文件：

```
-- init.lua
require('spellcheck')
require('format')
require('export')
require('golang')
require('multiedit')
view:set_theme(not CURSES and 'gotime' or 'term')
keys['ctrl+alt+j'] = require('format').paragraph
view.h_scroll_bar, view.v_scroll_bar =  false, true
ui.find.highlight_all_matches = true
textadept.editing.highlight_words = textadept.editing.HIGHLIGHT_SELECTED
textadept.editing.auto_enclose = true

-- Always strip trailing spaces, except in patch files.
events.connect(events.LEXER_LOADED, function(name)
  textadept.editing.strip_trailing_spaces = name ~= 'diff'
end)
buffer.tab_width = 4
buffer.use_tabs  = true
buffer.back_space_un_indents = true
view.view_ws  = view.WS_INVISIBLE

textadept.run.run_commands.crystal='wsl crystal "%f"'
textadept.run.compile_commands.crystal='wsl crystal build "%f" --release --link-flags -static'
textadept.run.run_commands.ruby='wsl ruby "%f"'
textadept.run.compile_commands.ruby='wsl mruby "%f"'
```

[gotime主题](https://link.zhihu.com/?target=https%3A//www.123pan.com/s/wOg9-oDxNh)是我使用[ThemeCreator网站](https://link.zhihu.com/?target=https%3A//mswift42.github.io/themecreator/)制作的配色方案。

![](https://pic2.zhimg.com/v2-ca30077431a651e9408624ae2242da6d_b.jpg)

gotime主题

### Kiss Linux配置

### 配置软件包库

[https://github.com/aicsx/kiss-repo](https://link.zhihu.com/?target=https%3A//github.com/aicsx/kiss-repo) [https://github.com/st3r4g/kiss-repo](https://link.zhihu.com/?target=https%3A//github.com/st3r4g/kiss-repo) [https://github.com/ehawkvu/kiss\-personal](https://link.zhihu.com/?target=https%3A//github.com/ehawkvu/kiss-personal)(编程语言) [https://github.com/mdartmann/mkiss](https://link.zhihu.com/?target=https%3A//github.com/mdartmann/mkiss)

```
export KISS_PATH=/root/repo/core:/root/repo/extra:/root/community
```

### 配置编译

```
// NOTE: The 'O' in '-O3' is the letter O and NOT 0 (ZERO).
export CFLAGS="-O3 -pipe -march=native"
export CXXFLAGS="$CFLAGS"
// NOTE: '4' should be changed to match the number of threads.This value can be found by running 'nproc'.
export MAKEFLAGS="-j4"
```

-   To play it safe use -O2 or -Os instead of -O3.  
    
-   If memory is low, omit -pipe. This option speeds up compilation but may use more memory.  
    
-   If the intention is to transfer packages between machines, omit -march=native. This option tells the compiler to use optimizations unique to the processor's architecture.  
    
-   The -jX option should match the number of CPU threads available. This can be omitted, however builds will then be limited to a single thread.  
    

### 参考