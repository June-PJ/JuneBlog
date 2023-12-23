---
title: Hexo美化（1）
date: 2023-12-22 11:26:38
description: 网站主题是Butterfly，作者美化网站的经历，基本是借鉴其他博客，一点点cv过来的，怕忘记，就记录下来
categories:
  - 博客搭建
tags:
  - 博客搭建
  - Hexo
  - Hexo美化
---

## 前言

​	网站主题是`Butterfly`，作者美化网站的经历，基本是借鉴其他博客，一点点cv过来的，怕忘记，就记录下来

### 佬的网站

[Ariasakaの小窝 (yisous.xyz)](https://yisous.xyz/)

[安知鱼 - 生活明朗 万物可爱 (anheyu.com)](https://blog.anheyu.com/)

[[DORAKIKA | 热爱漫无边际，生活自有分寸！](https://blog.dorakika.cn/)

[[ichikaの小窝 - 被发现了嗼](https://ichika.cc/)](https://blog.anheyu.com/)

[贰猹の小窝 - 奋力奔跑，去追心中的那一道光 (noionion.top)](https://noionion.top/)

### 美化之前

美化的时候需要修改网站的源文件、添加样式、js之类的，需要一点基础,可以参考[Hexo Butterfly博客魔改的一点点基础 | Ariasakaの小窝 (yisous.xyz)](https://yisous.xyz/posts/583ff077/)

## 1. 导航栏

### 效果图

修改效果：导航栏居中，二级菜单横向展示，导航栏显示网站、文章的标题等

![image-20231223165121692](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-20231223165121692.png)

![image-20231223165147837](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-20231223165147837.png)

### 参考链接

[关于Butterfly的导航栏的一些教程 | Ariasakaの小窝 (yisous.xyz)](https://yisous.xyz/posts/895003b5/)

[butterfly导航栏修改方案(自用方案) | 安知鱼 (anheyu.com)](https://blog.anheyu.com/posts/8e53.html)

### 1. 修改`nav.pug`文件

修改`[blogRoot]\themes\Butterfly\layout\includes\header\nav.pug`

```pug
nav#nav
  span#blog_name
    a#site-name(href=url_for('/')) #[=config.title]

  #menus
    !=partial('includes/header/menu_item', {}, {cache: true})
    center(id="name-container")
      a(id="page-name" href="javascript:rmf.scrollToTop()") PAGE_NAME

  #nav-right
    if (theme.algolia_search.enable || theme.local_search.enable)
      #search-button
        a.site-page.social-icon.search
          i.fas.fa-search.fa-fw
      #toggle-menu
        a.site-page
          i.fas.fa-bars.fa-fw
```

如果你的`themes`下没有`Butterfly`文件夹（可能是主题安装方式有问题），可以去`Butterfly`官网下载[jerryc127/hexo-theme-butterfly: 🦋 A Hexo Theme: Butterfly (github.com)](https://github.com/jerryc127/hexo-theme-butterfly)，我下载的版本是`4.5.1`

**注意：**如果你没有定义`rmf.scrollToTop()`函数，可以将其改为`btf.scrollToDest(0, 500)`

### 2. 添加`nav.js`文件

新建`[blogRoot]\source\static\js\nav.js`

```js
document.addEventListener('pjax:complete', tonav);
document.addEventListener('DOMContentLoaded', tonav);
//响应pjax
function tonav(){
    document.getElementById("name-container").setAttribute("style", "display:none");

    var position = $(window).scrollTop();

    $(window).scroll(function () {

        var scroll = $(window).scrollTop();

        if (scroll > position) {


            document.getElementById("name-container").setAttribute("style", "");
            document.getElementsByClassName("menus_items")[1].setAttribute("style", "display:none!important");

        } else {


            document.getElementsByClassName("menus_items")[1].setAttribute("style", "");
            document.getElementById("name-container").setAttribute("style", "display:none");

        }

        position = scroll;

    });
    function scrollToTop(){
        document.getElementsByClassName("menus_items")[1].setAttribute("style","");
        document.getElementById("name-container").setAttribute("style","display:none");
        btf.scrollToDest(0, 500);
    }
//修复没有弄右键菜单的童鞋无法回顶部的问题
    document.getElementById("page-name").innerText = document.title.split(" | June's Blog")[0];}
```

记得将信息改成自己的

### 3. 添加`nav.css`样式

新建`[blogRoot]\source\static\css\nav.css`

```css
/*导航栏居中*/
#nav-right {
    flex: 1 1 auto;
    justify-content: flex-end;
    margin-left: auto;
    display: flex;
    flex-wrap: nowrap;
}

/*去掉导航栏项目底下的蓝色长条*/
#nav *::after {
    background-color: transparent !important;
}

/*子菜单横向布局*/
#nav .menus_items .menus_item:hover .menus_item_child {
    display: flex;
}

/*网站标题部分的增强版*/
#site-name::before {
    opacity: 0;
    background-color: var(--june-theme) !important;
    border-radius: 8px;
    -webkit-border-radius: 8px;
    -moz-border-radius: 8px;
    -ms-border-radius: 8px;
    -o-border-radius: 8px;
    transition: .3s;
    -webkit-transition: .3s;
    -moz-transition: .3s;
    -ms-transition: .3s;
    -o-transition: .3s;
    position: absolute;
    top: 0 !important;
    right: 0 !important;
    width: 100%;
    height: 100%;
    content: "\f015";
    box-shadow: 0 0 5px var(--june-theme);
    font-family: "Font Awesome 6 Free";
    text-align: center;
    color: white;
    line-height: 34px; /*如果有溢出或者垂直不居中的现象微调一下这个参数*/
    font-size: 18px; /*根据个人喜好*/
}

#site-name:hover::before {
    opacity: 1;
    scale: 1.03;
}

#site-name {
    position: relative;
    font-size: 24px; /*一定要把字体调大点，否则效果惨不忍睹！*/
}

/*顶栏常驻*/
.nav-fixed #nav {
    transform: translateY(58px) !important;
    -webkit-transform: translateY(58px) !important;
    -moz-transform: translateY(58px) !important;
    -ms-transform: translateY(58px) !important;
    -o-transform: translateY(58px) !important;
}

#nav {
    transition: none !important;
    -webkit-transition: none !important;
    -moz-transition: none !important;
    -ms-transition: none !important;
    -o-transition: none !important;
}

/*显示标题*/
/*
2022.10.4更新：
根据我发现的没有自适应，间距不合理问题进行调整，如果用了这个的朋友们建议改一改
*/
#page-name::before {
    font-size: 18px;
    position: absolute;
    width: 100%;
    height: 100%;
    border-radius: 8px;
    color: white !important;
    top: 0;
    left: 0;
    content: '回到顶部';
    background-color: var(--june-theme);
    transition: all .3s;
    -webkit-transition: all .3s;
    -moz-transition: all .3s;
    -ms-transition: all .3s;
    -o-transition: all .3s;
    opacity: 0;
    box-shadow: 0 0 3px var(--june-theme);
    line-height: 45px; /*如果垂直位置不居中可以微调此值，也可以删了*/
}

#page-name:hover:before {
    opacity: 1;
}

@media screen and (max-width: 900px) {
    #page-name, #menus {
        display: none !important;
    }
}

#name-container {
    transition: all .3s;
    -webkit-transition: all .3s;
    -moz-transition: all .3s;
    -ms-transition: all .3s;
    -o-transition: all .3s;
}

#name-container:hover {
    scale: 1.03
}

#page-name {
    position: relative;
    padding: 10px 30px /*如果文字间隔不合理可以微调修改，第二个是水平方向的padding，第一个是垂直的*/
}

#nav {
    padding: 0 20px;
}
```

**记得在` _config.butterfly.yml`导入**

```xml
# Inject
# Insert the code to head (before '</head>' tag) and the bottom (before '</body>' tag)
# 插入代码到头部 </head> 之前 和 底部 </body> 之前
inject:
  head:
   - <link rel="stylesheet" href="/static/css/nav.css">
   ....
  bottom:
    - <script type="text/javascript" src="https://unpkg.zhimg.com/jquery@latest/dist/jquery.min.js"></script> #一定要放在所有引入的js前面！！！
    - <script data-pjax type="text/javascript" src="/static/js/nav.js"></script>
    ...
```

## 2. 右键菜单

### 效果图

![image-20231223165212917](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-20231223165212917.png)

### 参考链接

[butterfly博客自定义右键菜单升级版 | Ariasakaの小窝 (yisous.xyz)](https://yisous.xyz/posts/11eb4aac/)

[自定义右键菜单 | DORAKIKA](https://blog.dorakika.cn/post/20220118/)

### 1. 添加`rightmenu.pug`文件

新建 `[blogRoot]\themes\butterfly\layout\includes\rightmenu.pug`

```pug
#rightMenu
    .rightMenu-group.rightMenu-small
        a.rightMenu-item(href="javascript:window.history.back();")
            i.fa.fa-arrow-left
        a.rightMenu-item(href="javascript:window.history.forward();")
            i.fa.fa-arrow-right
        a.rightMenu-item(href="javascript:window.location.reload();")
            i.fa.fa-refresh
        a.rightMenu-item(href="javascript:rmf.scrollToTop();")
            i.fa.fa-arrow-up
    .rightMenu-group.rightMenu-line.hide#menu-text
        a.rightMenu-item(href="javascript:rmf.copySelect();")
            i.fa.fa-copy
            span='复制'
    .rightMenu-group.rightMenu-line
        a.rightMenu-item(href="javascript:rmf.switchDarkMode();")
            i.fa.fa-moon
            span='昼夜切换'
        a.rightMenu-item(href="javascript:rmf.switchReadMode();")
            i.fa.fa-book
            span='阅读模式'
```

修改`[blogRoot]/themes/butterfly/layout/includes/layout.pug`

```pug
doctype html
html(lang=config.language data-theme=theme.display_mode class=htmlClassHideAside)
  head
    include ./head.pug
  body
    ...

    else
      include ./404.pug

    include ./rightside.pug
    !=partial('includes/third-party/search/index', {}, {cache: true})
+    !=partial('includes/rightmenu',{}, {cache:true})
    include ./additional-js.pug
```

### 2. 添加`rightmenu.js`文件

新建`[blogRoot]\source\static\js\rightmenu.js`

```js
let rmf = {};
rmf.showRightMenu = function(isTrue, x=0, y=0){
    let $rightMenu = $('#rightMenu');
    $rightMenu.css('top',x+'px').css('left',y+'px');

    if(isTrue){
        $rightMenu.show();
    }else{
        $rightMenu.hide();
    }
}
rmf.switchDarkMode = function(){
    const nowMode = document.documentElement.getAttribute('data-theme') === 'dark' ? 'dark' : 'light'
    if (nowMode === 'light') {
        activateDarkMode()
        saveToLocal.set('theme', 'dark', 2)
        GLOBAL_CONFIG.Snackbar !== undefined && btf.snackbarShow(GLOBAL_CONFIG.Snackbar.day_to_night)
    } else {
        activateLightMode()
        saveToLocal.set('theme', 'light', 2)
        GLOBAL_CONFIG.Snackbar !== undefined && btf.snackbarShow(GLOBAL_CONFIG.Snackbar.night_to_day)
    }
    // handle some cases
    typeof utterancesTheme === 'function' && utterancesTheme()
    typeof FB === 'object' && window.loadFBComment()
    window.DISQUS && document.getElementById('disqus_thread').children.length && setTimeout(() => window.disqusReset(), 200)
};
rmf.switchReadMode = function(){
    const $body = document.body
    $body.classList.add('read-mode')
    const newEle = document.createElement('button')
    newEle.type = 'button'
    newEle.className = 'fas fa-sign-out-alt exit-readmode'
    $body.appendChild(newEle)

    function clickFn () {
        $body.classList.remove('read-mode')
        newEle.remove()
        newEle.removeEventListener('click', clickFn)
    }

    newEle.addEventListener('click', clickFn)
}

//复制选中文字
rmf.copySelect = function(){
    document.execCommand('Copy',false,null);
    //这里可以写点东西提示一下 已复制
}

//回到顶部
rmf.scrollToTop = function(){
    btf.scrollToDest(0, 500);
}

// 右键菜单事件
if(! (navigator.userAgent.match(/(phone|pad|pod|iPhone|iPod|ios|iPad|Android|Mobile|BlackBerry|IEMobile|MQQBrowser|JUC|Fennec|wOSBrowser|BrowserNG|WebOS|Symbian|Windows Phone)/i))){
    window.oncontextmenu = function(event){
        $('.rightMenu-group.hide').hide();
        //如果有文字选中，则显示 文字选中相关的菜单项
        if(document.getSelection().toString()){
            $('#menu-text').show();
        }

        let pageX = event.clientX + 10;
        let pageY = event.clientY;
        let rmWidth = $('#rightMenu').width();
        let rmHeight = $('#rightMenu').height();
        if(pageX + rmWidth > window.innerWidth){
            pageX -= rmWidth+10;
        }
        if(pageY + rmHeight > window.innerHeight){
            pageY -= pageY + rmHeight - window.innerHeight;
        }



        rmf.showRightMenu(true, pageY, pageX);
        return false;
    };

    window.addEventListener('click',function(){rmf.showRightMenu(false);});
}
```

**需先在` _config.butterfly.yml`导入`jquery`**

```yml
# Inject
# Insert the code to head (before '</head>' tag) and the bottom (before '</body>' tag)
# 插入代码到头部 </head> 之前 和 底部 </body> 之前
inject:
  head:
   ....
  bottom:
    - <script type="text/javascript" src="https://unpkg.zhimg.com/jquery@latest/dist/jquery.min.js"></script> #一定要放在所有引入的js前面！！！
    ...
```

### 3. 添加`rightmenu.css`样式

新建`[blogRoot]\source\static\css\rightmenu.css`

```css
/* rightMenu */
#rightMenu{
    display: none;
    position: fixed;
    width: 160px;
    height: fit-content;
    top: 10%;
    left: 10%;
    background-color: var(--card-bg);
    border: 1px solid var(--font-color);
    border-radius: 8px;
    z-index: 100;
}
#rightMenu .rightMenu-group{
    padding: 7px 6px;
}
#rightMenu .rightMenu-group:not(:nth-last-child(1)){
    border-bottom: 1px solid var(--font-color);
}
#rightMenu .rightMenu-group.rightMenu-small{
    display: flex;
    justify-content: space-between;
}
#rightMenu .rightMenu-group .rightMenu-item{
    height: 30px;
    line-height: 30px;
    border-radius: 8px;
    transition: 0.3s;
    color: var(--font-color);
}
#rightMenu .rightMenu-group.rightMenu-line .rightMenu-item{
    display: flex;
    height: 40px;
    line-height: 40px;
    padding: 0 4px;
}
#rightMenu .rightMenu-group .rightMenu-item:hover{
    background-color: var(--text-bg-hover);
}
#rightMenu .rightMenu-group .rightMenu-item i{
    display: inline-block;
    text-align: center;
    line-height: 30px;
    width: 30px;
    height: 30px;
    padding: 0 5px;
}
#rightMenu .rightMenu-group .rightMenu-item span{
    line-height: 30px;
}

#rightMenu .rightMenu-group.rightMenu-line .rightMenu-item *{
    height: 40px;
    line-height: 40px;
}
.rightMenu-group.hide{
    display: none;
}
```

## 3. 隐藏左栏

#### 效果图

修改效果：在一些不需要展示过多信息的界面（如：归档，分类详情，友链等等）隐藏左栏

![image-20231223170552105](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-20231223170552105.png)

![image-20231223170522159](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-20231223170522159.png)

### 参考链接

忘记是从哪位大佬那借鉴的了，😓

### 1. 修改` _config.butterfly.yml`

```yml
# aside (側邊欄)
# --------------------------------------

aside:
  enable: true
  hide: false
  button: true
  mobile: true # display on mobile
  position: left # left or right
  display:
    archive: false
    tag: false
    category: false
    flink: false # 友链页隐藏侧栏
```

### 2. 修改`layout.pug`

修改`[blogRoot]\themes\butterfly\layout\includes\layout.pug`

```pug
    - var htmlClassHideAside = theme.aside.enable && theme.aside.hide ? 'hide-aside' : ''
    - page.aside = is_archive() ? theme.aside.display.archive: is_category() ? theme.aside.display.category : is_tag() ? theme.aside.display.tag : page.aside
+    - page.aside = page.type === 'link' ? theme.aside.display.flink : page.aside // - 友链页隐藏侧栏
    - var hideAside = !theme.aside.enable || page.aside === false ? 'hide-aside' : ''
    - var pageType = is_post() ? 'post' : 'page'
```

## 4. 页脚

### 效果图

![image-20231223171443231](https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/image-20231223171443231.png)

### 参考链接

[本站的一些样式魔改 | ichikaの小窝](https://ichika.cc/Article/beautiful_MyBeautiful/#页脚)

[猹的魔改日记-小小的重写个页脚 | 贰猹の小窝 (noionion.top)](https://noionion.top/46524.html)

### 1. 添加`footer.pug`文件

新建 `[blogRoot]\themes\butterfly\layout\includes\footer.pug`

```pug
#footer-wrap
  #footer-left
    .footer-title
      span= config.title + ' | '
      if theme.footer.owner.enable
        - var now = new Date()
        - var nowYear = now.getFullYear()
      if theme.footer.owner.since && theme.footer.owner.since != nowYear
        span.footer-copyright!= `&copy;${theme.footer.owner.since} - ${nowYear} By ${config.author}`
      else
        span.footer-copyright!= `&copy;${nowYear} By ${config.author}`
    .footer-button
      a(title='GitHub' href='https://github.com/June-PJ')
        i.fab.fa-github
      a(title='bilibili' href='https://space.bilibili.com/189242242?spm_id_from=333.1007.0.0')
        i.fab.fa-bilibili
      a(title='QQ' href='http://wpa.qq.com/msgrd?v=3&uin=1687434191&site=qq&menu=yes')
        i.fab.fa-qq
      a(title='邮箱' href='mailto:1687434191@qq.com')
        i.fas.fa-envelope
    .wordcount
    - let allword = totalcount(site)
    span= 'June 已经写了 ' + allword + ' 字，'
    if isNaN(allword)
      - allword= Number(allword.replace('k', ''))
      if allword< 50
        span= "还在努力更新中.. 加油！加油啦！"
      else if allword< 70
        span= "好像写完一本 埃克苏佩里 的 《小王子》 了啊"
      else if allword< 90
        span= "好像写完一本 鲁迅 的 《呐喊》 了啊"
      else if allword< 100
        span= "好像写完一本 林海音 的 《城南旧事》 了啊"
      else if allword< 110
        span= "好像写完一本 马克·吐温 的 《王子与乞丐》了！ 了啊"
      else if allword< 120
        span= "好像写完一本 鲁迅 的 《彷徨》 了啊"
      else if allword< 130
        span= "好像写完一本 余华 的 《活着》 了啊"
      else if allword< 140
        span= "好像写完一本 曹禺 的 《雷雨》 了啊"
      else if allword< 150
        span= "好像写完一本 史铁生 的 《宿命的写作》 了啊"
      else if allword< 160
        span= "好像写完一本 伯内特 的 《秘密花园》 了啊"
      else if allword< 170
        span= "好像写完一本 曹禺 的 《日出》 了啊"
      else if allword< 180
        span= "好像写完一本 马克·吐温 的 《汤姆·索亚历险记》 了啊"
      else if allword< 190
        span= "好像写完一本 沈从文 的 《边城》 了啊"
      else if allword< 200
        span= "好像写完一本 亚米契斯 的 《爱的教育》 了啊"
      else if allword< 210
        span= "好像写完一本 巴金 的 《寒夜》 了啊"
      else if allword< 220
        span= "好像写完一本 东野圭吾 的 《解忧杂货店》 了啊"
      else if allword< 230
        span= "好像写完一本 莫泊桑 的 《一生》 了啊"
      else if allword< 250
        span= "好像写完一本 简·奥斯汀 的 《傲慢与偏见》 了啊"
      else if allword< 280
        span= "好像写完一本 钱钟书 的 《围城》 了啊"
      else if allword< 300
        span= "好像写完一本 张炜 的 《古船》 了啊"
      else if allword< 310
        span= "好像写完一本 茅盾 的 《子夜》 了啊"
      else if allword< 320
        span= "好像写完一本 阿来 的 《尘埃落定》 了啊"
      else if allword< 340
        span= "好像写完一本 艾米莉·勃朗特 的 《呼啸山庄》 了啊"
      else if allword< 350
        span= "好像写完一本 雨果 的 《巴黎圣母院》 了啊"
      else if allword< 360
        span= "好像写完一本 东野圭吾 的 《白夜行》 了啊"
      else
        span= "好像写完一本我国著名的 四大名著 了！！！"
    else
      span= "还在努力更新中.. 加油！加油啦！"
  #footer-right
    .footer-totop
      i.fas.fa-chevron-up(onclick='rmf.scrollToTop()')
    .footer-info
      p= '使用Hexo框架 | 基于butterfly修改'
      //a(title='湘公网安备 2023003198号' href='http://www.beian.gov.cn/portal/registerSystemInfo?recordcode=2023003198')= '湘公网安备 2023003198号'
      a(title='湘ICP备2023003198号' href='https://beian.miit.gov.cn/')= '湘ICP备2023003198号'
      //a(title='萌ICP备20223993号' href='https://icp.gov.moe/?keyword=20223993')= '萌ICP备20223993号'
    .footer-service
      a(title='腾讯云' href='https://cloud.tencent.com')
        img(alt='腾讯云' src='https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/%E8%85%BE%E8%AE%AF%E4%BA%91.webp')
      //a(title='51LA' href='https://www.51.la')
      //  img(alt='51LA' src='https://cdn.ichika.cc/typora/202211071552427.png!towebp')
      a(title='CC BY-NC-SA 4.0' href='https://creativecommons.org/licenses/by-nc-sa/4.0/deed.zh')
        img(alt='CC BY-NC-SA 4.0' src='https://cdn.jsdelivr.net/gh/June-PJ/PicGo-PJ/img/cc.webp')
```

记得将信息改成自己的

**注意：**同上文一样，如果你没有定义`rmf.scrollToTop()`,可以替换为`btf.scrollToDest(0, 500)`

### 2. 添加`footer.js`文件

新建`[blogRoot]\source\static\js\footer.js`

```css
/* 页脚 */
.footer_custom_text a{
    margin:0 5px;
}

#footer::before{
    content:none;
}

#footer-wrap{
    color:var(--june-fontcolor);
    padding:50px 5% 35px 5%;
    display:flex;
    flex-wrap:wrap;
    background:var(--june-theme);
    position:relative;
}

#footer-wrap > div{
    width:50%;
}

#footer-left{
    text-align:left
}

.footer-title{
    font-size:1.5rem;
    font-weight:bold;
}

.footer-copyright{
    font-size:1rem;
    font-weight:normal;
}

#footer-wrap .footer-button {
    display: flex;
    margin: 15px 0;
}

#footer-wrap .footer-button > a {
    font-size: 1.3rem;
    margin-right:24px;
    transition: 0.2s;
    background: #566573;
    width: 40px;
    height: 40px;
    display: flex;
    border-radius: 50%;
    color: white;
}

#footer-wrap .footer-button > a:hover{
    background:var(--june-theme);
    transition:0.2s;
}

#footer-wrap .footer-button > a i{
    margin:auto;
    line-height:42px;
}

#footer-wrap .iconfont{
    font-size:1.3rem;
}

#footer-right {
    text-align: right;
    height: max-content;
    margin-top: auto;
}

#footer-right p,#footer-right a{
    color:var(--june-white);
}

.footer-totop {
    position: absolute;
    top: 20px;
    left: 50%;
    transform: translateX(-50%);
}

.footer-totop i {
    font-size: 2rem;
    animation: footerToTop 1.2s linear infinite;
}

.footer-info p{
    font-size:14px;
    margin:0;
}

.footer-info a{
    margin-left:20px;
    transition:0.2s;
}

.footer-info a:hover{
    color:var(--june-theme-op)!important;
    transition:0.2s;
}

.footer-info a:hover img{
    filter: none!important;
    transition:0.2s;
}

.footer-service img {
    height: 20px;
    filter: brightness(1000%); /* 将灰度图像提高亮度至白色 */
    margin-left: 20px;
    margin-top: 10px;
    transition: 0.2s;
}

.footer-service img:hover {
    filter: brightness(100%); /* 取消悬停时的滤镜效果 */
    transition: 0.2s;
}

@keyframes footerToTop{
    0%{
        transform:translateY(0);
    }
    60% {
        transform: translateY(-25%);
    }
    100% {
        transform: translateY(0);
    }
}

@media screen and (max-width:768px) {
    #footer-wrap > div {
        width: 100%;
        text-align:center;
    }

    #footer-wrap .footer-button > a{
        margin:0 auto;
    }
}
```

颜色自行调整
