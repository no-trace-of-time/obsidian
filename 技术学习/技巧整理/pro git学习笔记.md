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