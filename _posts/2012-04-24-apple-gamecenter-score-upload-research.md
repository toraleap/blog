---
layout: post
title: Apple GameCenter 排行数据上传分析
category: tech
---

牛排入手已一月，来一个与牛排有关的研究吧。说到GameCenter，就是苹果提供的一个游戏平台，提供好友、成就、排行相关的功能。就算游戏水平不济，想不想在排行榜上占据一席之地呢？

首先，大部分游戏是单机可玩的，意味着个人数据是在联网的时候从本地上传，未越狱机器不能随意修改程序，修改游戏的数据的方法也各有不同，但是由于GameCenter的接口理论上是固定的，监控并修改网络流量相对来说应该较为简单。

我们使用电脑建立代理服务器的方式，监控来自iPad的流量。在这之前有些准备工作要做：

1.  允许Fiddler处理来自远程计算机的连接 
	Fiddler Options –> Connections –> 勾选 Allow remote computers to connect

2.  启用HTTPS抓包分析 
	Fiddler Options –> HTTPS –> 勾选 Capture HTTPS CONNECTs 及 Decrypt HTTPS traffic

3.  安装iOS专用证书生成器 
	根据官方FAQ，如需对iOS的HTTPS流量抓包，默认的证书生成器可能不兼容，需要另下载一个专用的证书生成器。   
	[点击这里下载](http://www.fiddler2.com/dl/FiddlerCertMaker.exe)，安装完毕后重启 Fiddler。

4.  将根证书导入iPad 
	由于FIddler的根证书不在iOS的信任列表中，需要添加此根证书到iPad，点击 Fiddler Options –> HTTPS –> Export Root Certificate to Desktop 按钮将根证书保存到桌面，再使用任意HTTP服务器软件(如HFS)共享此文件，使用iPad内置浏览器Safari访问证书文件的地址，按照弹出的提示安装证书。此步骤可能较为繁杂。

5.  设置iPad的代理服务器 
	要求iOS设备和电脑在同一个无线局域网内，点击所用Wifi连接的右侧蓝色标记，在下面设置手动HTTP代理，填写服务器为电脑的IP地址，端口号默认为8888。

准备工作完成后，iPad上的HTTPS数据流量就可以通过电脑端的Fiddler截取并操作了。

首先启动一个支持GameCenter的游戏，观察截获的数据包。这里以ready steady bang为例，一个考验反应速度的小游戏。

![gamecenter01](http://blog.toraleap.com/wp-content/uploads/2012/04/gamecenter01.png)

抓到3条请求，从URL直接就能看出我们要关心的那一条：submitScores。顺便说明，由于我这台牛排已经被注册为开发机，因此连接的是苹果的沙盒服务器。

![gamecenter02](http://blog.toraleap.com/wp-content/uploads/2012/04/gamecenter02.png)

图示为该条请求的详情，发送的是xml信息，显而易见score-value的值为要上传的成绩，这是个考验反应速度的游戏，单位为毫秒，数值越小，在排行榜上的成绩就越高。timestamp的值为获得该成绩的时间，我们暂不关心这一项。226毫秒的成绩，距目前排行榜第一名92毫秒还差得远呢。

要截获修改这条数据，首先给Fiddler设置一个断点。在Fiddler左下角命令区输入bpu submitScores，回车

![gamecenter03](http://blog.toraleap.com/wp-content/uploads/2012/04/gamecenter03.png)

bpu的含义是Breakpoint on URL，当发现URL包含其后的字符串时中断请求并等待用户处理。然后我们强制结束游戏并重新启动，这次上传请求被中断下来等待处理：

![gamecenter04](http://blog.toraleap.com/wp-content/uploads/2012/04/gamecenter04.png)

包含submitScores的请求被成功中断。选中该项请求，我们可以在其发送到服务器之前修改发送的内容。

![gamecenter05](http://blog.toraleap.com/wp-content/uploads/2012/04/gamecenter05.png)

这里我把数值修改为了76，这已经是第一了，也不能太过分吧。我们不需要修改服务器的返回响应，直接点击 Run to Completion 完成这一请求。

最后，让我们进入GameCenter，查看刚才修改的战果吧！

![gamecenter06](http://blog.toraleap.com/wp-content/uploads/2012/04/gamecenter06.png)

修改成就的方法稍复杂，需要知道各个成就的ID字符串，截获并修改submitAchievements请求，除此外原理都是一样的。顺便提醒，如发现iPad网络连接不正常，别着急，记得关闭代理服务器设置哦！