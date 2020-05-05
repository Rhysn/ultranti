---
title: 运用惰性加载Disqus(lazyload)加快网页访问速度
tags:
  - lazyload
  - Hexo
  - Disquse
categories:
  - [Hexo]
author: Rhysn
thumbnail: https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200413/lazy-loading-disqus/thumbnail.png
date: 2020-04-13 11:18:54
urlname: lazy_loading_disqus_comments
---

使用 [Hexo][hexo] 等方式进行静态网站搭建，永远绕不开的就是评论问题的解决，而评论解决方案中总是要考虑 [Disqus][disqus] ，由此又引发了JavaScript、CSS、image、font files各种外部资源请求导致的页面渲染阻塞问题。

为了解决这一问题，国外网页设计 [Osvaldas Valutis][ov] 编写了 [disqusLoader.js][lldcgithub] 脚本，同时作者给出了非常详细的[介绍][lldc]及[Demo][lldcdemo]，在此只是尝试将此脚本引入Hexo，加速网页访问速度。

在 Hexo 主题 Suka 1.4.0中完成配置。

首先在 `suka\layout\_plugin\comment\` 内创建 `suka\layout\_plugin\comment\disqus` 副本并重命名为 `disqus_lazyload` ，以此为基础完成修改。

将 `common.ejs` 文件中内容清空，之后的 lazyload 形式无需加载多余 JavaScript，将 `main.ejs` 内容修改如下：

```html
<div class="disqus"></div>
<div class="disqus-loading">Loading comments&hellip;</div>
<script src="https://cdn.jsdelivr.net/gh/osvaldasvalutis/disqusLoader.js/disqusloader.min.js"></script>
<script>
	disqusLoader( '.disqus',
	{
		scriptUrl:		'//<%= theme.comment.disqus.shortname %>.disqus.com/embed.js',
		disqusConfig:	function()
		{
			this.page.identifier = '<%= page.permalink %>';
			this.page.url = '<%= page.permalink %>';
			this.page.title = '<%= page.title %>';
			this.callbacks.onReady	= [function()
			{
				var el = document.querySelector( '.disqus-loading' );
				if( el.classList )
					el.classList.add( 'is-hidden' ); // IE 10+
				else
					el.className += ' ' + 'is-hidden'; // IE 8-9
			}];
		}
	});
</script>
```

此部分代码中直接调用了 `disqus` 的设置内容，所以在主题配置文件中，将 `comment` 部分修改如下：

```properties
comment:
  use: disqus_lazyload
  disqus:
    shortname: # Disqus's shortname
```

添加 CSS 样式，在 `suka\layout\_partial\head\config_style.ejs` 文件内 `</style>` 标签前追加如下内容：

```css
    .disqus-loading{
        font-size: 26px;
        font-weight: 300;
        color: #777;
        padding: 1.25rem 0; /* 20 */
    }
    .disqus-loading.is-hidden { display: none; }
```

Ps：

在 `main.ejs` 中引入部分使用 jsDelivr CDN 加速链接，也可以使用Github原始链接。

```html
<!-  jsDelivr CDN ->
<script src="https://cdn.jsdelivr.net/gh/osvaldasvalutis/disqusLoader.js/disqusloader.min.js">
<!-  Github ->
<script src="https://raw.githubusercontent.com/osvaldasvalutis/disqusLoader.js/master/disqusloader.js">
```

最终效果类似于：

![](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200413/lazy-loading-disqus/disqus-comments-enhanced-loading.gif)

其他主题修改内容都与此类似，大家可以自行尝试一下，轮子都被人造好了，我们直接装上跑就完了。



[hexo]: https://hexo.io/
[disqus]: https://disqus.com/
[lldc]: https://css-tricks.com/lazy-loading-disqus-comments/
[lldcgithub]: https://github.com/osvaldasvalutis/disqusLoader.js
[lldcdemo]: https://osvaldas.info/examples/lazy-loading-disqus-comments/
[ov]: https://osvaldas.info/