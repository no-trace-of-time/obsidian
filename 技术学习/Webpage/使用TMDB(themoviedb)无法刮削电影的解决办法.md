## 原因：因为api.themoviedb.org被墙无法访问，导致使用\]无法刮削或刮削特别慢

可通过修改本机host，指定api.themoviedb.org解析到可用IP来解决，可用的IP有：  
13.225.97.51  
13.225.97.23  
99.84.233.39  
13.225.97.69  
13.224.164.59  
99.84.233.94

最新IP可以在这里查看：[查看api.themoviedb.org](https://blog.lcsoul.cn/go.html?url=http://tool.chinaz.com/speedworld/api.themoviedb.org)

## 解决：

### 威联通

修改\\etc\\hosts文件增加一行  
13.225.97.51 api.themoviedb.org

### 威Windows

修改C:\\Windows\\System32\\drivers\\etc\\hosts文件  
添加：13.225.97.51 api.themoviedb.org

### 群晖

修改\\etc\\hosts文件增加  
sudo -i (解锁权限)  
13.225.97.51 api.themoviedb.org  
13.224.157.34 api.thetvdb.com

### 群晖Docker

设备不支持硬解输入以下代码：  
docker run  
–name=jellyfin  
–add-host=api.themoviedb.org:13.224.161.90  
–add-host=image.tmdb.org:104.16.61.155  
–add-host=api.themoviedb.org:13.35.67.86  
–add-host=www.themoviedb.org:54.192.151.79  
jellyfin/jellyfin:latest

支持硬解输入以下代码：  
docker run  
–name=jellyfin  
–add-host=api.themoviedb.org:13.224.161.90  
–add-host=image.tmdb.org:104.16.61.155  
–add-host=api.themoviedb.org:13.35.67.86  
–add-host=www.themoviedb.org:54.192.151.79  
–device=/dev/dri:/dev/dri jellyfin/jellyfin:latest

___