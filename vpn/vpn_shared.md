# ubuntu共享widows端VPN
## 一、背景：
  菜鸡一枚，Ubuntu Linux无法科学上网，多番尝试跑通了，做下记录，以供自己日后查看。
## 二、以下为重点，部分不同，主要抄袭来源：
>[VMware上安装的Ubuntu Linux共享Windows主机VPN连接外网](https://arctee.cn/686.html)

>>>![](https://github.com/huzi0007/Network/blob/main/pictures/vpn01.png?raw=true)

>为免丢失，所以复制粘贴，并修改如下
### 1. 现象 
>在 VMware 下安装 Ubuntu 时，网络选择的是 nat 模式，就是指虚拟机通过主机去访问外部网络。安装好后虚拟机可以访问国内网络，但是无法访问 Google、youtube 等需要翻墙才能使用的外网。但是此时我的 Windows 主机上是开了 VPN 的，主机可以科学上网，而虚拟机无法共享 VPN 连接外网。
### 2. 原因
>虚拟机上的私有网络配置没有设置，即网络代理(Network Proxy)未配置
### 3. 解决办法
>以我使用的 VPN 客户端来详细介绍下如何配置网络代理：
#### （1）打开“更改适配器设置”获取虚拟机 VMnet8 虚拟网卡的 ip
>这里注意下：因为虚拟机网络配置选择的是 nat 模式，所以这里查看 VMnet8 虚拟网卡的 IP，如果选择的是 桥接 模式，则查看宿主机正在使用的网络，比如主机是通过 WIFI 上网的话，就 查看 WLAN 这个网卡的 IP
​控制面板\网络和 Internet\网络和共享中心——>更改适配器设置
>
>>>![](https://github.com/huzi0007/Network/blob/main/pictures/vpn02.png?raw=true)
>
>找到 VMnet8，并双击
>
>>>![](https://github.com/huzi0007/Network/blob/main/pictures/vpn03.png?raw=true)
>>>![](https://github.com/huzi0007/Network/blob/main/pictures/vpn04.png?raw=true)

>单机详细信息，获取IPv4地址信息：192.168.127.1
>>>![](https://github.com/huzi0007/Network/blob/main/pictures/vpn05.png?raw=true)

#### （2）打开 VPN 客户端，在设置里找到代理端口号：
>a. 右击VPN客户端(纸飞机)，选择选项设置，获取本地端口(我的是1087)
>>>![](https://github.com/huzi0007/Network/blob/main/pictures/vpn06.png?raw=true)
>>>![](https://github.com/huzi0007/Network/blob/main/pictures/vpn07.png?raw=true)
>b. 双击VPN(纸飞机)，获取服务器端口(我的是1351)
>>>![](https://github.com/huzi0007/Network/blob/main/pictures/vpn08.png?raw=true)
#### （3）对 Ubuntu 系统进行VPN网络代理设置
>a. 点击右上角网络连接，点击设置
>>>![](https://github.com/huzi0007/Network/blob/main/pictures/vpn09.png?raw=true)
>b. 选择VPN下网络代理，点击设置齿轮
>>>![](https://github.com/huzi0007/Network/blob/main/pictures/vpn10.png?raw=true)
>c. 网络代理调成手动，并一次写入前面步骤获取的信息：
>>IPv4地址信息(192.168.1
>>127.1)、本地端口信息(1087)、服务器端口信息(1351)即可
>>>![](https://github.com/huzi0007/Network/blob/main/pictures/vpn11.png?raw=true)
#### （4）现在再去打开firefox浏览器访问youtubu试试吧！
>>>![](https://github.com/huzi0007/Network/blob/main/pictures/vpn12.png?raw=true)
