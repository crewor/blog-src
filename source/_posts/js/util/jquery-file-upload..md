---
title: 基于jquery实现的文件上传工具
keywords: 多文件,上传,多文件上传,图片预览,图片,预览
date: 2018-11-22 10:35:45
---
##### 特性
    1.支持多文件上传
    2.支持图片预览
    3.支持图片预览删除
<!--more-->
##### 实现js代码
    /**
     *  基于jquery的文件上传工具
     * eg:
     <div id="pics">
     </div>
     $("#pics").dragUpload({
            callback: function (files) {
                console.log(files);
                //do upload
                for (var i = 0; i < files.length; i++) {
                    var fd = new FormData();
                    fd.append("file", files[i]);
                    var xhr = new XMLHttpRequest();
                    xhr.open("post", "upload", true);
                    xhr.onload = function (ev) {
                        console.log(ev)
                    }
                    xhr.upload.onprogress = function (evt) {
                        var loaded = evt.loaded;     //已经上传大小情况
                        var tot = evt.total;      //附件总大小
                        var per = Math.floor(100 * loaded / tot);  //已经上传的百分比
                        $("#son").html(per + "%");
                        $("#son").css("width", per + "%");
                    };
                    xhr.send(fd);
                }
            },
            deleteCallback:function (dom) {
                console.log("delete");
            }
        });
     */
    (function ($) {
        $.fn.extend({
            dragUpload: function (options) {
                return new DragUpload($(this), options)
            }
        });
    
        function DragUpload(dom, options) {
            this.me = dom;
            var defaultOpt = {
                boxWidth: '200px',
                boxHeight: 'auto',
                border: '1px solid #cccccc'
            }
            this.delIcon = $("<img src='data:image/png;base64,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'>");
            this.preview = $('<div id="preview"><img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAMgAAACWCAMAAACsAjcrAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAyZpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuNi1jMTM4IDc5LjE1OTgyNCwgMjAxNi8wOS8xNC0wMTowOTowMSAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIiB4bWxuczp4bXBNTT0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL21tLyIgeG1sbnM6c3RSZWY9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9zVHlwZS9SZXNvdXJjZVJlZiMiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENDIDIwMTcgKFdpbmRvd3MpIiB4bXBNTTpJbnN0YW5jZUlEPSJ4bXAuaWlkOjcwMjIwNjU3RDc5RTExRThCREM3RUZGQzM3NEE1QzNEIiB4bXBNTTpEb2N1bWVudElEPSJ4bXAuZGlkOjcwMjIwNjU4RDc5RTExRThCREM3RUZGQzM3NEE1QzNEIj4gPHhtcE1NOkRlcml2ZWRGcm9tIHN0UmVmOmluc3RhbmNlSUQ9InhtcC5paWQ6NzAyMjA2NTVENzlFMTFFOEJEQzdFRkZDMzc0QTVDM0QiIHN0UmVmOmRvY3VtZW50SUQ9InhtcC5kaWQ6NzAyMjA2NTZENzlFMTFFOEJEQzdFRkZDMzc0QTVDM0QiLz4gPC9yZGY6RGVzY3JpcHRpb24+IDwvcmRmOlJERj4gPC94OnhtcG1ldGE+IDw/eHBhY2tldCBlbmQ9InIiPz66aW0mAAAAYFBMVEXg4ODZ2dn39/fx8fHd3d3Dw8Po6OjGxsavr6+0tLTPz8/X19f///+xsbHR0dHt7e2/v791dXWrq6sAAACMjIy9vb2BgYGhoaGXl5ezs7P8/Py5ubn+/v7j4+P6+vrIyMhTuhqtAAADYUlEQVR42uydi3ajIBCGERKNStsVEW3abt//LVdRFHPZdJsGgf3nNMfKRflxvmFIck4Ij8QIhHgopO7/An9F8kTq2ITUETGCqAVGwAgYASNgBIyAETACRsAIGAEjYASMgBEwAkbACBgBI2AEjIARMAJGwIivjBRVVcl/dpC+U+UZIxsI4aELqWMTAkbCZ2QZpLnzUJLzTPaHNj0T0gzlqp1vxoQa+smccquFEtQ1I1eEDMMdrFm3KStjHZ27Tzben6nxTGWOGbksZLHMbmNXKWb6mAJ9Fbnq7ZCRa0IE5URPLl3apNqJCKd6ttu+pB0KGKfCPJJG11CeysoLRioxu0mztGnXbUvOadkqzdFwXvRHNSkc/9uekYrpomGiu6WNGe2kqbUuI8cqtsDSeMGIXCrVXFJaQ8usQEbLXE0ay5mVVevtGJGrtWAqsaPwHFzLThmyi0t9N2bkq0KYskLUTwj5CUYKS4haVU4Hcu5aOjZJUTC5uFZ1j2vdx0g1j22GfZyV/K+wi3GwqYlRfRW9F/bvM7KMRdpClhBqhd9u7YZkeZyp0ShNX6YcM6LnUKY87arVgtixcVFT1oJIThfEYspiChO1plkwi6nT/Uh3mlOsU5TC5iizKuS86lV2Xtbel6LcsR+ZA4/Oa82ws8tJ4yKyW53rFLFbTUwuXe9H6LCcqT5jt4VoV1MX0viiU3Yaz9r+VDZjIJhaD2l8N19uuz37t7ZRPr6v5YWQOnQhPDYhYCRGRjY3fD7ik+HzETACRsAIGAEjITBCm10UjBz34uklBkYKIcQbDZ+RnRhsfwydkfSghYjnwBmhb2IyEjQjv/dGx6OBJ48H3djjgH88IzthW3YMlREDurHnUBnJxYntAmWkTJLERK3Dc5KUJFRGesuMkAeu7U5yLRN/DyzwXMulEB66kDo2IWAEjIARh4zsRc5uCnl5FZnvjBxOtlAXhfRZ8ZPvjDx9RQgR4tV3RrYUUocuhLsUIhwIeahrffwarTgGzsjl6QuQERdCeGxConEtMBI7Ix83G30EwEifNH4mtywLgJGzdxev2JvvjCRfFLLznZF3sm9u2568h7Znd2H4LoqPhu+igBEwAkbACBgBI2AEjIARMAJGwAgYASNgBIz8j4zgZwTBCIRcsT8CDAAALp3HNxEDtwAAAABJRU5ErkJggg==" class="img-responsive"  style="width: 100%;height: auto;" alt="" title=""> </div>');
            this.opts = $.extend(true, defaultOpt, {}, options);
            this.init();
            this.callback = this.opts.callback;
            this.deleteCallback = this.opts.deleteCallback;
        }
    
        DragUpload.prototype = {
            init: function () {
                this.me.append(this.preview);
                this.cssInit();
                this.eventClickInit();
            },
            cssInit: function () {
                var self = this;
                this.me.css({
                    'width': this.opts.boxWidth,
                    'height': this.opts.boxHeight,
                    'border': this.opts.border,
                    'padding': '10px',
                    'cursor': 'pointer',
                    'position': 'relative'
                }),
                this.preview.css({
                    'height': '100%',
                    'overflow': 'hidden'
                }),
                this.delIcon.css({
                    'display': true,
                    'width': '20px',
                    'height': '20px',
                    'position': 'absolute',
                    'top': '-10px',
                    'right': '-10px'
                }),
                this.delIcon.hover(function () {
                    self.delIcon.css({
                        'width': '22px',
                        'height': '22px'
                    })
                },function () {
                    self.delIcon.css({
                        'width': '20px',
                        'height': '20px'
                    })
                })
    
            },
            onDragOver: function (e) {
                e.stopPropagation();
                e.preventDefault();
                e.dataTransfer.dropEffect = 'copy';
            },
            onDrop: function (e) {
                e.stopPropagation();
                e.preventDefault();
                var files = e.dataTransfer.files; //获取文件对象
                // do something upload
                if (files.length == 0) {
                    return false;
                }
                if (!this.validate(files)) {
                    return;
                }
                this.preViewFiles(files);
                if (this.callback) {
                    this.callback(files);
                }
            },
            eventClickInit: function () {
                var self = this;
                this.me.unbind().click(function () {
                    self.createImageUploadDialog();
                })
                var dp = this.me[0];
                dp.addEventListener('dragover', function (e) {
                    self.onDragOver(e);
                });
                dp.addEventListener("drop", function (e) {
                    self.onDrop(e);
                });
    
    
            },
            onChangeUploadFile: function () {
                var files = this.fileInput.files;
                if (!this.validate(files)) {
                    return;
                }
                this.preViewFiles(files);
                if (this.callback) {
                    this.callback(files);
                }
            },
            preViewFiles: function (files) {
                //支持多个图片同时上传,根据文件数量进行计算，应该分割成多少个小方块 1，4，9，16，即 图片数量 <= X^2
                var num = 1;
                while (files.length > num * num) {
                    num++;
                }
                this.me.find("#preview").empty();
                for (var i = 0; i < files.length; i++) {
                    var img = window.URL.createObjectURL(files[i]);
                    var div = $("<div></div>").css("width", (100 / num) + "%").css("height", (100 / num) + "%").css("float", "left");
                    var imgDom = $("<img/>").css("width", "100%").attr("src", img);
                    div.append(imgDom);
                    this.me.find("#preview").append(div);
                }
                this.showDelIcon();
            },
            validate: function (files) {
                //校验，文件大小，类型
                var fileSize = Math.floor((files[0].size) / 1024);
                if (fileSize > 500000000000) {
                    return false;
                }
                //检测文件是不是图片
                if (files[0].type.indexOf('image') === -1) {
                    alert("您拖的不是图片！");
                    return false;
                }
                return true;
            },
            createImageUploadDialog: function () {
                if (!this.fileInput) {
                    //创建临时input元素
                    var fileInput = document.createElement('input');
                    //设置input type为文件类型
                    fileInput.type = 'file';
                    //设置文件name
                    fileInput.name = 'ime-images';
                    //允许上传多个文件
                    fileInput.multiple = true;
                    fileInput.onchange = this.onChangeUploadFile.bind(this);
                    this.fileInput = fileInput;
                }
                //触发点击input点击事件，弹出选择文件对话框
                this.fileInput.click();
            },
            showDelIcon:function () {
                var self = this;
                self.me.append(this.delIcon);
                this.delIcon.click(function () {
                    self.me.remove();
                    self.deleteCallback&&self.deleteCallback(self);
                });
            }
        }
    })(jQuery)