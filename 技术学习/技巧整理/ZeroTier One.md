2022-03-03

安装
		Windows： https://download.zerotier.com/dist/ZeroTier%20One.msi
		ios apple store
		linux: https://github.com/zerotier/ZeroTierOne
		fbsd: pkg
		lib : https://github.com/zerotier/libzt
			IwIP: [lwIP - A Lightweight TCP/IP stack - Summary [Savannah]](https://savannah.nongnu.org/projects/lwip/)

		Step1: 
			创建一个zerotier账号，16位network id
			可以用邮件、或者google、github、ms账号来进行
			15:50 注册完成
			15:53 network id： ## 8286ac0e47ec4e7d
					home-intranet

						
			
		Step2：
			在每台设备上，下载、安装，获取10位节点地址 node address
			加入网络
			16:00 
						w530上，join network
						生成一个新的网络接口，网络2
					ios上，是添加了一个vpn
		Step3：
			admin console，Auth上打勾，允许设备接入

	16:00 测试，互相ping
				w530 180371c8d8 10.147.18.144
				ip7p  ea07150a4e 10.147.18.234
			lenovo  752c68f8ff 10.147.18.88
			nas.home 3dc0f4b1d0 10。147.18.24
			tcloud_01 9fba26450e 10.147.18.20

			w530上
				以太网适配器 ZeroTier One [8286ac0e47ec4e7d]:

   连接特定的 DNS 后缀 . . . . . . . :
   本地链接 IPv6 地址. . . . . . . . : fe80::cd0d:19c5:8528:83a8%77
   IPv4 地址 . . . . . . . . . . . . : 10.147.18.144
   子网掩码  . . . . . . . . . . . . : 255.255.255.0
   默认网关. . . . . . . . . . . . . : 25.255.255.254

	   lenovo ping其他机器可以通，到530的延时在8和50ms之间变动
	   w530 ping lenovo就不通

	   切换ip7p的外网，到移动网络，lenovo ping 不通了
		   但是同时的zero控制后台，能看到外网ip已经变化了
		   w530可以ping 通ip7p的外网状态，延时在350-550ms之间变动

	   
		16:15
			fbsd，sysrc zerotier_enable="YES"
				zerotier-cli 
					info : 查看设备id
					join： 加入
						生成一个新的网络接口zt851lc1p3uojjt
						但是目前后台看不到，还没auth
						auth之后，ifconfig就能看到ip地址了

		16:24
			fbsd上，ping 外网的ip7p,y延时在20-70ms之间
				ping内网的w530，基本在1ms之内定期到35-40ms，大致6-7秒左右，从持续的1ms，变化到一次30ms
				ping内网的lenovo，不通
					但是，反向的是通的，5ms持续，间隔一会到20ms
		16:31
			ios上，ssh终端，外网，通过zero ip，访问nas成功，不用upnp的端口映射
		16:36 lenovo上，设置ssh登录
			hosts文件中，增加各ip
			.ssh/config中，增加一项zero-nas
			ssh登录成功

		16:37
			关闭路由器上的upnp，验证依旧登录成功
				ip7p，原来的home-wan不能用了，zero-nas-home依旧成功
				lenovo上，外网链接ip7p的热点，
					ping zero_nas_home，成功
					ssh zero-nas，报22222无法连接
						但是，nas上，netstat -n |grep 22222 ，能看到10.147.18.88过来到2222的连接
		16:45 在fbsd上增加对应zero相关ip到/etc/hosts
				重新ssh zero-nas，在lenovo上发起，成功了
		16:47 tcloud_01上安装，是ubuntu
>curl -s 'https://raw.githubusercontent.com/zerotier/ZeroTierOne/master/doc/contact%40zerotier.com.gpg' | gpg --import && \
if z=$(curl -s 'https://install.zerotier.com/' | gpg); then echo "$z" | sudo bash; fi

	脚本执行之后，使用apt/yum来安装

	tcloud上访问github太慢，就先在nas上分批下载gpg key，在tcloud上gpg --import
		然后获取install脚本，用原有脚本后面部分，在tcloud上运行
		最终成功运行
		安装zerotier-one


	17:04
		zerotier-cli join ## 8286ac0e47ec4e7d
		生成了一个ztrtauaqb2的网络接口
		刷新后台，看是否有需要auth的node

	17:13 后台ip有了
		lenovo上ping的延迟大概300ms
			ssh添加设置，访问ok

	17:21 tcloud01 reboot，看下zerotier是否会自动启动

#todo
	- [ ] dns push
	DNS Push

Requires ZeroTier version 1.6

Older versions of ZeroTier will ignore these settings

On macOS, iOS, Windows, and Android, ZeroTier can automatically add DNS servers for a specific domain. It does not set up or host a DNS server. You must host your own.

If you configure zt.example.com as your search domain, and 10.147.20.1 as a server address, then your computer will ask 10.147.20.1 to look up IP addresses for hostnames ending in zt.example.com

This must be enabled on each client with the allowDNS option. There is a checkbox in the UI in each network's details, near the Allow Managed checkbox.