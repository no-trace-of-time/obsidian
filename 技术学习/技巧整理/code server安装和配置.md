code-server设置
	 目前已经升级到4.1.0了，nas上的还是3.12.0，需要升级下了
		 对应vs code 1.63.0
		 目前github上code server最新版本1.64.0
	 /usr/local/etc/rc.d/code-server
	 升级测试
		 curl -fsSL https://code-server.dev/install.sh | sh -s -- --dry-run
	升级
		curl -fsSL https://code-server.dev/install.sh | sh
			可以成功升级
			service restart即可，原有的配置保存着

		本地全新安装
			git clone https://github.com/coder/code-server
			./install.sh