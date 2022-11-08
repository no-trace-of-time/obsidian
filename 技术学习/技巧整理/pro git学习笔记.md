# 1.起步
# git基础
	git每次不是像cvs那样记录文件的差异，而是全部文件的一个快照
	一般只添加数据
	文件不是用文件名做索引，而是用hash值做索引
	文件区域：working directory/staging area/.git directory(repository)
## 配置
	配置文件位置
		global
			/etc/gitconfig
			$HOME/.gitconfig
			$HOME/.config/git/config
		loccal
			$PROJECT/.git/config
	命令
		git config --list
		git config -l
		git config <key>
			key = user.name
# 2 git基础
## 2.1 获取git仓库
	初始化
		git init
		git add ...
		git commit -m ...
	clone
		git clone <url>
		url可以有多种协议
			http/https
			git://
			ssh: user@server:path/to/repo.git
## 2.2 git基础-记录每次更新到仓库
	文件的状态：untracked/unmodified/modified/staged
	git status
	git add:实际是把文件当前版本放到了stage area
		如果add之后，有修改了文件，那么，后继commit的文件，并不是commit时刻working directory中的版本，而是add之后，在staging area里面的版本
	git status --short
	忽略文件：.gitignore
		文件 `.gitignore` 的格式规范如下：
```
-   所有空行或者以 ＃ 开头的行都会被 Git 忽略。
-   可以使用标准的 glob 模式匹配。
-   匹配模式可以以（/）开头防止递归。
-   匹配模式可以以（/）结尾指定目录。
-   要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反。
```
		GitHub 有一个十分详细的针对数十种项目及语言的 `.gitignore` 文件列表，你可以在 [https://github.com/github/gitignore](https://github.com/github/gitignore) 找到它.
	git diff: 查看尚未暂存的文件更新的部分
	git diff -cached/-staged: 查看已暂存内容的变更
	git commit
	跳过使用暂存区域
		git commit -a:所有已经跟踪过的文件都暂存起来，一起提交，跳过了git add步骤
	移除文件
		从此彻底删除文件
			rm <file>
			git rm <file>
			git commit
			从暂存区移除，然后提交
			git rm：记录下这个delete操作，commit之后，今后这个文件不再纳入版本管理了
			如果之前已经放到暂存区了，需要使用git rm -f，防止误删
		仅仅从暂存区删除，但是在working area保存内容
			git rm --cached <file>
			相当于把这些文件任然保持未跟踪的状态
```
		git rm log/\*.log
```
	移动文件
		mv 之后，仓库中并不体现这个改名操作
		git mv <file_from> <file to>:等价于
			mv <file old> <file new>
			git rm <file old>
			git add <file new>
## 2.3 查看提交历史
	git log
	git log -p ：显示每次提交的内容差异
	git log -p -2:仅显示最近两次提交
	git log --stat: 显示每次提交的简略统计信息
	git log --pretty=online
	git log --pretty=format:"%h %s" --graph
	常用选项
		--since
		--until
		--author
		--grep
		--all-match  满足所有条件，不然，就是满足任意之一条件即可
		-S<function_name>
## 2.4 撤销操作
	git commit --amend
		对暂存区内容重新提交，
			如果上次commit之后尚未做任何修改，快照保持不变，只是修改提交信息
			如果上次commit之后有新增内容，第二次提交将代替第一次提交
				git commit
				git add ...
				git commit --amend
				这样可以仅保留最后一次提交内容
	取消暂存的文件
		git reset HEAD <file>
			将<file>取消暂存，这样，如果之前add了这个file，结果就是取消add	
	撤销对文件的修改
		git checkout -- <file>
			实际就是重新从repo中取出file的原始内容
			**这是一个危险的命令，会丢失所有对file的修改操作**
			想保留的话，用分支方式来操作
			
## 2.5 远程仓库的使用
	git remote -v
	origin: 默认的服务器名称
	可以有多个remote
		多个合作者写作
		方便拉取其他用户的贡献
		可能有某些远程仓库的推送权限
	添加远程仓库
		git remote add <repo short name>  <url>
		
	随后，可以使用短名来拉取
		git fetch <short name>
	设置一个分支跟踪一个远程分支
		git clone会自动设置本地master分支跟踪orign/master
		git pull之后，会抓取并自动尝试合并到本地当前分支
	推送到远程仓库
		git push [remote name] [branch-name]
		git push origin master
	查看远程仓库
		git remote show [remote name]
	移除与改名
		git remote rename [old name] [new name]
		git remote rm [remote name]
			移除某个remote的引用	
## 2.6 打标签
	git tag
	git tag -l 'v1,8.5*'
	标签种类
		lightweight:轻量
			仅是一个对特定comit的ref
			git tag v1.4
		annotated：附注
			存储在git db中的一个完整对象，包括：owner name，email，datetime，info，gpg sig
			git tag -a v1.4 -m '.....'
	可以对过去的提交打标签
		git tag -a v1.2 <hash begin 8digit>
	默认git push不会把标签推送到远程
		可以手工指定
			git push origin v1.5
			git push origin --tags 一次推送多个
	检出标签
		tag不能类似branch那样来回移动，没法直接checkout
		可以在特定tag创建一个分支，然后checkout
			git checkout -b v2 v2.0.0
			但是，如果之后有commit，v2分支会向前移动，和最初的v2.0.0 tag就会有不同了
			
## 2.7 别名
	git config --global alias.co checkout
		今后可以用git co来执行checkout
	git config --global alias.last 'log -1 HEAD'
	git config --global alias.visual '!gitk'
		执行外部命令
	
			
# git分支
## 3.1 分支简介
	git add ...
		计算每个文件的hash
		当前版本文件的快照保存到git 仓库-blob对象
		hash加入到stage区域等待提交
	git commit ...
		计算每个子目录的hash
		在仓库中保存这些hash为树对象（记录目录结构和blob对象索引）
		创建一个提交对象，包含
			变动文件的hash：对应各自的blob对象
			各子目录hash
			指向这个树对象（项目根目录）的指针
		提交
			首次提交：
				提交对象
				树对象（初始提交的文件内容）
			后继提交
				树对象
				提交对象：包含上次commit的指针
	git的分支，仅仅是指向提交对象的一个可变指针
		默认分支名称：master
		每次提交是，自动向前移动
		master只是git init时默认创建的一个名字，和其他分支地位相同
	分支创建
		git branch testing
			仅仅就是在当前commit对象上创建一个指针
		HEAD:指向当前所在分支（本地分支）
			git log --decorate：可以查看HEAD指针位置
	分支切换
		git checkout [branch name]
	git log -oneline -decorate --graph --all:输出各分支指向的情况
	git分支本质
		仅是包含所指向hash（长度40）的文件
		所以创建一个新分支就相当于写入41个字节（hash+回车）
		故此高效
		文件内容，已经提前add时候，从stage放到仓库的blob文件中了
## 3.2 分支的新建与合并
	简单场景
		根据一新需求，创建一分支，开发,feature branch
			git checkout -b issue53
				等价于：git branch issue53;git checkout issue53
			... some fix ...
			git commit -a -m 'add xxxx [issue  53]'
		紧急问题需要处理，修订原功能的bug
			切换到production branch
				git checkout master
			新建一分支，在期中做修复，bugfix branch
				git checkout -b hotfix
				.. some fix...
				git commit -a -m 'fix xxx '
			ut测试完成
		切换到production branch，合并bugfix branch
			git checkout master
			git merge hotfix
		push merge到production branch
			git push master
			...
			此时hotfix分支已经不再需要，可以删除
				git branch -d hotfix
		切换回feature branch
			git chekcout issue53
		继续原有开发工作
			... some fix ...
			git commit -a -m 'finished ... [issue 53]'
		把开发内容合并回production branch
			git checkout master
			git merge issue53
			此时，对于有公共祖先的场景，做一个简单的三方合并
			有一个合并提交
			git branch -d issue53
	遇到冲突时的分支合并
			此时git无法进行自动的合并提交，等待手工完成
			修改冲突
			git add [files] : 标记冲突已经解决
			git mergetool ： 采用合适的可视化合并工具
			git commit -m 'merge branch issue53 ': 完成提交
	分支管理
			git branch -v
			git branch --merged/--no-merged
				--merged: 对于没有*号的分支，通常可以删除，因为工作内容已经合并到了另外一个分支了，不再需要了
			git branch --no-merged master:尚未合并到master分支的有哪些？
	分支开发工作流
		长期分支：稳定分支，master
				master一般落后前沿分支
				大型项目，还可以继续细分proposed/pu(proposed update)分支
		主题分支：短期
		当多个分支同时运作的时候，可以全部存在于本地，当你新建和合并分支时，整个可以都没和服务器交互过
			分支不一定需要随时push到服务器上去
	远程分支：
		远程引用：对远程仓库的引用，包括分支，标签等
			git ls-remote <remote>
		远程跟踪分支
			本地无法移动的
			一旦发生远程通信，git会自动移动到远程最新位置
			<remote>/<branch>形式
			
		![[Pasted image 20221104170849.png]]
	推送
		仅将需要和别人协作的内容推送到公开分支
			git push origin serverfix
				git自动将serverfix分支名字展开为：refs/heads/serverfix:ref/heads/serverfix
			git push origin serverfix:serverfix  : 功能相同
			git push origin serverfix:awesomebranch : 给push的分支在remote上取另外的名字	
		git fetch可以在本地生成一个远程分支
			本地不会自动生成一份可编辑的拷贝，仅存在一个指针
			git merge remote/serverfix： 将远程工作合并到本地当前所在分支
			git checkout -b serverfix origin/serverfix ：本地创建工作分支，可以在其上工作
	跟踪分支
		之前git checkout -b 出来的远程分支的对应本地分支,local branch
		对应跟踪的远程分支：上游分支，upstream branch
		git pull :自动从upstream branch上抓取内容，合并到
		git clone remote... 会自动创建对应origin/master的本地跟踪分支master
		git checkout --track origin/serverfix:快捷方式
			创建跟踪分支，同时切换到该分支
		git checkout serverfix： 更快捷方式
			如果serverfix分支当前不存在，并且刚好只有一个名字匹配的远程分支，则自动创建一个跟踪分支，并切换过去
		git checkout -b sf origin/serverfix： 创建不同名字的跟踪分支
		也可以对于已有的本地分支，通过指定upstream branch的方式，来指定其成为跟踪分支
			git branch -u origin/serverfix
				-u = --set-upstream-to
		上游快捷方式：
			@{upstream}/@{u}
			git merge @{u}
		git branch -vv：查看所有跟踪分支
			更新remote端的数据
				git fetch --all; git branch -vv
	拉取
		git fetch：不会修改workdir内容
		git pull: git fetch+merge
	删除远程分支
		git push origin --delete serverfix: 只是删除了这个branch对应的指针，remote上恢复一般比较容易
	变基：rebase
		合并不同分支内容的另外一个做法，第一种是merge
		merge：
			![[Pasted image 20221104185856.png]]
		rebase：
			![[Pasted image 20221104185934.png]]
			git checkout experiment
			git rebase master
				将C4对于C2的修改内容，施加到C3上，结果作为新的commit
			git checkout master
			git merge experiment
				对master进行fast-forward
				![[Pasted image 20221104190204.png]]
			rebase使得commit log更加整洁，分叉少
			一般确保向远程分支push时保持log整洁的额场合使用
				先本地开发，自由使用branch
				开发完成，先将自己代码rebase到origin/master
				push origin/master
				这样，remote的开发人员就不用再合并了，直接fast forward即可
	更有趣的rebase
		![[Pasted image 20221104190532.png]]
		将client的变更跳过和server的合并，引用到master上
			git rebase --onto master server client
				将C8、C9的修改内容在master上重放
				结果：
				![[Pasted image 20221104190756.png]]
			git checkout master
			git merge client
		随后，将server分支变动也整合进来
			git rebase master server
				等价于：
					git checkout server
					git rebase master
				结果：
				![[Pasted image 20221104190958.png]]
	变基的风险
		准则：**如果提交存在于你的仓库之外，而别人可能基于这些提交进行开发，那么不要执行变基**
			提交存在于你的仓库之外。。。。可能基于这些提交。。。
			以上内容中的这些提交，具体啥含义？
			![[Pasted image 20221104225825.png]]
			![[Pasted image 20221104225844.png]]
			对照上面两张图，就是C4这个commit，已经push了，就不应当再做rebase后重新push --force了
			因为rebase之后，自己这边已经消除了c4的痕迹
			但是，由于做了push，其他开发人员可能会合并到这个commit（其他人从c1开始做branch，那pull后，如果做的早，就会合并到c4）
				除非rebase之前，没有人做过pull，就不会合并到c4这个commit
				可惜，实际场景下i，这个要求很容易被突破
			其他开发继续pull，就会同时出现c4、c4‘这两个相同的commit，形成困扰
	用变基解决变基
		对某次提交所引入的修改，git也会计算hash，patch-id
		如果pull被覆盖过的更新，并将workdir中的工作基于此变更的话，一般情况下git能成功分辨哪些是你的修改，并把它们引用到新分支上
		如上面的案例，如果不执行合并，而是：
			git rebase teamone/master
			git的处理：
				- 检查workdir下独有的commit(c2、c3/c4/c6/c7)
				- 检查期中哪些提交不是合并操作的结果（c2/c3/c4)
				- 检查哪些提交在对方覆盖更新时并没有被纳入目标分支（只有c2/c3，因为c4=c4‘）
				- 把查到的这些commit引用到teamone/master上
			![[Pasted image 20221104231028.png]]
			以上要求对方rebase时确保c4和c4‘几乎一样
		另一种处理：
			git pull --rebase，而不是直接pull
			或者：git fetch;git rebase teamone/master
			如果希望默认日常使用pull同时有rebase选项，可以配置
			git config --global pull.rebase true
		**务必不要对已经push的commit内容做rebase**
		**如果已经做了，无比提醒其他人要pull是要加--rebase选项**

	变基 vs 合并
		记录所有commit，太繁琐
		改变commit内容，缺少了痕迹
		是个tradeoff
# 4 服务器上的git
# 4.1 协议
	支持4中传输协议：
		- Local：基于本机（或者共享文件系统）的目录体系
			git clone /srv/git/project.jit: 直接使用link或者cp所需要的文件
			git clone file:///srv/git/project.jit：会触发使用网络传输的进程，效率比前者慢
			缺点：
				共享文件系统更可能比较难配置
				nfs访问可能比ssh慢
				仓库难以避免意外损坏
		- http
			老版本（1.6.6之前)只能只读，新版本和ssh类似了。
			智能http协议：比ssh简单，可以使用user/pwd授权，比ssh的公钥简单
				当前最流行了，可以同时支持匿名访问（类似git），也支持传输授权和加密（类似ssh)，还可以统一url访问
				优点：
					方便通过防火墙
				缺点：
					https服务端设置
					授权推送下，凭证管理会复杂些
			dump http协议：老的，只要http设置post-update hook即可支持。
				就是hooks/post-update这个文件
				git直接会默认执行合适的命令:git update-server-info
		- ssh
			git clone ssh://[user@]server/project.git
			缺点：
				不支持匿名访问，开源不方便
		- git
			git监听9418端口，但是无需授权
			需要先创建git-daemon-export-ok文件
			一般防火墙不会开放
# 4.2 在服务器上搭建git
	先把现有仓库导出为裸仓库-- 不包含当前workdir
		git clone --bare my_project my_project.git
	把裸仓库放到服务器上
```
	scp -r my_project.git user@git.example.com:/srv/git
	ssh user@git.example.com
	cd /srv/git/my_project.git
	git init --bare --shared
		该命令不会损坏任何commit、refs等内容
	其他用户，能上ssh，就自动可以进行git clone了
```
	小型安装
		架设git服务器最复杂的地方在于用户管理。比如部分用户只读，部分用户可读写
		ssh
			用普通文件系统权限即可
			权限分配方式
				- 每个用户一个本机账户：需要额外创建用户
				- 建立一个git用户，
					- 给每个需要写权限的人发送一给ssh公钥，将其加入~/.ssh/authorized_keys
					- 所有人通过git用户名访问
					- 访问主机的身份不会影响commit的用户信息
				- 通过某个ldap服务来使用ssh
# 4.3 生成ssh公钥
	ssh-keygen
# 4.4 配置服务器
	服务器上创建repo目录
```
	cd /srv/git
	mkdir project.git
	cd project.git
	git init --bare
```
	首个用户，只需将该repo设置为该项目的remote repo，就可以推送
```
	on John's computer
	cd myproject
	git init
	git add . 
	git commit -m 'initial commit'
	git remote add origin git@gitserver:/srv/git/project.git
	git push origin master
```
	可以限制git用户登录的shell，比如/usr/local/bin/git-shell
		git-shell可以配置，限制更多
	限制用户通过ssh端口转发访问其他git服务器：
		编辑authorized_keys，在key内容之前，加上选项：
```
	no-port-forwarding,no-X11-port-forwarding,no-agent-forwarding,no-pty ssh-rsa ....
```
# 4.5 git守护进程
	git协议的支持
	优点：
		- 对于内网，可用于支持大量参与人员或自动系统（ci/cd）只读访问的项目，节省逐一配置ssh公钥的麻烦
	git deamon --reusedir --base-path=/srv/git/ /srv/git
		--base-path: 允许用户不指定路径条件下clone操作
	启动：
		- systemd
		- xinetd
		- 。。。
	明确通知git哪些repo允许基于服务器的无授权访问：
		- repo目录下，创建git-daemon-export-ok文件
			- touch git-daemon-export-ok
# 4.6 smart http
	启用git自带的git-http-backend的cgi脚本即可
```
	apache: install mod_cgi/mod_alaias/mod_env module
	chgrp -R www-data /srv/git : cgi script owner
	apache config file:
		SetEnv GIT_PROJECT_ROOT /srv/git
		SetEnv GIT_HTTP_EXPORT_ALL
			留空，是否无授权就可输出，看git-deamon-export-ok文件是否存在
		ScriptAlias /git /usr/lib/git-core/git-http-backend/
		支持写入：
		<File "git-http-backend">
			AuthType Basic
			AuthName "Git Access"
			AuthUserFile /srv/git/.htpasswd
			Require expr !(%{QUERY_STRING} -strmatch '*service=git-receive-pack*' || %{REQUEST_URI} =~ m#/git-receive-pack$#)
			Require valid-user
		</Files>
	htpasswd -c /srv/git/.htpasswd schacon : add user schacon
```
# 4.7 GitWeb
	简单的web查看页面，git提供了GitWeb这个cgi脚本
	git instaweb --http=webrick  : mac osx 预装了ruby
	启动临时http服务器，并自动打开浏览器，方便查看
	可以把这个cgi安装在apache之类的web服务器上
# 4.8 GitLab
	功能更全的git服务器
	user
	group
	project：repo
	hook
	一起工作：
		- 赋予协作者对git版本库直接push的权限
		- 使用合并请求
			- 协作者直接在原始repo上创建分支，提交
			- 协作者在原始repo上向master分支开启一个合并请求
			- 无权限的协作者先fork，向副本提交，然后从fork开启到主项目的合并请求
# 4.9 第三方托管的选择

# 5 分布式git
	利用git提供的一些分布式工作流程
# 5.1 分布式工作流程
	集中式工作流
		类似svn，cvs
		对于冲突，服务器告知change正通过非快进式（non-fast-forward)的方式push，需要自己pull后合并完成才能推送
		利用多分支方式，单项目也能支持多人很好的工作
	集成管理者工作流
		多个远程仓库存在
			- 官方权威仓库
			- 开发者clone的自有仓库
			- 官方维护者添加开发者的作为remote repo
				- 本地测试
				- 合并结果并push到官方仓库
			- 是github、gitlab等hub-based工具最常用的工作流程
		优点：
			- 可以持续工作
			- master repo维护者可以随时拉取开发者的修改
			- 贡献者不必等待维护者处理完提交的更新
			- 各方可以按照自己的节奏工作
			
		![[Pasted image 20221106161501.png]]
	主管与副主管工作流
		多仓库工作流的变种，针对超大型项目，拥有数百位写作开发者场景，比如linux项目
		流程；
			- 普通开发者：在自己的主题分支工作，并根据master进行rebase，这个master是主管推送的参考仓库的master
			- 副主管：将普通开发者的主题分支合并到自己的master分支
			- 主管：将所有副主管的master分支合并到自己的master分支
			- 最终：主管将集成后的master分支推送到参考仓库，以便所有开发者以此为基础做rebase
			![[Pasted image 20221106161827.png]]
# 5.2 向一个项目贡献
 影响因素
	 - 活跃贡献者的数量
	 - 项目使用的工作流程
	 - 提交权限
		
提交准则：创建提交，到提交补丁。。若干好的提示
	可以参考Git源码中的Documentation/SubmittingPatches
	- 提交不应当包含任何空白（仅仅包含了增减空格或者空行）错误
		- 可以通过git diff --check来检查
	- 尝试让每个提交成为一个逻辑上的独立变更集
		- 多个问题的提交，最起码利用stage area将工作拆解为每个问题一个commit
		- 如果一些改动修改了同一文件，尝试使用git add --patch来部分暂存文件
	- 提交信息，优质提交信息，会使git的使用与协作容易很多
		- 一般规则
			- 用少于50字符的单行简要描述变更
			- 一空行
			- 一个更详细的解释
				- 改动的动机
				- 实现和之前行为的对比
			- 指令式语气：主动语气，第一人称
私有小型团队
	可以类似svn之类集中式系统的工作流程
	主要区别：
		- 合并发生在客户端这边，而不是提交时发生在服务器这边
			- 如果是同一点clone的，尽管修改的不同文件，两人push的时候，也会报错
				- svn会在服务器端做一次自动合并
				- git要求先在本地合并提交，然后再push
				![[Pasted image 20221106175939.png]]
				issue54分支完成后，了解和remote上master的差别
				git fetch origin
				git log --no-merges issue54..origin/master
				合并的流程：
				git checkout master
				git merge issue54
				git merge origin/master
				结果如下：
				![[Pasted image 20221106180243.png]]
				git push origin master
	简单流程的通常事件顺序
	![[Pasted image 20221106180426.png]]
私有管理团队：大型私有团队
	小组基于特性进行协作
	这些团队的贡献将由其他人整合
	流程：
		- 在不同feature branch之间切换
		- 完成feature A的工作后，推送到服务器
			git push -u origin featureA
				服务器上，master分支只有整合者有，其他人必须推送到另外的分支
		- 可以同时切换到feature B分支工作
		- 定期合并服务器的变更
			- 来自feature B的
				- git checkout featureB
				- git fetch origin
					- 服务器上变动在featureB2
				- git merge origin/featureB2
				- git push -u origin featureB:featureB2
					- 推送合并结果到featureB2分支上
		- 查看featureA的服务器上的变更
			- git fetch origin
			- git log featureA..origin/featureA
				- 查看新的工作日志
			- git checkout featureA
			- git merge origin/featureA
			- git commit -am '...'
			- git push
		- 整合者整合featureA/featureB2分支内容
			- git fetch origin
			 ![[Pasted image 20221106183937.png]]
			 这种管理团队工作流程的基本顺序
			 ![[Pasted image 20221106184044.png]]
派生（fork）的公开项目
	如何在支持简单fork的git托管上使用其做贡献
	基本流程

```
	git clone
	git checkout -b featureA
	git commit 
		可以用git rebase -i 将工作压缩成一个单独的提交，或重排提交中的工作使补丁更容易被维护者审核
	在原始项目中点击Fork按钮，创建一份自己可写的fork repo
	git remote add myfork <url>
	git push ...
		即使工作被拒绝或被拣选，也不必退回自己的master分支
	通知原项目维护者你有想要合并的内容：拉取请求(pull Request)
		生成：
			通过网站操作生成
			git request-pull命令操作：输出可以通过电子邮件发送
				git request-pull origin/master myfork
				

基本流程

```

	![[Pasted image 20221108182910.png]]

	当自己不是维护者时，通常保持跟踪origin/master的master分支会很方便
		- 工作主题分支独立于主分支，可以rebaae工作更加容易
		- 开发新特性，不要继续在刚push的分支上工作，从origin/master上重新开始
			git checkout -b featureB origin/master
			git commit
			git push myfork featureB
			git request-pull origin/master myfork
			git fetch origin
	这样，可以让每一个特性保持在repo中，类似补丁队列，可以重写、rebase、修改而不会让特性互相干涉或互相依赖
	
	![[Pasted image 20221108182910.png]]
	当维护者已经拉取了其他一串补丁，然后尝试拉取你的第一个分支，但没干净合并。此时，可以尝试rebase那个分支到origin/master顶部，为维护者解决冲突，然后重新push改动
	```
	git checkout featureA
	git rebase origin/master
	git push -f myfork featureA
		因为rebase了，所以必须用-f选项
		替代选项：push到服务器上要给不同分支（featureAv2)
	```
	这样会重写你的历史，现在看起来像是featureA工作之后的提交历史
	![[Pasted image 20221108183319.png]]	

	一个更加可能的情况：
		维护者喜欢featureB，但是想要你修改下细节
		你也可以利用这次机会，将工作基于项目现在的master分支
		从现在的origin/master开始新分支，