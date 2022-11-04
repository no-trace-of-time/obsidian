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
		