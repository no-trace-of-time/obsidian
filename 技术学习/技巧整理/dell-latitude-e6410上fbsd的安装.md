# fbsd安装和启动
uefi+gpt，安装后无法启动
重新安装，bios里面设置uefi，同时boot sequence里面，选上uefi int13
	bios：uefi
	fbsd install： 
		partition： gpt（uefi）
依旧不行
发现bios里面有设置uefi boot option的
	看了下
	增加了
```
	freebsd，\EFI\BOOT\BOOTx64.EFI
```
	启动ok了
看来dell这个机器，uefi需要手工设置下