# scoop本地镜像下的优化
	在gogs上增加了scoop相关的repo mirror之后，可以指向本地repo url，看看速度提升情况

[[scoop最新安装教程2021_路人夹饼的博客-程序员资料_scoop安装 - 程序员资料]]

- scoop配置变更
	- SCOOP_REPO的设置
	scoop config SCOOP_REPO http://nas.home:3010/lukesampson/scoop
	scoop update

	- repo url变更，本地化
```
	git -C "E:\scoop\buckets\main" remote set-url origin https://hub.fastgit.org/ScoopInstaller/Main.git
```
		也可以先进入到对应的main、extras目录，git remote set-url 。。。
		查看之前origin url：git config --get remote.origin.url
	scoop update
	速度提升的确很大
	- buckets.json变更
	$HOME\scoop\apps\scoop\current\buckets.json
	 	修改为本地repo url：nas.home:3010/....
	 scoop bucket add nirsoft
	 scoop update
		 速度不错的
# firefox的安装
	windows下安装的firefox和scoop安装的firefox使用不同的profile
	用安装完成之后的应用：firefox profile manager，来选择一下使用的profile
	之后就做一次sync，把其他设备上的设置同步过来
	就恢复使用环境了
	