---
title: 基于jquery实现的一种简易的页面脱敏工具
---
##### 基于{% link jquery https://jquery.com %}实现的一种简易的页面脱敏工具,主要用来将页面明文用户名，联系方式进行脱敏，此工具使用了ES5中的{% link Object.defineProperty https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty %}方法进行实现，所以只有支持此特性的浏览器可以使用
##### 例如
    用户名: 李某某 ——> 李*某(点击查看)
    联系方式: 15811111111 ——> 15*******11(点击查看)
<!--more-->
##### 实现js代码
    $(function () {
        /**
         * 脱敏js使用方式
         * <td class="__safe_firm_phone__">phone</td>
         * <span class="__safe_firm_phone__">phone</span>
         *
         */
    
        var __safe_firm_phone__prefix = "__safe_firm_phone__";
        var __safe_firm_user_name__prefix = "__safe_firm_user_name__";
    
        function wrapperTextDom(dom, firmFunc) {
            if ($.inArray(dom.tagName, ["TD", "SPAN", "B"]) > -1) {
                //如何动态修改值?
                var htmlValue = $(dom).html();
                //todo 对显示内容进行包装，使html内容改变时可以同时改变显示
                var tmp = $("<span style='cursor: pointer;'></span>");
                //
                $(dom).html(tmp);//将html替换为tmp
                //增加get，set 特性，在set和get的时候返回正确的内容
                Object.defineProperty(dom, "innerHTML", {
                    enumerable: true,
                    configurable: true,
                    get: function () {
                        return htmlValue;
                    },
                    set: function (v) {
                        htmlValue = v;
                        //修改显示
                        tmp.attr("data-value", v).html(firmFunc(v) + "(点击查看)");
                        tmp.click(function() {
                            this.innerHTML = this.getAttribute("data-value");
                        });
                    }
                });
                //set src value
                $(dom).html(htmlValue);
            }
        }
    
    
        function firmUserName(userName) {
            if (/^.+$/.test(userName)) {
                return replaceStr(userName, 1, userName.length > 2 ? userName.length - 1 : userName.length, "*");
            }
            return userName;
        }
    
        function replaceStr(str, start, end, replaceChar) {
            var res = "";
            var len = str.length;
            if (len <= start) {
                return str;
            }
            if (len < end) {
                end = len;
            }
            str = str.split("");
            for (var i = 0; i < str.length; i++) {
                var c = str[i];
                if (i > start - 1 && i < end) {
                    c = replaceChar;
                }
                res = res + c;
            }
            return res;
        }
    
        function firmPhone(phone) {
            if (/^1[34578]\d{9}$/.test(phone)) {
                //手机
                return phone.substring(0, 2) + "*******" + phone.substring(9);
            } else if (/^(\d{3,4}-)?\d{7,14}$/.test(phone)) {
                //电话
                return phone.substring(0, 6) + "***" + phone.substring(9);
            }
            return phone;
        }
    
        $.extend({
            "desensitize": function () {
                //防止异常导致js执行中断
                try {
                    $(".__safe_firm_phone__").each(function () {
                        try {
                            wrapperTextDom(this, firmPhone);
                        } catch (e) {
                            console.log(e);
                        }
                    });
                    //do hide
                    $(".__safe_firm_user_name__").each(function () {
                        try {
                            wrapperTextDom(this, firmUserName);
                        } catch (e) {
                            console.log(e);
                        }
                    });
                } catch (e) {
                    console.log(e);
                }
            }
        });
        //todo 触发脱敏方法
        $.desensitize();
    });