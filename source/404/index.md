title: 大大的404：该页无法显示
toc: false
comments: false
permalink: /404
date: 2017-12-26 14:35:24
---
<div align="center">
  <img src="//a0.att.hudong.com/57/09/300001174781130993090105342_950.jpg" />
  <strong style="color:red;">很抱歉，你所访问的页面不存在了，可能是我调整了网站域名所至</strong>
    <a href="javascript:to();">>> 点击这里试试 <<</a>
    或者 修改当前URL的域名为：blog.zhangbing.site
    页面找不到了，不要慌，你可以随便到处看看其他的东西
  <a href="https://zhangbing.site">网站首页</a> - <a href="https://blog.zhangbing.site">博客首页</a>
</div>

<script>
if(confirm('该页面URL可能已经变更，是否去新的地址？')) to();
function to(){
    var url = window.location.href; 
    // 'https://dunizb.com/2017/12/08/You-have-to-know-the-basic-HTTP-concepts/';
    location.href = 'https://blog.zhangbing.site/' + url.split('.com')[1]
}
</script>