---
title: 使用 Google 自定义搜索
tags:
  - Google
  - CSE
  - Disqus
  - Custom Search Engine
categories:
  - [Hexo,Search]
author: Rhysn
thumbnail: https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200416/google_search_cse_hexo/thumbnail.png
date: 2020-04-16 16:53:54
urlname: google_search_cse_hexo
---

使用 [Google Custom Search Engine][cse] 增强博客搜索能力（实际体验还不如本地搜索），实验环境为 Hexo + Suka主题，目标为在科学网络环境下调用 Google CSE ，在常规网络环境下使用Local Search。

## Google CSE 创建

使用 CSE 需要通过 `https://cse.google.com/` 自行创建一个，记录下得到**搜索引擎 ID**，在配置文件中使用。

![CSE](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200416/google_search_cse_hexo/cseid.png)

设置搜索引擎搜索范围，指定本站：

![CSE](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200416/google_search_cse_hexo/site.png)

其他相关设置可根据个人需求自行调整。

## Suka 主题更改

在配置文件 `themes\suka\_config.yml` 中的 Search 部分添加字段，方便后续更改与调整，修改内容如下：

```yaml
search:
  use: googlecse 
  # Swiftype
  swiftype_key:
  googlecse_id: 005901140517734173075:cfrhjvvby4c
```

更改  layout 部分内容，用以生成最终代码，首先修改 `themes\suka\layout\_pages\search.ejs` 增加 `googlecse` 的内容，这部分内容与配置文件中 use 部分使用一致，可根据个人习惯修改名称，保持两侧一致即可。

```ejs
<div class="container search-container">
    <div class="main-container">
        <!-- # Title # -->
        <h2 class="page-title"><%= __('search.main') %></h2>
           ......
        <% if (theme.search.use === 'googlecse') { %>
            <%- partial('_plugin/search/googlecse/main') %>
        <% } %>
    </div>
</div>
           ......
<% if (theme.search.use === 'googlecse') { %>
    <%- partial('_plugin/search/googlecse/common') %>
<% } %>
```

 匹配配置文件，指定所需的文件地址。

在 `themes\suka\layout\_plugin\search\` 下创建 `googlecse` 目录，与 `themes\suka\layout\_pages\search.ejs` 中指定路径保持一致，之后创建 common.ejs 与 main.ejs 两个文件，其中内容直接复制 `themes\suka\layout\_plugin\search\local-search` 中文件。

修改 HTML 部分内容如下，增加 CSE 使用的 `<div class="gcse-search"></div>` 部分内容，并使用 `style="display:none"` 在初始情况下隐藏。

```html
<div id="gcse" style="display:none"><div class="gcse-search"></div></div>
<div id="ls">
    <div id="search-input">
        <form class="input-group" autocomplete="off">
            <input maxlength="80" type="search" id="search-field" name="s" class="form-input input-lg" placeholder="<%= __('search.placeholder') %>" required>
            <button class="btn btn-primary input-group-btn btn-lg" type="submit"><%= __('search.main') %></button>
        </form>
    </div>
    <div id="search-result-info" hidden><%- __('search.num', '<span id="search-result-num"></span>')%></div>
    <div id="search-output"></div>
</div>
```

 之后在下方增加 Script 部分代码：

```html
<script>
    var head = document.getElementsByTagName('head')[0] || document.documentElement;
    var s = document.createElement('script');
    s.type = 'text/javascript';
    s.onload = s.onreadystatechange = function() {
        // 加载状态变更后，回调执行部分内容
        if (!/*@cc_on!@*/0 || this.readyState === 'loaded' || this.readyState === 'complete') {
            document.getElementById('gcse').setAttribute("style", "display:block");
            document.getElementById('ls').setAttribute("style", "display:none");
            document.getElementsByTagName("title")[0].innerText = 'Slowly Search By Google CSE ';
            //console.log('Using Google CSE');
        }
    };
    s.src = 'https://cse.google.com/cse.js?cx=<%= theme.search.googlecse_id %>';
    head.appendChild(s);
</script>
```

使用 JavaScript 加载 Google CSE 所需的 JS 文件，并在加载成功后执行回调函数内容，其中状态判断部分使用 [singcl][singcl] 分享的方案。

效果测试如下：

在科学网络环境下：

![CSE](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200416/google_search_cse_hexo/gcse.png)

常态：

![CSE](https://cdn.jsdelivr.net/gh/Rhysn/Ultranti@Published/data/img/20200416/google_search_cse_hexo/ls.png)

展示效果如此，但是 Google CSE 使用效果就很不尽人意了。

[cse]: https://cse.google.com/
[singcl]: https://juejin.im/post/5a96156a6fb9a0635a659244