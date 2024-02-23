---
title: test # 文章名称
date: 2022-11-16 13:52:46 # 文章发布日期
updated: 2024-01-20 15:52:46 # 文章更新日期
categories: 
- [hexo博客搭建与魔改]
- [数据结构与算法]
swiper_index: 1 #置顶轮播图顺序，非负整数，数字越大越靠前
---



---

# h1测试
hanser
<img src="https://hanser373.oss-cn-beijing.aliyuncs.com/blog/hanser.jpg"/>
## h2测试
在文章的front-matter里写sticky: 3可以置顶一写文章，数字越小  越靠前
```js
function changeColor() {
    // 仅夜间模式才启用
    if (document.getElementsByTagName('html')[0].getAttribute('data-theme') == 'dark') {
        if (document.getElementById("site-name"))
            document.getElementById("site-name").style.textShadow = arr[idx] + " 0 0 15px";
        if (document.getElementById("site-title"))
            document.getElementById("site-title").style.textShadow = arr[idx] + " 0 0 15px";
        if (document.getElementById("site-subtitle"))
            document.getElementById("site-subtitle").style.textShadow = arr[idx] + " 0 0 10px";
        if (document.getElementById("post-info"))
            document.getElementById("post-info").style.textShadow = arr[idx] + " 0 0 5px";
        try {
            document.getElementsByClassName("author-info__name")[0].style.textShadow = arr[idx] + " 0 0 12px";
            document.getElementsByClassName("author-info__description")[0].style.textShadow = arr[idx] + " 0 0 12px";
        } catch {

        }
        idx++;
        if (idx == 8) {
            idx = 0;
        }
    }
```
hanser
### h3测试
hanser
#### h4测试
##### h5测试
hanser



