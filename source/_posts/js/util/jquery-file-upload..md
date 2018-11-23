---
title: 基于jquery实现的文件上传工具
keywords: 多文件,上传,多文件上传,图片预览,图片,预览
date: 2018-11-22 10:35:45
---
##### 特性
    1.支持多文件上传
    2.支持图片预览
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
                border: '1px solid #cccccc',
            }
            this.preview = $('<div id="preview"><img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAMgAAACWCAMAAACsAjcrAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAyZpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuNi1jMTM4IDc5LjE1OTgyNCwgMjAxNi8wOS8xNC0wMTowOTowMSAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIiB4bWxuczp4bXBNTT0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL21tLyIgeG1sbnM6c3RSZWY9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9zVHlwZS9SZXNvdXJjZVJlZiMiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENDIDIwMTcgKFdpbmRvd3MpIiB4bXBNTTpJbnN0YW5jZUlEPSJ4bXAuaWlkOjcwMjIwNjU3RDc5RTExRThCREM3RUZGQzM3NEE1QzNEIiB4bXBNTTpEb2N1bWVudElEPSJ4bXAuZGlkOjcwMjIwNjU4RDc5RTExRThCREM3RUZGQzM3NEE1QzNEIj4gPHhtcE1NOkRlcml2ZWRGcm9tIHN0UmVmOmluc3RhbmNlSUQ9InhtcC5paWQ6NzAyMjA2NTVENzlFMTFFOEJEQzdFRkZDMzc0QTVDM0QiIHN0UmVmOmRvY3VtZW50SUQ9InhtcC5kaWQ6NzAyMjA2NTZENzlFMTFFOEJEQzdFRkZDMzc0QTVDM0QiLz4gPC9yZGY6RGVzY3JpcHRpb24+IDwvcmRmOlJERj4gPC94OnhtcG1ldGE+IDw/eHBhY2tldCBlbmQ9InIiPz66aW0mAAAAYFBMVEXg4ODZ2dn39/fx8fHd3d3Dw8Po6OjGxsavr6+0tLTPz8/X19f///+xsbHR0dHt7e2/v791dXWrq6sAAACMjIy9vb2BgYGhoaGXl5ezs7P8/Py5ubn+/v7j4+P6+vrIyMhTuhqtAAADYUlEQVR42uydi3ajIBCGERKNStsVEW3abt//LVdRFHPZdJsGgf3nNMfKRflxvmFIck4Ij8QIhHgopO7/An9F8kTq2ITUETGCqAVGwAgYASNgBIyAETACRsAIGAEjYASMgBEwAkbACBgBI2AEjIARMAJGwIivjBRVVcl/dpC+U+UZIxsI4aELqWMTAkbCZ2QZpLnzUJLzTPaHNj0T0gzlqp1vxoQa+smccquFEtQ1I1eEDMMdrFm3KStjHZ27Tzben6nxTGWOGbksZLHMbmNXKWb6mAJ9Fbnq7ZCRa0IE5URPLl3apNqJCKd6ttu+pB0KGKfCPJJG11CeysoLRioxu0mztGnXbUvOadkqzdFwXvRHNSkc/9uekYrpomGiu6WNGe2kqbUuI8cqtsDSeMGIXCrVXFJaQ8usQEbLXE0ay5mVVevtGJGrtWAqsaPwHFzLThmyi0t9N2bkq0KYskLUTwj5CUYKS4haVU4Hcu5aOjZJUTC5uFZ1j2vdx0g1j22GfZyV/K+wi3GwqYlRfRW9F/bvM7KMRdpClhBqhd9u7YZkeZyp0ShNX6YcM6LnUKY87arVgtixcVFT1oJIThfEYspiChO1plkwi6nT/Uh3mlOsU5TC5iizKuS86lV2Xtbel6LcsR+ZA4/Oa82ws8tJ4yKyW53rFLFbTUwuXe9H6LCcqT5jt4VoV1MX0viiU3Yaz9r+VDZjIJhaD2l8N19uuz37t7ZRPr6v5YWQOnQhPDYhYCRGRjY3fD7ik+HzETACRsAIGAEjITBCm10UjBz34uklBkYKIcQbDZ+RnRhsfwydkfSghYjnwBmhb2IyEjQjv/dGx6OBJ48H3djjgH88IzthW3YMlREDurHnUBnJxYntAmWkTJLERK3Dc5KUJFRGesuMkAeu7U5yLRN/DyzwXMulEB66kDo2IWAEjIARh4zsRc5uCnl5FZnvjBxOtlAXhfRZ8ZPvjDx9RQgR4tV3RrYUUocuhLsUIhwIeahrffwarTgGzsjl6QuQERdCeGxConEtMBI7Ix83G30EwEifNH4mtywLgJGzdxev2JvvjCRfFLLznZF3sm9u2568h7Znd2H4LoqPhu+igBEwAkbACBgBI2AEjIARMAJGwAgYASNgBIz8j4zgZwTBCIRcsT8CDAAALp3HNxEDtwAAAABJRU5ErkJggg==" class="img-responsive"  style="width: 100%;height: auto;" alt="" title=""> </div>');
            this.opts = $.extend(true, defaultOpt, {}, options);
            this.init();
            this.callback = this.opts.callback;
        }
    
        DragUpload.prototype = {
            init: function () {
                this.me.append(this.preview);
                this.cssInit();
                this.eventClickInit();
            },
            cssInit: function () {
                this.me.css({
                    'width': this.opts.boxWidth,
                    'height': this.opts.boxHeight,
                    'border': this.opts.border,
                    'padding': '10px',
                    'cursor': 'pointer'
                })
                this.preview.css({
                    'height': '100%',
                    'overflow': 'hidden'
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
                    div.append(imgDom)
                    this.me.find("#preview").append(div);
                }
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
            }
        }
    })(jQuery)