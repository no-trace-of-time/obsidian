# 多repo的同步
[[Working with Git remotes and pushing to multiple Git repositories  Jigarius.com]]

obsidian repo的案例
	git remote add origin git@github.com:no-trace-of-time/obsidian.git
	git remote add gitee git@gitee.com:no-trace-of-time/obsidian.git
	git remote add nas git@nas.home:simonxu/obsidian.git
		这里nas的url里面，不需要填写额外的gitea的内容
	git push -u origin master
	git push -u gitee master
	git push -u nas master
	本地的branch master对应的是最有一个push命令的remote

	设置一个统一的remote all
	git remote add all git@nas.home...
	git remote set-url --add --push all git@github...
	git remote set-url --add --push all git@gitee...

	删除一个多余的remote push url
	git remote set-url --delete --push all git@...

	同时向多个orgin提交
		git push all
			需要注意：all只有nas的fetch url的话，push是不会提交内容的
				需要增加nas的push url，才能实现三者同时push到remote服务器
		
	
	
# 修改remote的url
	git remote set-url gitee git@.....
	
# git status中文文件名编码显示
```
git config --global core.quotepath false
```
设置之后，git status就可以显示中文文件名了

# ssh方式登录git服务器
## github
	git -vT git@github.com
	可以显示详细信息
	github上，需要在Settings-SSH and PGP Keys-下面，有对应的ssh key清单

## gitee
	同github
	
# push local repo to server
	本地
		git init
		git add
		git commit
	github
		new repo
	本地
		git remote add origin git@github.com:no-trace-of-time/obsidian.git
		git push --set-upstream origin master
			可以简写为：git pubsh -u origin master
# https url的push避免每次都输入用户名、密码
	设置一个credential cache
		git config --global credential cache
		可以缓存几分钟
# 命令行上创建远程repo
	github API v3
	```
		curl -u 'USER' https://api.github.com/user/repos -d {"name":"REPO","private":"true"}
```
	github有自己的命令行接口，gh

gitee/gitea/gogs，应当有基本类似的api
	其实可以用gogs cli这样的工具来创建
	