---
title: 可搜索下拉框select
keywords: 可搜索下拉框select,select,selectbox,select box
date: 2019-01-16 16:05:25
---
##### 基于jquery实现的简单的可搜索select box 插件
    1.实现select下拉框
    2.搜索和展示的值应该设置一下
    3.选中后回调,返回当前li和对应option
    使用方式：
        $(function () {
           var wrapperSelect = $.wrapperSelect($("#brand"));
           1.重新加载，调用 wrapperSelect.refresh();
           2.销毁，wrapperSelect.destroy();
        })
<!--more-->
{% link demo https://crewor.github.io/sources/docs/jquery/extends/selectbox/selectbox.html %}
##### 实现代码
    js
        (function ($) {
            /**
             * 停止事件冒泡
             * @param arguments
             * @param event
             */
            function stopPropagation(arguments, event) {
                event.stopPropagation();
                var e = arguments.callee.caller.arguments[0] || event; //若省略此句，下面的e改为event，IE运行可以，但是其他浏览器就不兼容
                if (e && e.stopPropagation) {
                    // this code is for Mozilla and Opera
                    e.stopPropagation();
                } else if (window.event) {
                    // this code is for IE
                    window.event.cancelBubble = true;
                }
            }
        
            $.extend({
                /**
                 * 1.实现select下拉框
                 * 2.搜索和展示的值应该设置一下
                 * 3.选中后回调,返回当前li和对应option
                 * $(function () {
                 *    var wrapperSelect = $.wrapperSelect($("#brand"));
                 *    1.重新加载，调用 wrapperSelect.refresh();
                 *    2.销毁，wrapperSelect.destroy();
                 * })
                 *
                 */
                wrapperSelect: function ($srcSelect, opts) {
                    var setting = $.extend({
                        chooseFunCall: function ($selectedOption) {
                            $srcSelect.val($selectedOption.value).trigger("change");
                        }
                    }, opts);
        
                    var chooseFunCall = setting.chooseFunCall;
        
                    var optionsFun = function (option) {
                        //返回每个<li>$html</li>的html
                        return $.trim(option.innerHTML);
                    };
                    var data = [];
                    $srcSelect.find("option").each(function (index, option) {
                        data.push({
                            searchTitle: optionsFun(option),
                            option: option
                        });
                    });
                    var panel = $(" <div class=\"select-box\">\n" +
                        " <span class=\"select-box-input-for-show select-box-input form-control text-center\">\n" +
                        " <span class=\"select-box-down-span-title\">请选择</span><i class=\"dropdown-toggle\"></i>\n" +
                        " </span>\n" +
                        " <span class=\"select-box-input-for-input select-box-input form-control hide\">\n" +
                        " <input class=\"select-box-input\" type=\"text\" value=\"\" placeholder=\"请选择\"/>\n" +
                        " </span>\n" +
                        "\n" +
                        " <ul class=\"select-box-options hide\">\n" +
                        " </ul>\n" +
                        " </div>");
                    //将内容展示到页面
                    var width = $srcSelect.width();
                    //父元素宽度
                    var pwidth = $srcSelect.parent().width();
                    panel.width(width > 0 ? (width > pwidth ? pwidth : width) : "100%");
                    $srcSelect.after(panel);
                    $srcSelect.hide();
                    var selectOptions = panel.find(".select-box-options");
                    //操作options ul的一些方法
                    var selectOptionsFun = {
                        //选中前一个
                        pre: function () {
                            this.current(selectOptions.find("li.current").index() - 1);
                        },
                        //选中下一个
                        next: function () {
                            this.current(selectOptions.find("li.current").index() + 1);
                        },
                        //设置当前选中位置
                        current: function (i) {
                            i = i < 0 ? 0 : (i > selectOptions.find("li").length - 1 ? 0 : i);
                            selectOptions.find("li").removeClass("current").eq(i).addClass("current");
                            //移动滚动条的位置
                            var currentTop = selectOptions.find("li.current")[0].offsetTop;
                            selectOptions.scrollTop(currentTop - selectOptions.height() / 2);
                        },
                        //确定选中，执行当前选中事件
                        choose: function () {
                            //确认方法
                            selectOptions.find("li.current").trigger("click");
                        }
                    }
        
                    var inputSpan4Show = panel.find(".select-box-input-for-show");
                    var inputSpan4ShowTitle = inputSpan4Show.find(".select-box-down-span-title");
        
                    var inputSpan4Input = panel.find(".select-box-input-for-input");
        
                    inputSpan4Input.find("input").bind({
                        compositionstart: function () {
                            window.__select_box_lock_ = true;
                        },
                        compositionend: function () {
                            window.__select_box_lock_ = false;
                            filterData();
                        },
                        input: function (e) {
                            if (!window.__select_box_lock_) {
                                filterData();
                            }
                        },
                        keydown: function (e) {
                            if (e && e.keyCode == 38) {//上,左
                                selectOptionsFun.pre();
                            } else if (e && e.keyCode == 40) {//下,右
                                selectOptionsFun.next();
                            } else if (e && e.keyCode == 13) {//下,右
                                selectOptionsFun.choose();
                            }
                        }
                    });
        
                    var hideSelectOptions = function () {
                        inputSpan4Show.show();
                        inputSpan4Input.hide().find("input").val("");
                        selectOptions.hide();
                    }
                    var showSelectOptions = function () {
                        filterData();
                        selectOptions.removeClass("hide").show();
                        inputSpan4Show.hide();
                        inputSpan4Input.removeClass("hide").show().find("input").focus();
                    }
                    //添加点击其他地方隐藏元素
                    $(document).click(function () {
                        hideSelectOptions();
                    });
                    inputSpan4Show.click(function () {
                        showSelectOptions();
                        stopPropagation(arguments, event);
                    });
                    //选中事件
                    var chooseFun = function ($select) {
                        //设置当前显示值
                        inputSpan4ShowTitle.html($select.searchTitle).attr("title", $select.searchTitle);
                        chooseFunCall && chooseFunCall($select.option);
                    };
        
                    var filterData = function () {
                        var $selectOptions = selectOptions;
                        $selectOptions.empty();
                        var key = $.trim(inputSpan4Input.find("input").val());
                        //trim
                        //输入后，filter
                        $.each(data, function (index, $data) {
                            var searchStr = $data.searchTitle;
                            var i = searchStr.search(eval("/" + key + "/i"));
                            if (i > -1) {
                                var $li = $("<li>" + searchStr + "</li>");
                                $li.click(function () {
                                    chooseFun($data);
                                });
                                $selectOptions.append($li);
                            }
                        });
                        //默认选中第一个元素
                        selectOptionsFun.current(0);
                    }
                    filterData();
                    var res = {
                        inputSpan4ShowTitle: inputSpan4ShowTitle,
                        select: $srcSelect,
                        data: data,
                        dom: panel,
                        destory: function () {
                            this.dom.remove();
                            this.select.show();
                        },
                        refresh: function () {
                            this.destory();
                            $.wrapperSelect(this.select);
                        }
                    }
                    return res;
                }
            })
        })(jQuery)
    css:
        .select-box .hide {
            display: none !important;
        }
        
        .select-box input,.select-box span {
            width: 100%;
            white-space: nowrap;
            overflow: hidden;
        }
        
        .select-box input {
            outline: none;
            border: 0;
        }
        
        .select-box .select-box-options {
            list-style: none;
            position: absolute;
            background: #fff;
            z-index: 999;
            margin-top: 1px;
            min-width: 100%;
            white-space: nowrap;
            overflow: auto;
            max-height: 300px;
            border-radius: 4px;
            padding: 0;
            border: 1px solid #ddd;
            -webkit-box-shadow: 0 4px 10px 2px rgba(0, 0, 0, 0.28);
            box-shadow: 0 4px 10px 2px rgba(0, 0, 0, 0.28);
        }
        
        .select-box .select-box-options li {
            line-height: 25px;
            padding: 2px 15px 2px 15px;
        }
        
        .select-box .select-box-options li:hover {
            background: #C1C1C1;
        }
        .select-box .select-box-options li.current {
            background: #C1C1C1;
        }
        .select-box {
            position: relative;
            display: inline-block;
        }
        
        /**
            after 三角
         */
        .select-box .dropdown-toggle::after {
            display: inline-block;
            width: 0;
            height: 0;
            margin-left: .255em;
            vertical-align: .255em;
            content: "";
            border-top: .3em solid;
            border-right: .3em solid transparent;
            border-bottom: 0;
            border-left: .3em solid transparent;
        }
        
        .select-box .form-control {
            display: inline-block;
            border: 1px solid #ccc;
            border-radius: 4px;
            padding: 0;
            position: relative;
            line-height: 20px;
            width: 100%;
        }
        .select-box .select-box-input {
            width: 100%;
            display: inline-block;
            line-height: 20px;
        }
        
        .select-box .text-center {
            text-align: center;
        }