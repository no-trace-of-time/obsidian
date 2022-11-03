#dhcp

- todos
	- [ ] 设置仅支持在mac列表里面的设备
	- [ ] 不同设备在不同子网中，隔离智能电器和服务器
	- [ ] 重新加载配置文件
	- [ ] # [ISC DHCP Server "Dynamic and static leases present"](https://serverfault.com/questions/750710/isc-dhcp-server-dynamic-and-static-leases-present)



2021-12-22
	
	https://askubuntu.com/questions/1015658/how-to-reserve-dhcp-for-mac-address-in-another-file
	
	使用include，把mac-ip的映射关系放到外部文件中
	
	
	https://serverfault.com/questions/923171/isc-dhcp-assign-pool-subnet-to-certain-mac-addresses
	
	deny unkown-client;
	
		随后在known-hosts.conf中增加对应的ether mac，并在/etc/hosts中指定对应的ip地址，即可
		也可以不指定ip地址，这样自动获取动态范围内的ip地址