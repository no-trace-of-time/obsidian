## 安装环境

1.  Windows 10 专业版 
2.  确保是powershell 5（及其以上） PowerShell  5  查看当前版本    $PSVersionTable.PSVersion
3.  更改脚本执行的策略    set-executionpolicy remotesigned -s cu

## 安装

1.  设置scoop环境变量（备注：这里设置环境变量第三个参数User表示用户级别，Machine表示系统级别。Machine没权限的话，可以手动去环境变量设置。）
    1.  $env:SCOOP='E:\\scoop'
    2.  \[Environment\]::SetEnvironmentVariable('SCOOP',$env:SCOOP,'User')
2.  设置scoop global 环境变量
    1.  $env:SCOOP\_GLOBAL='D:\\ScoopGlobalApps'
    2.  \[Environment\]::SetEnvironmentVariable('SCOOP\_GLOBAL',$env:SCOOP\_GLOBAL,'User')
3.  安装命令
    1.  iex (new-object net.webclient).downloadstring('https://get.scoop.sh')  或者  iwr-useb get.scoop.sh|iex  （但是两个都不行emm）
    2.  别急有办法打开电脑的hosts 
        1.  C:\\Windows\\System32\\drivers\\etc  找到 hosts  输入 199.232.4.133 raw.githubusercontent.com  在保存
        2.  执行 iex (new-object net.webclient).downloadstring('https://raw.githubusercontent.com/lukesampson/scoop/master/bin/install.ps1')
        3.  iex (new-object net.webclient).downloadstring('https://raw.githubusercontent.com/lukesampson/scoop/master/bin/install.ps1'
        4.  测试有没有安装成功 输入 scoop help 看就可以了  输入一下信息就可以了
        5.  ![](https://img-blog.csdnimg.cn/20210424165203269.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTk5Mjc3Mg==,size_16,color_FFFFFF,t_70)
4.  安装好之后推荐 换成国内的安装源  因为访问githup的话速度太慢 先更换源
    1.  scoop config SCOOP\_REPO https://gitee.com/squallliu/scoop
    2.  scoop update
5.  更换 bucket 源
    1.  scoop install git
    2.  git -C "E:\\scoop\\buckets\\main" remote set-url origin https://hub.fastgit.org/ScoopInstaller/Main.git
    3.  如果要加行新的源  scoop bucket add extras https://hub.fastgit.org/lukesampson/scoop-extras.git 
    4.  获取修改原来的      git -C "E:\\scoop\\buckets\\main" remote set-url origin https://hub.fastgit.org/lukesampson/scoop-extras.git   都可以好吧
    5.   如果 你输入  git -C "E:\\scoop\\buckets\\main" remote set-url origin https://hub.fastgit.org/ScoopInstaller/Main.git  这个的时候有问题 或者报错了 别急 先百度
    6.  我是这样操作的  先进入  E:\\scoop\\buckets\\main 在
    7.  ![](https://img-blog.csdnimg.cn/20210424170511955.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTk5Mjc3Mg==,size_16,color_FFFFFF,t_70)
    8.  ![](https://img-blog.csdnimg.cn/20210424170552820.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTk5Mjc3Mg==,size_16,color_FFFFFF,t_70)
    9.  初始化一下在运行 git -C "E:\\scoop\\buckets\\main" remote set-url origin https://hub.fastgit.org/ScoopInstaller/Main.git
    10.  如果还是报错也别急 **git remote add origin "**https://hub.fastgit.org/ScoopInstaller/Main.git**" 然后再**
    11.  git -C "E:\\scoop\\buckets\\main" remote set-url origin https://hub.fastgit.org/ScoopInstaller/Main.git 这个时候应该没有没有问题了 有问题 留言 再交流。。。。

##   推荐一些强大的软件

1.  先安装库
2.  ```
    scoop bucket add extras
    ```
    
3.  scoop install aria2    
4.  scoop install nvm
5.  ```
    scoop install screentogif 
    ```
    
6.  ![](https://img-blog.csdnimg.cn/2021042417243367.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTk5Mjc3Mg==,size_16,color_FFFFFF,t_70)
7.  安装成功的例子 你可以查看已经安装的软件  scoop list
8.  ![](https://img-blog.csdnimg.cn/20210424172519591.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTk5Mjc3Mg==,size_16,color_FFFFFF,t_70)
9.  Shadowsocksrr、Clash、FSCapture、UninstallTool、Ditto、Notepad2-mod、Dism++、2345看图王等
10.  scoop bucket add echo https://github.com/echoiron/echo-scoop  肯定是不行的 用  https://hub.fastgit.org/echoiron/echo-scoop 
11.  固定前缀 https://hub.fastgit.org/       家你要的源 比如   echoiron/echo-scoop    ScoopInstaller/Main.git     

emm 如果你是一名java程序员推荐还是 手动配置java 别用这个 建议 

补充一下 如果说你是一个新服务器的情况下面 ,   使用“1”个参数调用“DownloadString”时发生异常:“请求被中止: 未能创建 SSL/TLS 安全通道。”

![](https://img-blog.csdnimg.cn/20210512153342914.png)

遇到这个错误不要急运行一个命令

\[Net.ServicePointManager\]::SecurityProtocol = \[Net.SecurityProtocolType\]::Tls12;

就可以了

原因是因为:使用HttpWebRequest请求https链接时，无法访问的问题，设置ServicePointManager.SecurityProtocol安全协议

2022-05-14
# [Git问题解决：warning: Pulling without specifying how to reconcile divergent branches is discouraged.](https://www.cnblogs.com/zhangyouwu/p/15667753.html)

使用git pull命令出现以下的警告：

warning: Pulling without specifying how to reconcile divergent branches is
discouraged. You can squelch this message by running one of the following
commands sometime before your next pull:

  git config pull.rebase false  # merge (the default strategy)
  git config pull.rebase true   # rebase
  git config pull.ff only       # fast-forward only

You can replace "git config" with "git config --global" to set a default
preference for all repositories. You can also pass --rebase, --no-rebase,
or --ff-only on the command line to override the configured default per
invocation.

该警告的中文版本文案描述如下：

warning: 不建议在没有为偏离分支指定合并策略时执行pull操作。 
您可以在执行下一次pull操作之前执行下面一条命令来抑制本消息：

git config pull.rebase false  # 合并（默认缺省策略）
git config pull.rebase true   # 变基
git config pull.ff only       # 仅快进

您可以将 "git config" 替换为 "git config --global" 以便为所有仓库设置
缺省的配置项。您也可以在每次执行 pull 命令时添加 --rebase、--no-rebase，
或者 --ff-only 参数覆盖缺省设置。

解决办法：

若无特殊需求，执行命令

git config pull.rebase false

修改remote url之后，可能会scoop update出现以上错误，用
```
git config pull.rebase false
```
搞定即可

