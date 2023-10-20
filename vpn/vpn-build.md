1. 加速器安装报错，dpkg: error: cannot access archive 'libssl1.0.0_1.0.1t-1+deb8u7_amd64.deb': No such file or directory 安装这个ssl 的dev包，sudo apt install swig libssl-dev即可。(其实安装后，还是报错，但最后稀里糊涂可以了)
=======================================================

 ShadowsocksR账 号  配 置 信 息 ：                                                                                            

 I  P       : 167.179.111.224                                                                                                            

 端 口        : 1130                                                                                                                             

 密 码        : daodao                                                                                                                        

 加 密        : aes-256-cfb                                                                                                                 

 协 议        : origin                                                                                                                           

 混 淆        : tls1.2_ticket_auth_compatible     

 设 备 数 限 制  : 0(无 限 )     

 单 线 程 限 速  : 0 KB/S      

 端 口 总 限 速  : 0 KB/S     

 SS    链 接  : ss://YWVzLTI1Ni1jZmI6ZGFvZGFvQDE2Ny4xNzkuMTExLjIyNDoxMTMw     

 SS  二 维 码  : [http://doub.pw/qr/qr.php?text=ss://YWVzLTI1Ni1jZmI6ZGFvZGFvQDE2Ny4xNzkuMTExLjIyNDoxMTMw](http://doub.pw/qr/qr.php?text=ss://YWVzLTI1Ni1jZmI6ZGFvZGFvQDE2Ny4xNzkuMTExLjIyNDoxMTMw)                                                                

 SSR   链 接  : ssr://MTY3LjE3OS4xMTEuMjI0OjExMzA6b3JpZ2luOmFlcy0yNTYtY2ZiOnRsczEuMl90aWNrZXRfYXV0aDpaR0Z2WkdGdg                                                       

 SSR 二 维 码  : [http://doub.pw/qr/qr.php?text=ssr://MTY3LjE3OS4xMTEuMjI0OjExMzA6b3JpZ2luOmFlcy0yNTYtY2ZiOnRsczEuMl90aWNrZXRfYXV0aDpaR0Z2WkdGdg](http://doub.pw/qr/qr.php?text=ssr://MTY3LjE3OS4xMTEuMjI0OjExMzA6b3JpZ2luOmFlcy0yNTYtY2ZiOnRsczEuMl90aWNrZXRfYXV0aDpaR0Z2WkdGdg)        

  提 示 :  

 在 浏 览 器 中 ， 打 开 二 维 码 链 接 ， 就 可 以 看 到 二 维 码 图 片 。

 协 议 和 混 淆 后 面 的 [ _compatible ]， 指 的 是  兼 容 原 版 协 议 /混 淆 。 

2. 未测试
------------------------------------------------------------------------------------------------------

**手把手教你从如何从公司内网****访问外网****（非翻墙）**

万次阅读 多人点赞

## 2021-06-28 00:39:32背景

不能上外网？是可忍孰不可忍。

根据公司安全要求，在办公网络是无法访问外网的，甚至打开百度都只能靠页面快照。作为一名程序员，不能百度是一件无法忍受的事情。幸好不是完全的网络隔离，而是屏蔽了几乎所有能上传文件的网站。如果你也遇到类似的情况，请参考下面文档，给你将如何突破这种限制。

>友情提示：技术无罪，方便自己同时，不要故意违反公司规定，损害公司利益。
## 技术选型

1. nginx，正向代理老牌
2. squid，请参考我另外一篇博客。
## 
## 说明

因为 Nginx 不支持 CONNECT，所以无法正向代理 Https 网站（网上银行，Gmail）。

如果访问 Https 网站，比如：[https://www.google.com](https://www.google.com)，Nginx access.log 日志如下：

```plain
"CONNECT www.google.com:443 HTTP/1.1" 400
```
所以nginx需要安装`http_proxy_connect`插件来实现https的代理,注意版本关联关系
[https://github.com/chobits/ngx_http_proxy_connect_module/blob/master/README.md](https://github.com/chobits/ngx_http_proxy_connect_module/blob/master/README.md)

## 资源准备

```plain
## ngx_http_proxy_connect_
git clone https://github.com/chobits/ngx_http_proxy_connect_module.git
## nginx-1.9.2
 wget http://nginx.org/download/nginx-1.9.2.tar.gz
```
## 安装nginx插件

把http_proxy_connect插件安装到nginx中

```plain
[root@server1 nginx-1.9.2]# patch -p1 < ../ngx_http_proxy_connect_module/patch/proxy_connect.patch

## log 务必所有都sucess
patching file src/http/ngx_http_core_module.c
patching file src/http/ngx_http_parse.c
patching file src/http/ngx_http_request.c
Hunk #1 succeeded at 959 (offset -9 lines).
Hunk #2 succeeded at 1565 (offset -8 lines).
patching file src/http/ngx_http_request.h
Hunk #1 succeeded at 41 (offset -1 lines).
Hunk #2 succeeded at 402 (offset -2 lines).
patching file src/http/ngx_http_variables.c

```
安装nginx
```plain
./configure --add-module=../ngx_http_proxy_connect_module
make && make install
```
## 修改配置

/usr/local/nginx/conf/nginx.conf

```plain
 server {
     listen                         1080;
 
     # dns resolver used by forward proxying
     resolver                       8.8.8.8;
 
     # forward proxy for CONNECT request
     proxy_connect;
     proxy_connect_allow            443 563; #许可https和nntps协议通过
     proxy_connect_connect_timeout  10s;
     proxy_connect_read_timeout     10s;
     proxy_connect_send_timeout     10s;
 
     # forward proxy for non-CONNECT request
     location / {
         proxy_pass http://$host;
         proxy_set_header Host $host;
     }
 }
```
验证配置文件
```plain
nginx -t
```
如果报错说明proxy_connect模块没有安装好
## 启动nginx并验证结果

```plain
# 启动
nginx
# 验证代理配置，端口号和listen保持一致
curl https://www.baidu.com/ -v -x 127.0.0.1:1080
```
## 卸载

注：如果报错需要重装时考虑

```plain
rm -f -R /usr/local/nginx && rm -f /usr/local/sbin/nginx
```
## 设置浏览器代理

注意开启防火墙限制

![图片](https://github.com/huzi0007/Network/blob/main/pictures/vpn_build.png?raw=true)


可以打开百度搜索ip地址，如果外网地址就是代理服务器地址说明配置成功

## 后记

上述方法可以给工作带来不少便利，如果http不好用可以参考squid的博客。个人觉得squid更好用一些

 

