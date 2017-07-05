---
title: hexo 配置一
date: 2016-03-26 11:38:14
tags: hexo
categories: program

---
## 修改主题

## 安装不蒜子
### 安装脚本 (必选)
hexo目录下，打开themes/你的主题/layout/_partial/footer.ejs添加上述脚本即可，当然你也可以添加到 header 中。添加到最后面就可以

	<script async src="https://dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js">
	</script>

<!--more-->

### 安装标签
同样更改上述的文件为：中间一段是插入的代码

	<div class="footer-left">
		&copy; <%= date(new Date(), 'YYYY') %> <%= config.author || config.title %>
	</div>


	<span id="busuanzi_container_site_pv">
		本站总访问量<span id="busuanzi_value_site_pv"></span>次
	</span>
	<span id="busuanzi_container_page_pv">
		本文总阅读量<span id="busuanzi_value_page_pv"></span>次
	</span>



	<div class="footer-right">
 		<a href="http://hexo.io/" target="_blank">Hexo</a>  Theme <a href="https://github.com/litten/hexo-theme-yilia" target="_blank">Yilia</a> by Litten
	</div>

最终效果：

![不蒜子](http://i.imgur.com/gxjLmwV.png)

更多内容参考：[http://ibruce.info/2015/04/04/busuanzi/](http://ibruce.info/2015/04/04/busuanzi/)

## 网站地图 Sitemap