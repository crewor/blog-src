---
title: nginx反向代理重定向地址错误
---
##### nginx反向代理重定向地址错误
<!--more-->
##### 备注：未进行测试，但大概原理是这样，此文档只是为了进行一点提示和引导，防止遇见此类问题无处下手。
使用nginx做代理的时候，如果项目需要rediret:/action ... 需要配置 【proxy_redirect】
例如：如果nginx端口为82端口，项目域名为 content.crewor.com/web ，片段配置如下：

    server{
        listen 82;
        upstream webStream{
            server 192.168.0.253:8080 weight=50;
            server 192.168.0.193:8889 weight=1;
        }
         location /web/ {
             proxy_pass   http://webStream/web/;
             proxy_redirect http://webStream/web/ http://content.crewor.com/web/;
        }
    }
    proxy_redirect:http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_redirect

##### 访问:
    http://content.crewor.com/web/action
##### 这个地址会进行redirect 当前项目的：
	http://content.crewor.com/web/newaction
##### 如果不配置proxy_redirect则会重定向到
	http://content.crewor.com:【82】/web/newaction
#### 注意：
    他会加上nginx的端口，这是因为response中有浏览器重定向的参数
    “Location” and “Refresh” header fields of a proxied server response.
    被代理服务器“Location” 默认为http://content.crewor.com:【82】/web/newaction
    所以需要配置proxy_redirect 替换http://content.crewor.com:【82】 为http://content.crewor.com。
