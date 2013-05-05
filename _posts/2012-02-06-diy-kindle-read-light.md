---
layout: post
title: 山寨Kindle自供电阅读灯
category: default
---

去年10月18日入手一部Kindle4 Nontouch，看书看漫画效果上是没得说，但是E-ink屏幕自身不发光，晚上阅读就伤脑筋了。到淘宝上查查，亚马逊原装带灯皮套直接是四百大洋起；山寨灯要么靠充电，要么装电池，就没有一个用到K4自带的取电接口，多不方便啊。于是，DIY一个Kindle自供电阅读灯的想法自然而然的萌生了。

![kindle01](http://blog.toraleap.com/wp-content/uploads/2012/02/kindle01.jpg)  
亚马逊原装带灯皮套

想法来得快，可惜手边没有材料啊。市面上USB接口的小灯挺多的，最简单的方法，把Kindle的取电接口转换成USB输出，不就可以使用现成的灯了么？本来应该找个更迷你的小灯，无奈手边就这么一个，先将就用着吧。

![kindle02](http://blog.toraleap.com/wp-content/uploads/2012/02/kindle02.jpg)  
USB供电LED灯

接下来需要一个USB母口，四下寻觅无果，只好破坏一条USB延长线了，死前留念。

![kindle03](http://blog.toraleap.com/wp-content/uploads/2012/02/kindle03.jpg)  
USB延长线临终留影，我们只需要那个母口

接下来看看Kindle4的取电接口，说白了就是两个金属触点而已。万用表测得其电压为4V，左边正极，虽然低于USB标准电压5V，驱动LED还是绰绰有余的。

![kindle04](http://blog.toraleap.com/wp-content/uploads/2012/02/kindle04.jpg)  
Kindle4取电接口，电压4V，左正右负

正式开始。在分解延长线之前，我们先确定一下USB母口的供电引脚的位置。话说一手拿相机一手握两支笔，还要捏住待测物体，这还真不是一般人能做到的，只好把迷你虎钳祭出来了。将延长线母头固定好，另一端接入计算机，万用表测量结果顶端为正极，底端为负极，电压5.18V。

![kindle05](http://blog.toraleap.com/wp-content/uploads/2012/02/kindle05.jpg)  
USB母口引脚测试

然后咔嚓一剪刀，残忍的现场就不直播了。

![kindle06](http://blog.toraleap.com/wp-content/uploads/2012/02/kindle06.jpg)  
USB延长线分解

因为还不确定需要多长，所以留了挺长一截；剩下的公口也给留了一段线，以后还可以用作材料。去掉一小段胶皮，露出四根导线。查询USB线缆颜色定义，得知红白绿黑从左到右依次为Power、Data+、Data-、GND。

![kindle07](http://blog.toraleap.com/wp-content/uploads/2012/02/kindle07.jpg)  
USB线缆微距照片

白绿两条数据线这里不需要，直接剪掉。把红黑两条线前端胶皮剥掉，为了帮助正确定位，找了一块硬纸片，在Kindle触点对应位置穿孔，将红黑两条导线穿过，记住Kindle的左边触点是正极，对应红色导线。

![kindle08](http://blog.toraleap.com/wp-content/uploads/2012/02/kindle08.jpg)  
辅助定位用硬纸片

在纸片的另一面，想办法在不影响导电的情况下将裸露导线固定住。使用烙铁加焊锡是一种办法，本人焊功不佳见笑了。

![kindle09](http://blog.toraleap.com/wp-content/uploads/2012/02/kindle09.jpg)  
焊接好的导线触点

转接线缆雏形完成，通电测试！亮起来吧！

![kindle10](http://blog.toraleap.com/wp-content/uploads/2012/02/kindle10.jpg)  
BINGO!

关闭环境光，4V电压仍然能够保证LED有充足的亮度，可以满足阅读需求。

![kindle11](http://blog.toraleap.com/wp-content/uploads/2012/02/kindle11.jpg)  
LED亮度实验

接下来看Kindle上的阅读效果，关闭环境光实拍，挺不错的。

![kindle12](http://blog.toraleap.com/wp-content/uploads/2012/02/kindle12.jpg)  
阅读效果实拍

再来一张，这次没有直接拍到光源，反映了更真实的视觉效果。

![kindle13](http://blog.toraleap.com/wp-content/uploads/2012/02/kindle13.jpg)  
阅读效果实拍

哈哈概念作这么就算做成了，用胶带小心缠好，来看看矮丑穷的成品~

![kindle14](http://blog.toraleap.com/wp-content/uploads/2012/02/kindle14.jpg)  
已经不能再简陋了的成品展示

话说这个怎么用？总不可能在看书的时候，还得一直小心用手按着吧？强行固定在Kindle背面肯定不是个好主意，我现在的想法，还是得买个硅胶套，把这玩意整合到硅胶套上，带灯胶套官方可是没有的哦，可不能说山寨啊~