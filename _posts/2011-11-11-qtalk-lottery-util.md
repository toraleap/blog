---
layout: post
title: QTalk 特权中心抽奖机器
category: tech
---

值此百年一遇的光棍节，首先祝各位光棍（包括本人）节日快乐，截图留念于此。

![111111111111](http://blog.toraleap.com/wp-content/uploads/2011/11/111111111111.png)

QTalk最近推出了一系列的抽奖活动，经验卡金币卡什么的好抽不在话下，但是复仇焰魂和即将开放的掘墓人必定是炙手可热啊，每小时100的库存量一直以来都是见光死，怎么才能拼手速抢到呢？

![qqtalk01](http://blog.toraleap.com/wp-content/uploads/2011/11/qqtalk01.png)

不难发现抽奖窗口是个网页，为了进一步分析，我们先用HTTP抓包工具Fiddler，顺利的获得此页面地址： 
<http://qtalk.qq.com/client_act/act_list.shtml>

在浏览器中打开，提示用QQ号码登录。想要用软件模拟QQ的登录过程估计挺麻烦的，为了简单起见，就利用浏览器好了，本次抽奖机器就决定基于Chrome做成一个插件。

既然是抽奖机器，没有验证码没有回答问题，最简单的工作原理就是不停的刷这个抽奖页面。立即兑换按钮是灰色的，这不能阻止咱深究的意志，查看源代码，找出立即兑换这个按钮对应的代码，有如下关键词句：  
>	document.location = ‘/php/act/get_gift/index/’+actid+’?rd=’+Math.random(); 

换句话说，复仇焰魂的活动编号是50，兑换网址自然如下： 
>	http://qtalk.qq.com/php/act/get_gift/index/50

![qqtalk02](http://blog.toraleap.com/wp-content/uploads/2011/11/qqtalk02.png)

在浏览器中打开，出现兑换失败的提示，这是当然的了，毕竟还没到点嘛。看样子，我们的抽奖机器呢，就指使浏览器不停地刷新这个页面就行了。所用脚本相当简单，全部的JavaScript代码就两行，保存为refresh.js：

	actid = document.location.pathname.match(/index\/(\d+)/)[1]; 
	document.location=’http://qtalk.qq.com/php/act/get_gift/index/’ + actid + ‘?rd=’+Math.random();

先使用正则表达式从网址中提取出活动编号，然后使页面转向到指定的地址。网址后加的随机数是否必要暂时未知，既然是模拟抽奖，就应该尽可能做到和正常操作完全一致是不是？

然后下面就是Chrome插件的声明文件manifest.json，这个还显得长了不少。

	{ 
	   "content_scripts": [ { 
	      "all_frames": true, 
	      "js": [ "refresh.js" ], 
	      "matches": [ "http://qtalk.qq.com/php/act/get_gift/index/*" ] 
	   } ], 
	   "description": "请先访问QQTalk活动列表 http://qtalk.qq.com/client_act/act_list.shtml，并用QQ号登录，查看想要参加的活动编号，然后进入地址 http://qtalk.qq.com/php/act/get_gift/index/50，替换尾数为活动编号。", 
	   "icons": { 
	      "64": "icon64.png", 
	      "16": "icon16.png", 
	      "48": "icon48.png" 
	   }, 
	   "name": "QQTalk活动抽奖插件", 
	   "permissions": [ "http://qtalk.qq.com/php/act/get_gift/index/*" ], 
	   "version": "1.0.0" 
	}

关于Chrome插件的制作方法这里就不谈了，语法请自行Google，完成manifest.json文件，准备好必要的图标后，便可以开启Chrome的开发人员模式，将插件加入到扩展程序列表。

![qqtalk03](http://blog.toraleap.com/wp-content/uploads/2011/11/qqtalk03.png)

用法如上，首先用Chrome进活动列表页面登录，查看活动编号；然后替换抽奖地址尾部的活动编号，打开抽奖页面，该页面会不断的自动刷新，看清楚时间快到点时就打开N个标签疯狂的刷，超过时间后关闭标签再去查看战果吧。

由于只有两行代码，插件很粗糙，使用起来也不怎么方便。但是，最重要的一点，能刷到光棍节礼物的抽奖机器才是好机器，哈哈！当然僧多粥少，别指望一用就能抽到就是啦。

![qqtalk04](http://blog.toraleap.com/wp-content/uploads/2011/11/qqtalk04.png)

[Chrome插件下载](http://res.toraleap.com/attachments/qqtalk_lottery.crx) [源码下载](http://res.toraleap.com/attachments/qqtalk_lottery.zip)