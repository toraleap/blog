---
layout: post
title: OpenWrt 刷机笔记
category: tech
---

一直听闻OpenWrt的强大，浏览了一下其支持的设备突然发现手中的TP-Link TL-MR11U赫然在支持列表内，这忍不住就想试一把鲜。没想到OpenWrt水这么深，准备工作没做充分，刷得容易，配置时却四面碰壁。折腾了许久也算是基本配置好了，考虑到接下来可能要买一款能刷wrt系列固件的千兆双频路由，且将正确的配置过程做一笔记分享吧。

首先要说的是，OpenWrt刚刷上时可是没有Web管理界面，没有开启Wifi的，所以没准备好网线，或是没有连通外网的，建议还是先别刷了，放那真的像块砖。

TL-MR11U这款路由刷机倒是简单，首先从OpenWrt官网<http://wiki.openwrt.org/toh/start>下载到对应路由的固件文件，然后用原生固件的Web升级页面直接就能刷入了。相信你不会在刷机过程中关闭电源的。

接下来就是配置的步骤了：

1.  准备好Telnet和SSH工具。Windows7用户去控制面板→程序与功能→打开或关闭Windows功能里，勾选Telnet客户端；然后下载个Putty作为SSH客户端备用。注意Putty汉化版曾爆出过木马风波，因此尽量不要下载汉化版，推荐到官网<http://www.putty.org/>下载原版。
2.  有线连接到刷好的路由，可以通过其他路由器间接连接。OpenWrt刷好后的默认IP是192.168.1.1，因此保证自己的电脑也在同一网段才能互访。
3.  进入命令提示符cmd，输入
>	telnet 192.168.1.1
连接到路由器，应该能看到欢迎画面。

4.  为root帐户设置密码，以后将使用SSH客户端以此密码登入：
>	root@openwrt:~$ passwd
>	Changing password for root
>	New password:输入要设置的新密码，不会有任何显示
>	Retype password:再次输入要设置的新密码，不会有任何显示
>	Password for root changed by root
>	root@openwrt:~$ exit
5.  设置好root密码后，telnet通道会自动被禁用，接下来换Putty进行连接。运行Putty，在Host Name处填入192.168.1.1，点击Open：
>	login as: root
>	root@192.168.1.1's password: 输入刚才设置的密码
>
>	BusyBox v1.19.4 (2012-08-01 13:14:26 UTC) built-in shell (ash)
>	Enter 'help' for a list of built-in commands.
>
>	  _______                     ________        __
>	 |       |.-----.-----.-----.|  |  |  |.----.|  |_
>	 |   -   ||  _  |  -__|     ||  |  |  ||   _||   _|
>	 |_______||   __|_____|__|__||________||__|  |____|
>	          |__| W I R E L E S S   F R E E D O M
>	 -----------------------------------------------------
>	 ATTITUDE ADJUSTMENT (Bleeding Edge, r32930)
>	 -----------------------------------------------------
>	  * 1/4 oz Vodka      Pour all ingredients into mixing
>	  * 1/4 oz Gin        tin with ice, strain into glass.
>	  * 1/4 oz Amaretto
>	  * 1/4 oz Triple sec
>	  * 1/4 oz Peach schnapps
>	  * 1/4 oz Sour mix
>	  * 1 splash Cranberry juice
>	 -----------------------------------------------------
>	root@OpenWrt:~#
6.  我们的目标是激活Web管理界面，对于新手只要这个有了一切都好办。原版OpenWrt固件是没有包含Web管理界面的组件luci的，因此我们需要先做适当配置让路由器能够连接网络，然后通过网络下载安装：
对于PPPoE拨号上网的用户：
设置WAN口类型为PPPoE
>	uci set network.wan.proto=pppoe
设置PPPoE用户名、密码、MTU信息
>	uci set network.wan.username=拨号用户名
>	uci set network.wan.password=拨号密码
>	uci set network.wan.mtu=1492
对于使用上级路由的用户：（以下IP根据具体情况更改）
>	uci set network.lan.ipaddr=192.168.1.199
>	uci set network.lan.netmask=192.168.1.199
>	uci set network.lan.gateway=192.168.1.1
>	uci set network.lan.dns=192.168.1.1
接下来，应用之前所做的修改
>	uci commit
让路由器使用新配置重新连接网络
>	/etc/init.d/network restart
7.  由于我们修改了路由的IP地址，Putty可能需要重新连接，方法见步骤5。连接完成后，接下来：
更新软件包列表
>	opkg update
安装luci 图形化Web界面
>	opkg install luci
启用http服务
>	/etc/init.d/uhttpd enable
>	/etc/init.d/uhttpd start
8.  在浏览器中访问路由的IP，图形化界面出来了！虽然是英文界面，也比在命令行下摸黑要好得多了吧。基本的无线功能可以轻松开启了，然后继续体验OpenWrt的强大功能和高自由度吧。