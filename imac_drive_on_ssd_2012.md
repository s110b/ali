---
title: "升级老款imac硬盘"
date: 2018-05-11T08:58:17+08:00
draft: true
categories: ["tec"]
tags: ["imac"]
---

> 2012年末的imac有点过份慢，用软件测了下磁盘性能，大约只有50M/s的写入速度。
> 由于之前在笔记本上更换ssd获得了很大的性能提升，所以设想把imac的也替换下。

# 准备工作：

## 确认电脑型号。

    2012年末 imac late
    imac内部只有一个sata位置，之前硬盘需要拆掉。
    imac之前硬盘有温控装置，换之后风扇会狂转，可以用软件控制。
    目前市面上支持sata的ssd硬盘速率大约550M/s

## 拆机方式在淘宝上打听了下需要400￥

    imac内部只有一个sata位置，之前硬盘需要拆掉。
    imac之前硬盘有温控装置，换之后风扇会狂转，可以用软件控制。
    目前市面上支持sata的ssd硬盘速率大约550M/s

## 外置硬盘方式。

    imac支持usb3，理论速度最大大约640M/s
    三星的t5外置ssd硬盘速度可以 550m/s
    imac支持外置硬盘装系统。

最后，亚马逊评论区，用这款ssd装mac系统的用户不少。
[亚马逊的评论有干货](https://www.amazon.com/Samsung-T3-Portable-SSD-MU-PT500B/product-reviews/B01AVF6UQQ/ref=cm_cr_othr_d_paging_btm_9?ie=UTF8&reviewerType=all_reviews&pageNumber=9)

---

## 确定可行，[jd下单](https://item.jd.com/15972330408.html)等待收货安装。

```php
> ask:
 If i use this drive on my 2012 imac with usb 3.0, will the transfer speeds be less than 540mb/s?

 Answer:

> The USB 3 is capable of handling up to 640 MBps, The Samsung T5 is listed with a read/write speed of up to 540 MBps. Check the specs of your 2012 iMac or check with an Apple Rep. The computer itself may be the limiting factor for any storage device you mate with it. I doubt you will be disappointed with the performa… see more
By WNH on November 18, 2017
Just tested it on my 2014 Mac Mini using Blackmagic app and got 424 MB/s write speed and 427 MB/s read speed. This in comparison to about 80MB/s on WD Passport external HD.
 quote here...
```

## 安装系统步骤

1. [How can I install OS X on a new ssd](https://www.ifixit.com/Answers/View/264094/How+can+I+install+OS+X+on+a+new+ssd)
2. [如何给Mac电脑做备份](https://zh.wikihow.com/%E7%BB%99Mac%E7%94%B5%E8%84%91%E5%81%9A%E5%A4%87%E4%BB%BD)
3. [mac 国外论坛](https://forums.macrumors.com/threads/is-anyone-here-booting-the-os-from-a-t3-t5-external-ssd.2091818/)

旧硬盘速度

![_E5_B1_8F_E5_B9_95_E5_BF_AB_E7_85_A7_2018-05-11__E4_B8_8A_E5_8D_888.04.23](https://yuncodeweb.oss-cn-hangzhou.aliyuncs.com/uploads/xiquwugou/source/e86f0fad038394f05c178080f1578faf/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7_2018-05-11_%E4%B8%8A%E5%8D%888.04.23.png)

新硬盘速度
![_E5_B1_8F_E5_B9_95_E5_BF_AB_E7_85_A7_2018-05-14__E4_B8_8A_E5_8D_888.53.57](https://yuncodeweb.oss-cn-hangzhou.aliyuncs.com/uploads/xiquwugou/source/6e2bf313b4c5f371e26461e569308bbc/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7_2018-05-14_%E4%B8%8A%E5%8D%888.53.57.png)
