# 修改当前用户名
	运行netplwiz，用户账户
	修改用户名
	也可以修改密码
	

# 启用远程桌面支持
	打开防火墙
	还是w530链接不上
	需要高级设置中打开远程桌面功能
	需要管理员
	用管理员登录，打开，允许远程桌面
		还需要设置允许simon这个普通用户可以链接过来


# 扩展属性不一致的错误处理
表现：资源管理器继续响应很慢，terminal无法打开的问题，界面上无法快速创建子目录，删除文件也很慢，基本没进展
都是和之前扩展属性不一致这相同的
https://zhuanlan.zhihu.com/p/500151214
	运行netplwiz,组成员-其他-选择admin-重新登录-重启
	切换到微软自带输入法