- [ ] home lab
	- [ ] nas
		- [ ] 稳定多硬盘
	- [ ] pve
		- [ ] openwrt
		- [ ] alpine
	- [ ] comp
		- [ ] 搭建
- [ ] learning
	- [ ] git
	- [ ] shell
		- [ ] shellspec
		- [ ] online install style
		- [ ] modern pratical shell programming
		- [ ] sed
		- [ ] awk
		- [ ] 
	- [ ] erlang
	- [ ] fbsd
		- [ ] rc script
		- [ ] zfs
- [ ] note
	- [ ] website内容目录放到obsidian内部，hugo管输出
- [x]  2021-12-14 w530笔记本拷贝速度被限制在100Mbps，确定下原因，千兆没发挥出来
	- [x]  2021-12-15 发现nas上alc0接口是100M的，service netif restart alc0，恢复了
- [x]  2021-12-14 4k视频kodi播放延迟，一部里面持续出现，需要定位
	- [x]  hitman‘s wife bodyguard
		- [x]  usb硬盘播放也有延迟
		- [x]  2021-12-22 考虑打开zfs的预读
			- [x]  https://wiki.freebsd.org/ZFSTuningGuide
			- [x]  The combination of ZFS and NFS stresses the ZIL to the point that performance falls significantly below expected levels. The best solution is to put the ZIL on a fast SSD (or a pair of SSDs in a mirror, for added redundancy). You can now enable/disable ZIL on a per-dataset basis (as of ZFS version 28 / FreeBSD 8.3+).

-    zfs set sync=disabled tank/dataset
- [x]  2021-12-14 4k视频格式kodi无法播放
- [x]  2021-12-14 obsidian
	- [x]  手机上发送浏览器页面给obsidian
	- [x]  git同步
	- [x]  是否可以通过git完成pc和手机的同步
	- [x]  双向链接实战
	- [x] 现有页面加tag
	- [x] unlinked mentions啥概念，和linkedd有啥区别
	
- [x]  2021-12-14 nas的2333端口突然有来自w530 ip的大量auth failed，netstat -an |grep 2333| wc-l ，显示有3k多条连接
	- [x]  应当是启动了两个clash dashboard
	- [x]  16:47 发现不是这个原因
	- [x]  ff连接，报proxy无法连接
	- [x]  应当是ff保存的usr/pwd问题，重新输入了一次，ok了
- [x]  2021-12-15 wav批量转换为专辑，可以被kodi识别，ape格式等，附加上tag信息
	- [x] 不搞了,换下载ape用已经做好的
- [ ]  nas设置更新
	- [x]  2021-12-22 禁止nmbd，不再/var/log/message中有workgroup name too long的提示了
		- [x]  nmbd_enable="NO"
	- [x] nas上增加一个统一的web入口，把各种入口集成起来,dashboard的集合
		- [x] clash dashboarkd
	- [x] 安装calibre-web，当成一个浏览器读书的入口，方便在多终端上阅读
		- [x] https://github.com/janeczku/calibre-web

- [ ] 算法学习材料
	- [ ] [GitHub - kaist-cp/cs431](https://github.com/kaist-cp/cs431)
	- [ ] 从GitHub Pages + 自建CN2反代加速迁移到CloudFlare Pages之后感觉非常赞！用自选IP在A记录针对天朝不同运营商进行解析，在天朝的速度超快！（其他国家慢我怀疑是有某几位推油梯子慢拉慢了速度，毕竟CF在全球部署的CDN应该都是挺快的）
	- [ ] [技术分享：无侵入式【全链路多重灰度发布】和【全链路压力测试】（龙韵 + 张博民）_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1NT4y127oL/)
	- [ ] [流处理系统中状态的表示和存储 - Alex Chi](https://www.skyzh.dev/posts/articles/2022-01-15-store-of-streaming-states/)
	- [ ] [github.com/tomnomnom/gron](https://t.co/YQFsCNPanw) 这个项目太有意思了，把你的json flatten成一系列的assignment，就可以用grep等工具做query，最后再还原回json。比学jq查询门槛低，可以用各种unix工具。
	- [ ] [GitHub - txthinking/zoro: zoro can help you expose local server to external network. Support both TCP/UDP, of course support HTTP. Zero-Configuration. zoro 帮助你将本地端口暴露在外网.支持TCP/UDP, 当然也支持HTTP. 内网穿透.](https://github.com/txthinking/zoro)  这个项目很适合了解内网穿透的实现方式 ：
	- [ ] [A successful Git branching model » nvie.com](https://nvie.com/posts/a-successful-git-branching-model/)
	- [ ] 接之前window的文章，这篇文章介绍了DuckDB如何实现一些特定类型的滑动窗口剧和函数。像中位数、quantile、most common element之类的函数显然不应该在每次移动窗口时重算，因此需要以特定的方式维护状态。其实就是一些LeetCode easy到medium的算法 [https://duckdb.org/2021/11/12/mov](https://t.co/RNSfP72b6n)
	- [ ] DDIA 作者[@martinkl](https://mobile.twitter.com/martinkl)关于分布式系统的公开课，质量非常高。涉及到的主题有CAP理论、拜占庭将军问题、RPC、分布式系统逻辑时钟、容错、Raft、分布式事务、最终一致性、CRDT、广播算法等，共不到7小时的时长。 [youtube.com/playlist?list=PLeKd45zvjcDFUEv_ohr_HdUFe97RItdiB](https://www.youtube.com/playlist?list=PLeKd45zvjcDFUEv_ohr_HdUFe97RItdiB)
	- [ ] 旧金山大学制作的系列算法可视化交互动图，包括常见的堆、栈、队列等。学习算法数据结构的时候，如果能图示化的展现其变化过程，理解起来就会更顺畅，在学习B+Tree算法的时候，我就用过这里的演示来理解流程。 [https://cs.usfca.edu/~galles/visual](https://t.co/3uOOPLaSQj) 
	- [ ] [GitHub - 0voice/campus_recruitmen_questions: 2021年最新整理，5000道秋招/提前批/春招/常用面试题（含答案），包括leetcode，校招笔试题，面试题，算法题，语法题。](https://github.com/0voice/campus_recruitmen_questions) 
	- [ ] 助力一下 gopher 今年金九银十跳槽季，小弟我把去年刷的 leetcode 整理出了 520 题汇成了小册子，全部都是 go 实现的 runtime beats 100% 了。在线阅读地址：[https://books.halfrost.com/leetcode/](https://t.co/G14GhATFhe)，网页支持了 PWA，可以像 mac 应用一样沉浸式阅读。
- [ ] https://leanpub.com/the-tao-of-tmux/read
- [ ] https://www.w3schools.io/file/markdown-blockquotes/
- [ ] [Format your notes - Obsidian Help](https://help.obsidian.md/How+to/Format+your+notes)

- [ ] 把nas上部署的若干服务整理下，配置rc script
	- [x] calibre web
	- [x] fail2ban
	- [x] rtorrent
	- [x] flood


	
		
	
		