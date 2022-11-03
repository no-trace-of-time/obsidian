# 增加当前主机名在提示行上，方便观察
	修改dell机器的主机名
	设置profile
		位置：
```
		$HOME\Docurments\WindowsPowerShell\Microsoft.PowerShell_profile.ps1
```

		https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_profiles?view=powershell-7.2
		
		https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_prompts?view=powershell-7.2

		内容：
```
	function prompt {
		"PS [$env:COMPUTERNAME]> "
	}
		
```
				

		发现w530/dell两台机器上，profile路径不完全相同
			w530： PowerShell\...
			dell:  WindowsPowerShell\...
			发现问题在powershell的版本，是原始随机的，还是最新github上下载的
				对于原始的，是windowsPowerShell目录
				对于最新的，是PowerShell目录