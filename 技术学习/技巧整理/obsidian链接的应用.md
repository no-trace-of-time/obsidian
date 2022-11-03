# 双链的相关操作
- 手工输入两个中括号，里面填写内容
- 选中一段文字，鼠标右键，选择extract current selection... ，选择链接的页面 ^c21c75
- 在一个note右边链接清单中，ctrl+鼠标左键，点击链接，会并排打开对应的页面




# 单链的相关操作
- 手工输入一个方括号，里面填写内容。这样，括号中对应页面就会出现unlinked mentions里面
	这种： [2022-02-11]

![[Pasted image 20220226224504.png]]


# 链接用来实现笔记之间关联的操作方式
- daily notes和完整整理内容之间的关联
	- 每天先记录daily note，自动的yyyy-mm-dd格式的标题
	- 对于确定和之前页面相关的，增加一个backlink
- 层级关系的内容
	- 比如：概念之间，A依赖B，B依赖C
		- 先看到A，继续研究，发现依赖B，需要先看B的时候，在A当前位置添加一个B的backlink，或者B页面添加一个A的backlink
		- 从B研究到C，在C上加B的backlink
		- C研究完成，根据backlink，到B继续研究
		- B研究完成，同样追溯到A
		- 直到A研究完成


# 笔记的引用和嵌入
- 引用
	- 文件引用: [[note文件名称]]
	- 标题引用: [[obsidian链接的应用#双链的相关操作]]
	- 块引用: [[obsidian链接的应用#^c21c75]]，其实就是一个段落的位置，回车分开每个段落
- 标签
	- 可以在多个笔记中使用同一个标签，并在关系图中查看
		- #bhyve  #aa #bb/cc/dd （还可以分级的tag）
- 官方note composer插件提取内容
	> 选中一段文字
	> 鼠标右键，弹出选择：extract current selection ....
	> 选择的内容会转移到指定文件，也可以创建新文件，加在最后
	> 原始文件位置，会增加一个对应的backlink
	
	[[Obsidian 笔记整理及TOC索引（二）]]
