-   [Github 增强 - 高速下载](https://link.juejin.cn/?target=https%3A%2F%2Fgreasyfork.org%2Fzh-CN%2Fscripts%2F412245-github-%25E5%25A2%259E%25E5%25BC%25BA-%25E9%25AB%2598%25E9%2580%259F%25E4%25B8%258B%25E8%25BD%25BD "https://greasyfork.org/zh-CN/scripts/412245-github-%E5%A2%9E%E5%BC%BA-%E9%AB%98%E9%80%9F%E4%B8%8B%E8%BD%BD")

为 `Github` 的 `Git Clone`、`Release`、`Raw`、`Code(ZIP)` 等文件添加 高速下载（加速下载）。 ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dc1058c4f41740029d5d540e8d906b76~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

**第二种 安装GitHub加速插件**

-   [GitHub加速](https://link.juejin.cn/?target=https%3A%2F%2Fchrome.google.com%2Fwebstore%2Fdetail%2Fgithub%25E5%258A%25A0%25E9%2580%259F%2Fffjjnphohkfckeplcjflmgneebafggej "https://chrome.google.com/webstore/detail/github%E5%8A%A0%E9%80%9F/ffjjnphohkfckeplcjflmgneebafggej")

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7fd5159138324365a4d06da6974e4ad0~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

提供`Release`、`Code(ZIP)` `tag`文件加速。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8038c275d1334c78b82e2e15cf28a67d~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

### 通过代理网站下载

**Release、Code(ZIP) 文件加速：**

-   [gh.api.99988866.xyz](https://link.juejin.cn/?target=https%3A%2F%2Fgh.api.99988866.xyz "https://gh.api.99988866.xyz")
-   [github.rc1844.workers.dev](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.rc1844.workers.dev "https://github.rc1844.workers.dev")
-   [ghgo.feizhuqwq.workers.dev](https://link.juejin.cn/?target=https%3A%2F%2Fghgo.feizhuqwq.workers.dev "https://ghgo.feizhuqwq.workers.dev")
-   [git.yumenaka.net](https://link.juejin.cn/?target=https%3A%2F%2Fgit.yumenaka.net "https://git.yumenaka.net")
-   [github.com.cnpmjs.org](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com.cnpmjs.org "https://github.com.cnpmjs.org")
-   [mirror.ghproxy.com/](https://link.juejin.cn/?target=https%3A%2F%2Fmirror.ghproxy.com%2F "https://mirror.ghproxy.com/")
-   [ghproxy.com/](https://link.juejin.cn/?target=https%3A%2F%2Fghproxy.com%2F "https://ghproxy.com/")
-   [toolwa.com/github/](https://link.juejin.cn/?target=https%3A%2F%2Ftoolwa.com%2Fgithub%2F "https://toolwa.com/github/")

**Git Clone 加速：**

-   [github.do](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.do "https://github.do")
-   [gitclone.com](https://link.juejin.cn/?target=https%3A%2F%2Fgitclone.com "https://gitclone.com")
-   [hub.fastgit.xyz](https://link.juejin.cn/?target=https%3A%2F%2Fhub.fastgit.xyz "https://hub.fastgit.xyz")
-   [ghproxy.com](https://link.juejin.cn/?target=https%3A%2F%2Fghproxy.com "https://ghproxy.com")
-   [hub.0z.gs](https://link.juejin.cn/?target=https%3A%2F%2Fhub.0z.gs "https://hub.0z.gs")

具体哪个速度快，请自行找一些大文件来测速。

### Gitee中转fork仓库下载

访问 `gitee` 网站并登录，在顶部选择“从 `GitHub/GitLab` 导入仓库” 如下

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d2b92288492f47b9b3c47a042caf7441~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

在页面中粘贴你的Github仓库地址，点击导入即可

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/44721404d30f4d1fb4ac08d1a17077d0~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp) 导入成功，`git clone`仓库，下载速度可以达到`2MB/s`，就是有点费事。

### 修改HOSTS文件进行加速

自从 2021.3 月初某会开始，很多地区已经**间歇性无法访问** Github 了。 这种情况无论是改 DNS 还是改 Hosts 都没用，因为对 Github 域名 SNI 干扰/封锁，**任意 IP 指向 Github 去访问时，该 IP 的 443 端口就会超时 3 分钟！** 因为是随机干扰的，所以有时候会碰到 “短暂” 可用的 IP

> 有兴趣可以看看这篇详细讲解分析的文章：[www.v2ex.com/t/758568](https://link.juejin.cn/?target=https%3A%2F%2Fwww.v2ex.com%2Ft%2F758568 "https://www.v2ex.com/t/758568")

由于条件随机生效，修改HOSTS文件进行加速方法是否还有效，可以自测。

-   [SwitchHosts -修改HOSTS小工具](https://link.juejin.cn/?target=https%3A%2F%2Fswh.app%2Fzh%2F "https://swh.app/zh/")

**定时同步**

添加一条规则：

-   方案名：GitHub
-   类型：远程
-   URL 地址：`https://gitee.com/ineo6/hosts/raw/master/hosts`
-   自动更新：1个小时

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b31537d1591e4eac9550f9116015077b~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

**刷新DNS缓存**

打开终端，输入对应命令

```
// Mac用户
sudo killall -HUP mDNSResponder

// Win
ipconfig /flushdns
复制代码
```

> 原文地址：[github.com/ineo6/hosts](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fineo6%2Fhosts "https://github.com/ineo6/hosts")

有兴趣可以阅读文章[SwitchHosts! 还能这样管理 hosts](https://link.juejin.cn/?target=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FA37XnD3HdcGSWUflj6JujQ "https://mp.weixin.qq.com/s/A37XnD3HdcGSWUflj6JujQ")，后悔没早点用 了解详情，里面有介绍以及各个平台刷新 DNS 缓存的方法。

**参考**

-   [github.com/ineo6/hosts](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fineo6%2Fhosts "https://github.com/ineo6/hosts")
-   [www.beiwangshan.com/205.html](https://link.juejin.cn/?target=https%3A%2F%2Fwww.beiwangshan.com%2F205.html "https://www.beiwangshan.com/205.html")
-   [greasyfork.org/zh-CN/scrip…](https://link.juejin.cn/?target=https%3A%2F%2Fgreasyfork.org%2Fzh-CN%2Fscripts%2F412245-github-%25E5%25A2%259E%25E5%25BC%25BA-%25E9%25AB%2598%25E9%2580%259F%25E4%25B8%258B%25E8%25BD%25BD "https://greasyfork.org/zh-CN/scripts/412245-github-%E5%A2%9E%E5%BC%BA-%E9%AB%98%E9%80%9F%E4%B8%8B%E8%BD%BD")

![](https://lf3-cdn-tos.bytescm.com/obj/static/xitu_juejin_web/00ba359ecd0075e59ffbc3d810af551d.svg) 赞

![](https://lf3-cdn-tos.bytescm.com/obj/static/xitu_juejin_web/3d482c7a948bac826e155953b2a28a9e.svg) 收藏