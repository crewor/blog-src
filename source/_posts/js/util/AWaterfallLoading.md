---
title: 基于jquery实现的瀑布式滚动加载工具
keywords: jquery滚动加载工具,滚动加载工具,js,js滚动加载工具,js加载工具,js加载,加载工具,加载,js瀑布加载工具,瀑布加载工具
date: 2018-07-22 08:35:45
---
##### 瀑布式滚动加载js监听工具
<!--more-->
#### 源码
    /**
     * param1 滚动容器，例如div body 等要监听的滚动条
     * param2 触发的加载方法
     * param3 距离底部多少距离加载
     **/
    function RegScrollEvent() {
        var v = {};
        var win = arguments[arguments.length - 3]||$(window);
        win.mcs_scroll_var = v;
        if (!isNaN(arguments[arguments.length - 1])) {
            v.dest = arguments[arguments.length - 1];
        } else {
            v.dest = 0;
        }
    
        if (typeof(arguments[arguments.length - 2]) == 'function') {
            var fun = arguments[arguments.length - 2];
            v.func = fun;
        }
        var lastScrollTop = -1;
        win.scroll(function (arguments) {
            try {
                var scrollTop = $(this).scrollTop();
                var scrollHeight = $(document).height();
                var windowHeight = $(this).height();
                if (scrollTop >= lastScrollTop){
                    lastScrollTop = scrollTop;
                    if (scrollTop + windowHeight >= scrollHeight - v.dest) {
                        if (typeof (v.func) == 'function') {
                            v.func();
                        }
                    }
                } else {
                }
            } catch (e) {
                console.log(e);
            } finally {
            }
        });
    }